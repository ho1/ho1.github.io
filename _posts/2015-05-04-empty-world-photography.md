---
published: false
---

## Empty World Photography

Enter text in [Markdown](http://daringfireball.net/projects/markdown/). Use the toolbar above, or click the **?** button for formatting help.

{% highlight python %}
def loadfolder(folder):
    
    # Get a list of all .JPG in the folder
    folderstring = folder + '/*.jpg'
    files = glob.glob(folderstring)  
    
    # Load all jpgs and store arrays in our list
    imglist = []
    for f in files:
        imglist.append(loadimage(f))  

    # Get the dimension of the photos. Note this assumes all photos are the same dimensions
    width, height = Image.open(files[0]).size    
   
    return imglist, width, height
{% endhighlight %}


asdads
