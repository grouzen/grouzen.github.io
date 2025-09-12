---
layout: post
title: Building Microjournal Rev.6
tags: hardware diy microjournal electronics writerdeck esp32
slug: building-microjournal-rev6
---

![Top image](/assets/images/building-microjournal-rev6/PXL_20250912_150821937.jpg)

I've built the [Microjournal](https://github.com/unkyulee/micro-journal) and want to share the joy of going through the process with the rest of the fans and whoever is still thinking about doing it. My story started with one simple idea: I wanted to build something physical, some object in the real world with my own hands.

# Introduction

I've spent my school years crafting many different things. I was lucky enough to have an opportunity to attend free after-school classes where we were taught to build models of ships and planes. This was an echo of the Soviet era, when they were trying to incentivize young people to learn the necessary skills needed to build the country.

I think this played a big role in who I became - a person who likes building stuff. Despite being derailed to programming after I bought my first computer when I was 16, I still want to create something material. It gives a different feeling compared to completing yet another piece of business logic at work or a small open source pet project.

I can't remember how and where I came across the Microjournal, but I got that feeling that I want to have one almost instantly. From a DIY perspective, the device combines the best of multiple worlds I like to fiddle with at the same time:
- physical (with a need to 3D print a shell)
- electronics, computers, keyboards, etc., that you need to wire together
- and, of course, software.

Luckily, the author and maker of it runs an [online shop](https://www.tindie.com/stores/unkyulee/) where you can choose from various options tailored to different types of users. You can buy either an almost-ready-to-use device or a lovely package of individual parts that need to be assembled almost from scratch. I've taken the latter option, of course. For even more tech-savvy enthusiasts, there is always the option to follow a build guide available on GitHub for making it completely on their own. It requires having a 3D printer at home, which I don't have yet.

# Building process

I think it is time to get to the main point of this article. How it feels to build the [Microjournal Rev.6 DIY Kit](https://www.tindie.com/products/unkyulee/diy-kit-micro-journal-rev6/).

Given my personal life story, it may be obvious that I know how to handle a soldering iron and a screwdriver. So the entire process was quite smooth for me at every stage.

## Keyboard PCB

I bought a version where you need to assemble a keyboard by yourself. It includes soldering resistors and hot-swappable sockets for key switches onto a provided keyboard PCB. It was pretty chill and meditative experience that took some time, as it needed to be repeated 48 times for each key.

![](/assets/images/building-microjournal-rev6/PXL_20250503_164959363.jpg)

![](/assets/images/building-microjournal-rev6/PXL_20250503_185014931.jpg)

![](/assets/images/building-microjournal-rev6/PXL_20250503_190651363.NIGHT.jpg)

![](/assets/images/building-microjournal-rev6/PXL_20250510_202621344.jpg)

## Connect the keyboard to ESP32

Then you need to connect the keyboard to the ESP32 board. It is being done by soldering 16 wires. This number comes from the number of rows added to the number of columns which is 4x12. It gives you exactly 48 keys. 

![](/assets/images/building-microjournal-rev6/PXL_20250510_213122379.MP.jpg)

![](/assets/images/building-microjournal-rev6/PXL_20250510_222243993.jpg)

![](/assets/images/building-microjournal-rev6/PXL_20250510_222651616.jpg)

![](/assets/images/building-microjournal-rev6/PXL_20250510_222714906.jpg)

## Screen and SD card slot

The next step is to wire up the screen. I would say it was one of the most annoying parts of the build process due to slightly convoluted explanations in the official build guide. So I hope this article will help other people go through it. 

![](/assets/images/building-microjournal-rev6/PXL_20250827_160258868.jpg)

## Power supply

Now it's left to connect a battery charger module, power switch, and battery holder to the ESP32.

![](/assets/images/building-microjournal-rev6/PXL_20250827_160118434.jpg)

![](/assets/images/building-microjournal-rev6/PXL_20250827_160226022.jpg)

![](/assets/images/building-microjournal-rev6/PXL_20250827_162221161.jpg)

Voilà! We're ready to run it for the first time!

## First run

As I mentioned already, the entire process was quite smooth for me. It worked perfectly on the first attempt. 

![](/assets/images/building-microjournal-rev6/PXL_20250827_162742787.jpg)

![](/assets/images/building-microjournal-rev6/PXL_20250827_170046820.jpg)

## Key switches and keycaps

I decided to hold off on buying key switches and keycaps until I make sure the device is functioning properly.

Somehow, I came across [Akko V3 Cream Black Pro](https://akkogear.eu/products/v3-cream-black-pro-switch-45pcs) switches and bought them at the official web store. I had to get two items instead of one since I need 48 switches when each item contains 45 pieces. But this is not a big deal considering the price.

Surprisingly, new keycaps may easily cost you more than $100. Fortunately, the second-hand market is flourishing, and you can find pretty neat stuff for a small amount of money.

I found my new beautiful Japanese keycaps on Vinted for around $20. They are being delivered for the time being, so I'm using my old gaming keyboard (with German keymap) as a keycap donor for now.

![](/assets/images/building-microjournal-rev6/PXL_20250912_150907932.jpg)

![](/assets/images/building-microjournal-rev6/PXL_20250912_150916146.jpg)

# Conclusion

I can recommend buying the DIY edition even for people who are not very familiar with electronics. You just need to watch a couple of videos on YouTube on how to solder two wires together. So it can be a good opportunity to spend some quality time together with your family building something cool.

Moreover, you may become interested in assembling more available open-source hardware after this experience. A good aspect of Microjournal rev. 6 is that it is based on ESP32, which is quite powerful and popular microcontroller nowadays. You can learn how to write programs for it by either modifying the original firmware or writing your own.

This article was written on Microjournal, btw!
