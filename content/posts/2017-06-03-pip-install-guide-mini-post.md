---
title: Pip install guide[Mini-Post]
author: ~
date: '2017-06-03'
slug: pip-install-guide-mini-post
categories: []
tags: [data science, python]
subtitle: 'Quick guide to installing Python libraries'
---

I will get back to the SQL posts but thought I would post something that could be useful to others. If you want to use Python for Data Science you will need additional python libraries.

I don't have a huge understanding of command line functions and I found it a bit difficult to find a step by step guide on how to install python libraries .

My wife(the doctor) thought that PIP was something else medical related from a scandal a few years ago. I won't mention it here as I'm trying to keep this blog proffessional but it has nothing to do with that.

**The basic idea is we're telling the command line to run the program 'pip' and tell it to install the python library you desire.**

Let's run through an example, I use a windows machine so that's the example I'm going to use. 

### **Step 1 Download package**

Download the relevant package that matches your version of Python(2 or 3) and OS(operating system) from the site below 

https://pypi.python.org/pypi

I'm going to download the numpy package for Python 3.6 and windows 32

![Image](/images/posts/Python%20Library.PNG)

### **Step 2 Find your pip directory**


Now we just need the full directory for pip, go into the program folder where you have Python is installed. I happen to have it installed on the C drive but it could be different in your case. A quick shortcut to get the directory of a file is to hold shift and right click the file. You'll see an option 'copy as path', click that

![Image](/images/posts/Copy%20as%20path.png)

You'll end up with a directory like the below

"C:\Python\Scripts\pip.exe"

Also when copying a directory in windows, the directory is shown with back slashes but the command line needs this to be forward slashes to run the program. We change this to:

C:/Python/Scripts/pip.exe

### **Step 3 Start the command line from the downloaded folder**

Similar to what we did above, find the folder you downloaded the python package to. 
Hold shift and right click the folder you should see an option "open command window here". Click that option 

![Image](/images/posts/Open%20Command%20window.png)


### **Step 4 Install python package**
Once the command line window is open paste the pip directory we found earlier.

Add the word "install" on the end with a space.

Now we just need the name of the package, instead of copy and pasting the name of the package. A nice shortcut the command window has if you type the first few letters of the python package in this case "nump" and  press tab it'll shift through the files in the current directory. One you've selected the right file just hit enter and you should see the below


![image](/images/posts/CMD%20install%20numpy.PNG)

Thanks for reading and appreciate any feedback on posts!

Shan

