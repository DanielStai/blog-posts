## Getting Started with the Command Line Part 1: Intro to the Command Line

This article is part of a series of articles aimed at getting you, the complete beginner at using the command line, from zero to hero. 

At the end of this article you'll be able to: 

- define what the command line is
- advantages of using the command line 
- various uses of the command line for both basic and advanced users 
- getting started with basic commands


This article is based on Meta's Back-end Developer Certificate Course 3: Version Control. You can find a link to the course [here](https://www.coursera.org/learn/introduction-to-version-control) and a link to my article on Meta Back-end Development Course 1: Introduction to Back-end Development [here](https://dev.to/danielstai/getting-started-with-the-command-line-part-1-22bi)

We interact with computers with a daily basis to perform various tasks e.g. editing word documents, listen to music, watch videos, create files etc. At the heart of all these interactions is a process of the computer accepting data and giving some output. 

There are various ways we interact with the computer with the most popular one being through the use of graphical user interfaces (GUI). 

These are very popular since they are easy to use with little or no training but the have their own limitations including being slow and limiting full interaction with the computer. 

Another way to interact with the computer is the command line. Using the command line is faster, less prone to errors and offers a lot of flexibility.

With the command line, we can perform some basic tasks like:
 
- creating directories
- creating files 
- combining directories 
- copying and moving files 
- performing advanced searches

Some of the more advanced uses of the command line include but not limited to: 

- tracking software changes 
- accessing remote servers 
- unzipping archives 
- accessing software manuals 
- installing and uninstalling software 
- checking, mounting and unmounting disks 
- automating tasks with scripts 
- controlling access to files and directories
- containerization (running and controlling self contained virtual software)

## Basic Commands
Let's introduce some of the most common and basic commands: 

- ```
cd
``` - This command is used to change into a directory for example if you wanted to change into the desktop directory you would type `cd ~/Desktop`. Similarly, to leave that directory you type `cd ..`

- The `touch` command is used to create files. Say you want to create a file named example.html. This would be the correct command syntax for that - `touch example.html`

- Another basic command is the `mkdir` command which is used to create a directory. If for instance you wanted to create a directory named project you could type `mkdir project` into the command line

So how do we put this all together to effectively communicate with the computer? Lets see this in a sample workflow:

Lets say you want to create a folder for your new text project, create an empty text document,add some content into the text document and open the same on a code editor so as to work on it:

1. `cd ~/Desktop` - Change into Desktop directory
2. `mkdir myproject` - Create a folder called myproject on the desktop
3. `cd myproject` - Change into the myproject folder
4. `touch example.txt` - Create a file called example.txt
5. `cat > example.txt` - This will let us add some text to the file. After typing whichever text you want to go in the file, press CTRL+D to terminate the command
6. `code example.txt` - Open example.html on VSCode to edit

And there you go. We have defined what the command line is, its various uses and we've gone through a sample workflow using the command line to create a folder, create a file, add content to the file and finally open a code editor to continue working.

On the next article in this series we will delve further into understanding the command line. We will look at how to:

- create, rename and delete files and folders on your hard drive using Unix commands
- Use pipes and redirection.

Adios 