---
published: true
layout: post
date: 2014-06-14T12:31:19.000Z
summary: See what the different elements looks like. Your markdown has never looked better. I promise.
categories: jekyll pixyll
---

For our wedding a few years ago, my wife convinced me that we needed a photobooth. It'd be a great way to capture fun photos of our family and friends and also provide them with a keepsake.

Most people would have found a local supplier and it would've been another checked box in the wedding to-do list. In our case however, since we were having two celebrations, on two sides of the Atlantic, in two weeks, that wouldn't work. So I decided to build a photobooth.

The specifications for the photobooth almost wrote themselves:
- It had to be easy to use and require little supervision
- It had to pack down and fit into a duffle bag which weighed less than 70 lbs/32 kg (airline limitation)
- It had to print two "filmstrips", one for us to keep and one for the guests

The overall process would be something like this:
1. Guests trigger process by pushing a button
2. Four photos are taken and combined into filmstrip
3. Two filmstrips are printed onto a single 4x6 photo

I early on decided to use VB.NET to write the code that would tie all of these steps together. Grabbing images and sending them to a printer them seemed like tasks where I could leverage standard .NET functionality, plus I had written code in VB.NET at work for the past few years.


## Taking the photos
The first thing I had to tackle was to figure out a robust way of capturing the photos. My first thought was to use the [Canon SDK](http://www.usa.canon.com/cusa/consumer/standard_display/sdk_homepage) to trigger and acquire pictures from our Canon DSLR. After poking at it for a while, it became clear that it'd be a bit of an overkill.

So I started looking at using a webcam instead, since the images were printed in very small dimensions, I didn't really have to worry about resolution. With webcams being USB and "Plug-n-Play" I figured that it wouldn't too difficult to grab the images. After having tried numerous code snippets I had found online (none of which worked well) and decided that I didn't want to dive into the Windows API, I decided to go with OpenCV. OpenCV is admittedly a pretty big overkill for what I was trying to do, but the [Emgu .NET wrapper for OpenCV](http://www.emgu.com/wiki/index.php/Main_Page) was well-documented and easy to use. Combined with a [Logitech C920 webcam](http://www.amazon.com/Logitech-Webcam-Widescreen-Calling-Recording/dp/B006JH8T3S), I got pretty crisp looking photos into VB.NET. The C920 webcam also has a 1/4" mount which allowed me to easily attach it to a tripod.

![](https://cloud.githubusercontent.com/assets/12212438/7826201/1ed41e1c-03d4-11e5-8f0d-a76955b3eac6.JPG)


## Pushing the button
I needed a way for the guests to start the process and who doesn't like to push a [big red button](https://www.sparkfun.com/products/9181)? But how would I get the signal from the button press into my .NET application? I toyed with the idea of using an Adruino or the USB data acquistion that I laying around. Instead, I followed the [KISS principle](http://en.wikipedia.org/wiki/KISS_principle) and repurposed an old USB mouse for the task. A few wires and some soldering allowed me to connect the button's switch electrically to the _right mouse button_. I even could use the cardboard box the button had shipped in (with a fresh coat of paint) to house it all. A few pieces of foam made sure the mouse's circuit board stayed in place. But perhaps the biggest win was since .NET has built-in events for mouse clicks it was a breeze to trigger the photo process.
![](https://cloud.githubusercontent.com/assets/12212438/7826038/a0c243ac-03d1-11e5-9853-434b7feadc42.JPG)
_A big red button_

![](https://cloud.githubusercontent.com/assets/12212438/7826039/a0c6411e-03d1-11e5-83db-4de236cc9640.JPG)
_The big red button's switch soldered to the mouse board along with foam to keep the board (and more importantly my solderings with questionable quality) in place._

## Printing it all
I found a really compact photo printer ([HiTi P110S](http://www.hiti.com/us/Products/PhotoPrinter_P110S_Overview.asp)) that was reasonably priced (around $250) and printed 4x6 photos with solid quality. Paper and ink was sold in packages and I think each print ended up being 40 cents. To split the 4x6 into two 2x6 "strips" we just had a pair of scissors, very much in line with the KISS principle.

## The code
With the webcam, button and printer in place, writing the code was pretty straight forward. The windowed software was listening for a mouse click which triggered a process which would:

- Capture a frame from the webcam and simultaneously play a sound to indicate
- Assemble four frames into a film strip and assemble two film strips into a 4x6 format
- Send it to the printer

Once I got the three steps working, it was all put together into a simple WinForms application in VB.NET to make it easy to start it all up. But in reality, the GUI was never really used as the guests would just use the red button and the audible feedback to interact with the photobooth. I had some thoughts of using the screen of the laptop that ran everything to serve as a real-time viewport, but decided to just keep simple.

The code weighs in at under 200 lines and I think it shows the benefit of using Emgu CV and the .NET functionality to handle the webcam, image processing and printing. The source code of the software which I named FotoBoto, [is available on GitHub](https://github.com/ho1/FotoBoto). It's been tested with VS2015 Community Edition and to make it easy to get started, all dependencies are included in the /bin/Debug folder. Perhaps not best practice, but it was one of the things I struggled with when learning .NET and for a stressed out groom or bride, it's the last thing you want to have to worry about! 

## Making it portable

To ensure fun and crazy photos in the photobooth, I wanted four walls around the guests as they were having their photos taken (as opposed to just a backdrop). But bringing four walls across the Atlantic was not ideal. Some research showed that people had successfully used PVC tubing and sheets of fabric to build temporary structures ([forts!](http://www.instructables.com/id/Easy-Rebuildable-PVC-Fort/)).

![](https://cloud.githubusercontent.com/assets/12212438/7826036/a0b3c2b4-03d1-11e5-9d3d-28eb9a123f40.JPG)

For the fabric we went with a multicolored striped fabric as it looked more festive and would consume the ink in the printer more evenly!
![](https://cloud.githubusercontent.com/assets/12212438/7826037/a0c0c1bc-03d1-11e5-8622-8d9a9481309a.JPG)

Instead of using a single tube for the full height of the booth, I split it into three, with three additional joints. This allowed me to fit the tubes into the designated duffle bag, which fit within the airline's luggage limits.

![Everything but the printer in the bag!](https://cloud.githubusercontent.com/assets/12212438/7826035/a0b1fb3c-03d1-11e5-9276-dba571ceaa20.JPG)


## Lessons learnt
Overall I was really happy with how the photobooth performed, we captured about 240 photostrips throughout the two events. A few things that I would do differently if I did it again:

- The printer was a bit slow compared to how quickly people got in and out of the booth. I could've limited the rate at which the photo process could be started to balance it better with the printer.
- I relied on the skimpy speakers of the laptop to play the "shutter" sound indicating that a photo had been taken. But with plenty of background noise from the party, people didn't always hear it. A pair of external speakers would've solved the problem.
- Being short on time setting the booth up at one of the weddings, I didn't pay to much attention to its alignment and a lot of the shorter guests barely made it in to the frame.
- I could also have included a "frame" counter in the code that could help to notify when the printer was about to run out of paper.

![The final results](https://cloud.githubusercontent.com/assets/12212438/8763405/225a2ade-2d57-11e5-8ba1-9c02d0f6da38.jpg)

If you end up using FotoBoto yourself, I'd love to hear about it and how it went. Pull requests are highly encouraged!
