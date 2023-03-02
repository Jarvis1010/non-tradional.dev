---
title: "Command Line Basics For New JavaScript Developers"
datePublished: Wed Sep 11 2019 13:31:03 GMT+0000 (Coordinated Universal Time)
cuid: ckxqmszul0al61ms1ckoqayby
slug: command-line-basics-for-new-javascript-developers-ec80d2867503
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316547724/1rDphX8EL.jpeg
tags: bash, command-line, terminal-command

---

Photo by [Shahadat Shemul](https://unsplash.com/@shemul?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

Switching from a career in finance to software development brought on many things that were new and unfamiliar. One that was probably the most foreign was learning to use the command line on a regular basis. If you are like me, you probably grew up using a GUI of some sort. You probably only used the terminal if you absolutely had to and for most PC users, Mac or Windows, you will never need to use the terminal or command prompt.

Learning to use the command line is very helpful and necessary. One can typically move around your computer and run commands faster using a keyboard than doing the same things using a mouse. More importantly, there are certain commands that can only be run on the command line and one cannot properly learn to code without learning, at least the basics of how to use the command line.

### Shells

When you run the command prompt or terminal what you are really running is something called a “Shell.” Shells, simply speaking, are user interfaces that give the user access to the operating system’s services. The Graphic User Interface, or GUI, that you interact with on a day to day basis is a type of shell. The shell we will be learning to use in this post is called a Command Line Interface or CLI for short.

Mac and Linux machines come with a command shell called bash, which is a popular shell used in Unix based operating systems. Windows, on the other hand, uses a shell called command prompt by default which is derived from the old MSDOS prompt of old. Even though these come preinstalled, you can install other command-line shells. Powershell, for example, is a common shell used in Windows especially among .Net developers.

For the purposes of this post, I am going to focus on bash shell commands since both Linux and Mac use the bash shell by default. Powershell also has many of the same commands as bash, so everything I discuss in this post should be able to be used in Powershell as well. Some of these commands do overlap with the default command prompt in windows, but not all of them, though a simple google search should help you find equivalents for those that don’t.

### Getting Info

One of the most common things you will need to do is get information about the current directory. To get a list of files and folders in your current directory you simply use the command `ls` which stands for list. Running this command will give you a readout of all the files and folders in the current directory.

Most of the time, the command line will tell you what directory you are in, but if you are ever working in a terminal where this has been turned off, you can get your current location using the command `pwd` which stands for present working directory

### Moving Around

It’s good to know how to get info on where you currently are, but how do you move around to other folders? This is accomplished using the `cd` command, which stands for change directory. Unlike `ls` and `pwd` commands, the`cd` command takes an input. The input it takes is simply the directory that you want to change to, like this:

cd /Users/temp/

Running the above command would change the directory to the temp folder in the Users directory. In the above example, we are using the full path, but typically we want to move around relative to the current directory. If we want to move to a child directory, one can simply type in the name of the directory. Using the above example and assuming we were already in the Users directory if we wanted to get to the temp directory we would simply need to write `cd temp` to get to the temp directory.

What about when you want to move up to a parent directory? To do that one must first learn two short cuts you can use in the command line. The first is the single dot, `.` which is a short cut that means the current directory. Using it with the `cd` command is obviously not useful since it will just change to the current directory, but it can be very helpful with other commands. The second short cut is much more useful to the `cd` command, it is the double dot. `..` which is used to mean the parent directory.

To get to the parent directory of whatever directory you currently are in once simply needs to write, `cd ..` to get there. You can go up multiple levels simply by adding more double dots separated by slashes like this: `cd ../../..`

One more useful tidbit of knowledge. It can be very hard to remember how directories are spelled or one just doesn’t want to type out the entire name of the directory. Anytime, while writing the path, you can hit tab and the shell will attempt to fill in the name of the directory if it can.

### Creating Files and Directories

Another common task one needs to do is to create directories and files. Making a directory is very simple. One simply uses the `mkdir` command and then the name of the directory you want to create. `mkdir` stands for make directory.

There are different ways one could create a file but the easiest way is to use the `touch` command. For example, if you wanted to make a file called `index.js` you would simply write `touch index.js` and the shell will create an empty index.js file in the current directory

### Copying Files and Directories

To copy a file, you will need to use the `cp` command. The `cp` command takes two inputs: the source file and the destination file, separated by a space. To copy a file called index.js to another file called copy.js, you would write:

cp index.js copy.js

If you wanted to copy to another directory you would need to add the full path of the destination folder in front of the file name.

Copying a directory is not as simple. If you attempted to do the same thing with a directory you will get an error and nothing would be copied. This is because the shell doesn’t know what to do with all the files and folders that may be inside the directory.

If you wanted to copy all the files and folders exactly how they were, you would need to pass a flag to the command. Flags are passed immediately after the command and typically have either a single or double dash. The flag that we pass to the `cp` command is the `-r` flag. The `-r` means recursive and tells the `cp` command to recursively copy all the files and folders in the directory to the new destination.

### Deleting Files and Folders

The final command that is important to know is the `rm` command. `rm` stands for remove and is how we delete files and folders. To delete a file one simply needs to type `rm` and the name of the files, like this: `rm copy.js` and the file will be removed.

Just like the `cp` command, it’s not that simple when trying to delete a directory. Luckily we can pass the same `-r` flag to the `rm` command to recursively delete all the files and folders inside of the directory we want to remove.

The command line might be foreign, but it doesn’t have to be hard. It may take a bit to get used to it, but you will get it. Learning how to properly navigate the command line interface will improve your ability to work in the environment that you code in and help you spend more of your time doing what you love: Writing amazing code to solve the world’s problems.