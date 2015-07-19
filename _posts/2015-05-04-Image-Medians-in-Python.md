---
published: true
layout: post
date: {}
summary: See what the different elements looks like. Your markdown has never looked better. I promise.
categories: jekyll pixyll
---


Many years ago I played around with using Photoshop to remove any motion from a series of photos. It creates a cool effect of an _empty world_. A while ago, I thought it could be a fun Python project to try to recreate something similar. After a bit of hiking (to get the photos) and a bit of coding, I got my first result.

![Interstate 70 heading up into the foothills just west of Denver](https://cloud.githubusercontent.com/assets/12212438/8763796/dd151dd4-2d6b-11e5-9c6f-69b060886ddc.jpg)

The basic premise is pretty simple; take the same pixel from a number of photos and find the median color, then create a new image with the new median color. It might be worthwhile to point out that we avoid taking the average color value since we want to remove variations (motion) from the final photo. If we for a single frame have a very bright pixel where we normally would have a dark pixel, the resulting "average" pixel would be slightly brighter. So by using the median, we don't have any outliers affect our final result, which is what we are looking for.

For the approach to work, we need to have a series of photos taken with minimal camera movement between each shot. This will allow us to assume that each pixel captures the same thing over and over. Since we are combining multiple photos into a single photo, any motion will result in blur in the end. Although, one could argue that with a high enough number of photos, statistically the median value should be the same for each pixel. In my case, I just decided to use a tripod.

![Setup to capture the photos](https://cloud.githubusercontent.com/assets/12212438/8763834/a3756d98-2d6d-11e5-95ee-76f2956db2ec.jpg)

With 50 photos captured over a 5 minute period I had the photos I needed and could start coding.

### Calculating pixel medians

The first thing I had to do was to load in all images in a folder into memory. For this project I took the photos in lower resolution (1920x1080) so I didn't have any memory issues. A [Stack Overflow thread](http://stackoverflow.com/questions/26457220/python-memory-management-with-a-median-image-stacker) discusses options should memory be an issue, so that could be worthwhile checking out. Here is the code to load all the photos:

{% highlight python %}
def load_folder(folder):
    # Get a list of all .JPG in the folder
    folderstring = folder + '/*.jpg'
    files = glob.glob(folderstring)  
    
    # Loop over file list and load all jpgs and store in array/list
    imglist = []
    for f in files:
        imglist.append(loadimage(f))  

    # Get the dimension of the photos.
    # Note this assumes all photos are the same dimensions
    width, height = Image.open(files[0]).size    
   
    return imglist, width, height
    
def load_image(fname):
        im = Image.open(fname).convert('RGB')  # Load/convert to rgb
        pixel_array = np.array(im) # Convert to numpy array
        return pixel_array
{% endhighlight %}

Note that the image list contains 2-D arrays (that conversion occurs in the load_image method). Each object in the 2-D array is a three element vector such as [256 256 128] which represents the R-G-B value respectively for that pixel.

So to calculate the median pixel color we take the median R, G and B intensities for each pixel. By looping over the entire 2-D array we cover all pixels in the image and can re-assemble a new image.

{% highlight python %}
def process_pixels(imglist):
    
        # Create an empty new image to start filling in
        res_img = Image.new('RGB', (height,width), 'black')
        
        # Loop over the height and width
        for x in range(0,height,1):
            for y in range(0,width,1):
                
                # For each pixel coordinate, create an empty array
                # This will be filled with value from each frame
                r = []
                g = []
                b = []
                
                # From each photo in the list, get R, G, B values
                for current_img in imglist:
                    r.append(current_img[x,y][0])
                    g.append(current_img[x,y][1])
                    b.append(current_img[x,y][2])
                    
                # Calculate the median value of each R, G, B value
                r = int(np.median(np.array(r),overwrite_input=True))
                g = int(np.median(np.array(g),overwrite_input=True))
                b = int(np.median(np.array(b),overwrite_input=True))
            
                # Put a pixel with the right color in the right spot
                res_img.putpixel((x,y),(r,g,b))
        
        # Rotate/Flip the image and return it
        return res_img.transpose(0).rotate(90)
{% endhighlight %}

When running this code, the many median calculations is really what slows things down. I tried the [Bottleneck library](https://pypi.python.org/pypi/Bottleneck), but didn't see enough improvements to make it worthwhile, but it maybe something to further investigate.

### Making it faster

As I was writing this blog post, I came across a signficantly faster way of doing this on [Stack Overflow](http://stackoverflow.com/questions/26457220/python-memory-management-with-a-median-image-stacker), which doesn't explictly loop over all pixels and calls the np.median() functions three times per pixel. On the other hand, the way I first had written it maybe easier for a beginner to understand. In any case, here is what the process_pixel() code would look like:

{% highlight python %}
def process_pixels(imglist):
    median = np.uint8(np.median(imglist, axis=0))
    return Image.fromarray(median)
{% endhighlight %}

### Using iPhone time-lapses as input
Taking the tripod and DSLR out to create these _empty world_ photos is not always super-convenient. So I got the idea of using my iPhone and its time-lapse feature to capture multiple frames that could be processed. Using a phone car mount, I could easily keep the camera steady as the time-lapse was being captured. 

![iPhone mounted in car mount, which in turn is mounted to a railing. Looking southeast down the Highland Bridge and 16th St in Denver](https://cloud.githubusercontent.com/assets/12212438/8763960/59e0e96c-2d74-11e5-8f1d-6ccee7c5299c.jpg)

Before I could re-use the processing code shown above, I had to extract image stills from the video. I didn't have to use every single frame from the time lapse, but rather a few (specified by the fps argument):

{% highlight python %}
def convert_video(self, fname,fps=2):
        callstring = 'ffmpeg -i ' + fname  + ' -r ' + str(fps) + ' -qscale:v 2 median/output_%03d.jpg'
        subprocess.call(callstring, shell=True)
{% endhighlight %}

Which at the end produced this:
![iPhone time-lapse turned into an _empty world_ photo](https://cloud.githubusercontent.com/assets/12212438/8763975/5735a59e-2d75-11e5-98f2-52a2d74f9d84.jpg)

### More examples

The optical quality from the iPhone video was admittedly not as good as from the Canon DSLR, but it proved a quick way to create _empty world_ photos. I played around with a few other locations in downtown Denver, some of which turned out pretty cool.

![I-25 North](https://cloud.githubusercontent.com/assets/12212438/8763976/5736602e-2d75-11e5-93b2-b7dbc1d72454.jpg)

![I-25 South](https://cloud.githubusercontent.com/assets/12212438/8763977/573a9e00-2d75-11e5-9847-2d8a486287fe.jpg)

![I-25 South](https://cloud.githubusercontent.com/assets/12212438/8763974/573473e0-2d75-11e5-84bf-bb23e506ba41.jpg)

The last photo shows some light ghosting effects where there weren't enough frames without "cars" in them, resulting in the median not being the color of the roadway, but rather that of cars. One way to solve it would likely be to use more frames in the median calculation.

Now if someone would just put this functionality into an iOS app...
