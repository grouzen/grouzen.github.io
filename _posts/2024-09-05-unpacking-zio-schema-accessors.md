---
layout: post
title: Unpacking ZIO Schema's Accessors
tags: scala macros zio schema parquet
slug: unpacking-zio-schema-accessors
---

![The image](../assets/images/unpacking-zio-schema-accessors-title.webp)


> [ZIO Schema](https://github.com/zio/zio-schema) is a [ZIO](https://zio.dev/)-based library for modeling the schema of data structures as first-class values.

The big part of it is an automated, macro-based derivation of `zio.schema.Schema` from your type classes. It has been adapted by many libraries within the ZIO ecosystem, which makes it a good candidate to include in your next ZIO-powered library when it comes to codec derivation. What makes it even cooler is an underdocumented feature: Accessors. There is not much information available on the internet about this library, so I've decided to share some of my findings about it.

# ZIO Apache Parquet

I've recently been hacking on my new library, [ZIO Apache Parquet](https://github.com/grouzen/zio-apache-parquet). The main reason I decided to start building this library was a genuine interest in what the ZIO schema is capable of.

It is not a secret that it is essential requirement to be able to filter the data when you work with big data sets. From the very beginning I didn't like the idea of repeating the type-unsafe filter predicate API that the rest of the languages suffer from. I'm sure you know what I'm talking about. This clunky `col("foo") == "bar"` type of code that doesn't provide any guarantees. I wanted to get rid of it and forget about it forever, for the sake of all fathers of type theory. 

# ZIO SQL

It was my idée fixe for a while. I had been thinking about it in the background. Once I got some free time, after a brief research, I came across another library from the ZIO ecosystem - [ZIO SQL](https://zio.dev/zio-sql/). I noticed one interesting detail in how they manage accessing fields (columns) of the case classes (tables). You can find it right in their documentation: 
- [https://github.com/zio/zio-sql?tab=readme-ov-file#table-schema-decomposition](https://github.com/zio/zio-sql?tab=readme-ov-file#table-schema-decomposition)
- [https://zio.dev/zio-sql/deep-dive/#table-description](https://zio.dev/zio-sql/deep-dive/#table-description)

That was an "aha" moment! Of course, I've jumped straight ahead into the documentation of "ZIO Schema"... and found nothing about that. The only mention I found was on the [documentation page](https://zio.dev/zio-schema/derivations/optics-derivation#automatic-derivation-of-optics) about the `zio-schema-optics` module. Fortunately, there is a brilliant talk by [Jaroslav Regec](https://github.com/sviezypan) on YouTube ["Peeking Inside the Engine of ZIO SQL" by Jaroslav Regec](https://www.youtube.com/watch?v=Ezs7dMbbGlY) that sheds some light on this topic. The explanation of "Accessors API" and what you can achieve with it starts at [7:10](https://youtu.be/Ezs7dMbbGlY?t=430).

# Profunctor optics

You might say, "Nah, it's nothing new to see here." Look at any library that implements profunctor optics like [quicklens](https://github.com/softwaremill/quicklens) or [Monocle](https://github.com/optics-dev/Monocle). 
These libraries do provide a set of predefined combinators that allow you to access and manipulate the fields of case classes. However, there's an important distinction here: they work with actual data values within case class instances. In other words, they're focused on accessing and modifying the contents of existing objects.

In our case, the case classes represent not data, but rather schemas.
We need to be able to build an arbitrary DSL over the fields. In the case of ZIO SQL and ZIO Apache Parquet, the fields of a case class represent the columns of a table in the database and the columns of a Parquet dataset respectively. Moreover, we want to have a rich set of operators to be applied to the data contained in the columns.

No more talk, let's dive deep into this already.

# Accessor Builder

An entry point to it is `zio.schema.AccessorBuilder`:
```scala
trait AccessorBuilder {
  type Lens[F, S, A]
  type Prism[F, S, A]
  type Traversal[S, A]

  def makeLens[F, S, A](product: Schema.Record[S], term: Schema.Field[S, A]): Lens[F, S, A]

  def makePrism[F, S, A](sum: Schema.Enum[S], term: Schema.Case[S, A]): Prism[F, S, A]

  def makeTraversal[S, A](collection: Schema.Collection[S, A], element: Schema[A]): Traversal[S, A]
  
}
```

As you may see, it is the same old profunctor optics, but you can define your own implementation of `Lens`, `Prism`, and `Traversal`. That's what changes everything.
It means we are not limited by the regular optics combinators such as: `modify`, `set`, and `get`. 

For this, we need to override `type Lens` and `def makeLens` only:
```scala
final class ExprAccessorBuilder(typeTags: Map[String, TypeTag[?]]) extends AccessorBuilder {

  override type Lens[F, S, A] = Column.Named[A, F]

  override def makeLens[F, S, A](product: Schema.Record[S], term: Schema.Field[S, A]): Column.Named[A, F] = {
    val name             = term.name.toString
    implicit val typeTag = typeTags(name).asInstanceOf[TypeTag[A]]

    Column.Named[A, F](name)
  }

}
```

This piece is taken from my [ZIO Apache Parquet](https://github.com/grouzen/zio-apache-parquet/blob/main/modules/core/src/main/scala/me/mnedokushev/zio/apache/parquet/core/filter/ExprAccessorBuilder.scala) library, where `Lens` is `Column.Named`, which represents a Parquet column that supports standard comparison operators such as `>`, `<=`, `==`, and so on.
`makeLens` is being called for each field of a case class. This way, we build a complete set of columns of an arbitrary `Schema.Record` that represents the case classes of arity N.

All this happens inside a special method called `Schema.makeAccessors`:
```scala
def makeAccessors(b: AccessorBuilder): Accessors[b.Lens, b.Prism, b.Traversal]
```

where `Accessors` is an abstract type member:
```scala
type Accessors[Lens[_, _, _], Prism[_, _, _], Traversal[_, _]]
```

These two are being overridden for each subtype of `zio.Schema`, including `zio.Schema.Record`. Let's have a look at the `CaseClass2` for the sake of example:
```scala
sealed trait CaseClass2[A1, A2, Z] extends Record[Z] {

  type Accessors[Lens[_, _, _], Prism[_, _, _], Traversal[_, _]] =
    (Lens[Field1, Z, A1], Lens[Field2, Z, A2])

  override def makeAccessors(
      b: AccessorBuilder
    ): (b.Lens[Field1, Z, A1], b.Lens[Field2, Z, A2]) =
      (b.makeLens(self, field1), b.makeLens(self, field2))
      
}
```

The last step is calling the `makeAccessors` methods passing in our own implementation of `AccessorBuilder`:
```scala
val accessorBuilder = 
  new ExprAccessorBuilder(typeTag.asInstanceOf[TypeTag.Record[A]].columns)
  
schema.makeAccessors(accessorBuilder)
```

The result has the type:
```scala
schema.Accessors[accessorBuilder.Lens, accessorBuilder.Prism, accessorBuilder.Traversal]
```

For example, let's define a very simple case class: 
```scala
case class MyRecord(a: Int, b: String)
```

Let's say, we derived a `zio.Schema` for it. The result of calling `schema.makeAccessors(accessorBuilder)` will be:
```scala
(Column.Named[Int, "a"], Column.Named[String, "b"])
```

What are those "a" and "b" literal singleton types? They are well-known "phantom types". It may be very useful if you need some extra information available only during compile time in your macros. I'll leave it to be explored in one of my next blog posts.

# Conclusion

Now we can clearly see that we changed the representation of `MyRecord.a` field. Instead of value of type `Int` it became `Column.Named[Int, "a"]` that's obviously not a value anymore but some sort of a schema. This shift in perspective opens up exciting possibilities for crafting a domain-specific language (DSL) tailored to your needs.
