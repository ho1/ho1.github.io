---
published: false
---

For our wedding a few years ago, my wife convinced me that we needed a photobooth. It'd be a great way to capture fun photos of our family and friends and also provide them with a keepsake.

Most people would have found a local supplier and it would've been another checked box in the wedding planning list. In our case however, since we were having two celebrations, on two sides of the Atlantic, in two weeks, that wouldn't work. So I decided to build a photobooth.

The specifications for the photobooth almost wrote themselves:
- It had to be easy to use and require little supervision
- It had to pack down and fit into a duffle bag which weighed less than 70 lbs/32 kg
- It had two print two "filmstrips", one for us to keep and one for the guests to take

The overall process would be something like this:
1. Guests trigger process by pushing a button
2. Four photos are taken and combined into filmstrip
3. Two filmstrips are printed onto 4x6 photo paper

I early on decided to use VB.NET to write the code that would tie all of these steps together. Grabbing images and sending them to a printer them seemed like tasks where I could leverage standard .NET functionality.


## Taking the photos

The first thing I had to tackle was to figure out a robust way of capturing the photos. My first thought was to use the [Canon SDK](http://www.usa.canon.com/cusa/consumer/standard_display/sdk_homepage) to trigger and acquire pictures from our Canon DSLR. After poking at it for a while, it became clear that it'd be a bit of an overkill.

So I started looking at using a webcam instead, since the images were printed in very small dimensions, I didn't really have to worry about resolution. With webcams being USB and "Plug-n-Play" I figured that it wouldn't too difficult to grab the images. After having tried numerous code snippets I had found online (none of which worked well) and decided that I didn't want to dive into the Windows API, I decided to go with OpenCV. OpenCV is admittedly a pretty big overkill for what I was trying to do, but the [Emgu .NET wrapper for OpenCV](http://www.emgu.com/wiki/index.php/Main_Page) was well-documented and easy to use. Combined with a Logitech C920 webcam, I got pretty crisp looking photos into VB.NET.

## Pushing the button
I needed a way for the guests to start the process and who doesn't like to push a big red button? But how would I get the signal from the button press into my .NET application. I toyed with the idea of using an Adruino or the USB data acquistion that I laying around. Instead, I followed the [KISS principle](http://en.wikipedia.org/wiki/KISS_principle) and repurposed an old USB mouse for the task. A few wires and some soldering allowed me to connect the button's switch electrically to the _right mousebutton_. Even better, since .NET has built-in events for mouse clicks it was a breeze to trigger the photo process.



