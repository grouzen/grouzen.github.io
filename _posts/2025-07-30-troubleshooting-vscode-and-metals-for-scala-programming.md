---
layout: post
title: Troubleshooting VS Code and Metals for Scala programming
tags: scala scala3 ide vscode metals tools
slug: troubleshooting-vscode-and-metals-for-scala-programming
---

![The image](/assets/images/troubleshooting-vscode-and-metals-for-scala-programming-title.png)

# History of Scala IDE

I've been a Scala developer for almost ten years now – a journey that started around 2013 with a popular Coursera course by M. Odersky. My first Scala IDE was Emacs + [Ensime](https://ensime.github.io) (because I couldn't figure out how to quit from Vim, of course). The support for Scala was added to IntelliJ IDEA in April 2023 (according to my locally running instance of Gemma3n). My journey with it began right after joining Megogo for my first Scala job in 2016, and it's been a consistent preference ever since. Then...

Then, the first public release of [Metals](https://scalameta.org/metals/) happened in May 2018. I don't recall the exact moment I became aware of it, but I was intrigued from the start. I was so hyped to use it, even though it was somewhat clunky and lacked a good amount of necessary features. An Emacs user is unlikely to be scared by this, right? I kept trying it through the next couple of years, but found myself drawn back to IntelliJ IDEA. 

The developers of Metals have been making very good progress over the years. However, it has remained somewhat unsuitable for someone who needs to ship real business value. But after revisiting it late in 2023, I was genuinely impressed! It finally clicked and I was hoping to say goodbye to IDEA!

# Rise of Metals

This time it was different. It wasn't hanging randomly on start up. The basic intellisense and "go to definition" functionality worked perfectly. Debugger, the thing I've rarely used in IDEA, just worked! 

During that period, I was involved in project development for several initiatives at my workplace and continued developing personal libraries ([ZIO Apache Parquet](https://github.com/grouzen/zio-apache-parquet) and [ZIO Apache Arrow](https://github.com/grouzen/zio-apache-arrow)), which were written in Scala 3, either fully or partially. Instant feedback from the compiler and faster code completion are game-changers. 

But nothing is perfect. Every even slightly complex tool breaks from time to time. 

# Invalidate Caches

Yes, sometimes it gets stuck in a loop or dies suddenly. This doesn't happen as often nowadays, and it's very easy to fix, so I'm not going to go into more details about this.

The thing that happens quite often to me is that IntelliSense can cease to function. I've discovered a reliable two-step workaround. Implementing both steps consistently resolves the issue for me:

1. Metals > Clean and restart build server > Import build (optional)
2. Remove `.bloop`, `.bsp`, and `.metals` directories inside your project's directory
3. (Optional) Close the VS Code and kill the [Bloop](https://scalacenter.github.io/bloop/) Java process that keeps running even after all the Metals instances were shut down.

Additionally, you can check the Metals logs in the Output panel. This can be useful if you misconfigure your Java/Bloop/SBT setup initially.

# Solved in Metals

Why I personally have been using Metals all this time and never looked back at IDEA again? 

The reasons are:

🔄 Speed of compiler feedback and IntelliSense.
🔥 Working debugger.
💡 Brilliant Scala 2 & 3 support, including macros.
📖 Java-based desktop software (including IDE) is a mistake!