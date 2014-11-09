---
layout: post
title:  "To convert an image from Font Awesome"
date:   2014-09-09 22:03:54
categories: Programming
tags: fontawesome
og:
  type: article
  description: You can easily get PNG files extracted from the Font Awesome by using the font awesome to png.
---

Some of the images used on the ** XPUSH ** site is converted from font in the [Font Awesome].

In this post, we will introduce to [font awesome to png] tool which was used by XPUSH team.

The [font awesome to png] source was developed in Python, using this method is very simple, not difficult also modified according to the purpose you want to use.

Based on the OS X environment,
First, make sure Python is installed. (OSX is the Python installed by default.)

In the [source] (https://github.com/odyniec/font-awesome-to-png/blob/master/font-awesome-to-png.py) of [font awesome to png], You can make sure that it uses the image library.
So [PIL] (Python Imaging Library) is required.
[PIL] has devised an update since 2009, still a lot of people use it because it was made very well.
However, since the Python version may no longer support while still rising, we will use the [Pillow] that provides the same functionality instead of PIL

[Pillow] can be installed without difficulty along the [Official Site] (http://pillow.readthedocs.org/en/latest/installation.html#mac-os-x-installation) guide.

    $ brew install libtiff libjpeg webp little-cms2
    $ sudo pip install Pillow

When installation of Pillow is complete, you'll extract the image from the font file into the [font awesome to png].


Download the Font Awesome through the [Font Awesome] site. There is TTF files in unzipped folder named `/font-awesome-XXX/fonts`

Navigate to this folder and copy a file which is named [font-awesome-to-png.py](https://github.com/odyniec/font-awesome-to-png/blob/master/font-awesome-to-png.py) to the folder. 
It can be converted to an icon in the form of a font image to the following instructions.

    $ font-awesome-to-png.py --size 280 facebook

[Font Awesome]: http://fortawesome.github.com/Font-Awesome/
[font awesome to png]: https://github.com/odyniec/font-awesome-to-png
[PIL]: http://www.pythonware.com/products/pil/
[Pillow]: http://pillow.readthedocs.org
