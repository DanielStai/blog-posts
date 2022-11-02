# Getting Started with the Command Line Part 2: Pipes and Redirection

This article is part of a series of articles aimed at getting you, the complete beginner at using the command line, from zero to hero. 

This article is a follow up to Command Line basics Part 1: Intro to the Command Line which you can read [here](https://dev.to/danielstai/getting-started-with-the-command-line-part-1-22bi)

The whole series is based on Meta's Backend developer course (Version Control) on Coursera. You can have a look at the course [here](https://www.coursera.org/learn/introduction-to-version-control)


At the end of this article you'll be able to: 
- Define what a pipe is and how to use it to be more effective on the terminal
- Define what redirection is and a use redirection on standard input, standard output and standard error

## Pipes

In the previous article, we went through a few basics commands such as `cd`, `ls`, `pwd` and `mkdir` among others.

We also saw a very simple example of a typical workflow using the command line.

In this article we will take it a little further by discussing pipes. 

We use pipes to take the output from one command as the input to another. 

The pipe symbol is `|`. We will use the steps below to illustrate its use.

**Step 1. Create sample files to use**

We will first create the files we will work on and add some dummy 'lorem ipsum' text to help illustrate our point. This work flow can be seen in the screen grab below

![CLI workflow](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/vi3mt137ywe7lpbmj6eu.png)

We have created two files i.e. example1.txt and example2.txt. The command cat `example1.txt` and `cat exampe2.txt` simple shows us the content of the text file

**Step 2. Using a pipe to open the text file and count the words in the file**

We will now use a pipe (`|`) to open the text files and count the words in the file. 

We can simply run the command `wc cat example1.txt -w` and similarly for example2.txt. The `-w ` flag tells the command to return the word count for the example1.txt file. 

Say we want the combined word count of example1.txt and example2.txt? 

This is where pipes come in. We will run the command `cat example1.txt example2.txt | wc -w`

All the above steps can be seen in the screen grab below

![word count](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/1qrzwsd8z5xmwhyhf3ir.png)

We have successfully used a pipe to take the output of one command (`cat example1.txt example2.txt`) as the input of the next command (`wc -w`)

Lets take a look at redirection now

## Redirection

As you may have already noticed, the standard workflow of any linux command is that it takes an input and gives some sort of output or error message. 

In most cases, the standard input device is the keyboard while the standard output device is the screen/monitor. 

Using redirection, we can change where the standard input comes from or where output and errors go.

We will take a look at the three io (input/output) redirections available i.e. standard input redirection, standard output redirection and error redirection

**1. Standard input redirection**

The standard input redirection gives you the option to record your input and save it to a file either by overwriting or appending the file. 

Lets see this using the example in the image below. We will continue working in the 'tutorial' folder we created in the previous section when discussing pipes.

![stdin](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/hx8gs8jv7cwy8h4nwlwp.png)

First we check the existing contents of the 'tutorial' folder by running the already familiar `ls` tag. We can see our two files from earlier

For user input, the symbol `<` is used. But first, type the command `cat > input.txt`. This will create the file 'input.txt' and assign whatever text you type in the console to it. Press CTRL + D to exit the input prompt

Now lets check the contents of the 'tutorial' folder. You can see a new file 'input.txt' has been created. To see the content of the file, we can type in the command `cat < input.txt`. This will display the text in 'input.txt' that we had typed in earlier.

The above workflow can be seen below:

![input.txt exists](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/h1u17bmmm3j90t0zkpwn.png)

**2. Standard Output redirection**

The redirection of standard output allows you to control where output goes. 

When you run a simple command like `ls -l`, the output is normally some text that we can see on the terminal. 

We could choose to store this information in a text file for whatever reason. 

![output.txt](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/7wyj4gi3y7adq2zlcq5f.png)

We will simply run the command `ls -l > output.txt`. 

This will take the output from the `ls -l` command, create a file called 'output.txt' and write the output there. 

Running the `ls` command we can confirm that indeed the file 'output.txt' has been created. To view the contents of the file, we run the command `less output.txt` in the terminal. 

This is what we get:

![output.txt](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/sebduexy7peg5uclgp86.png)

As we can see, our 'output.txt' file contains the same message we saw on our terminal when we ran `ls -l`. Redirection successful!

**3. Standard error redirection**

The standard error redirect allows you to specify that the error should be written to a file.

As in previous example, lets do it using a real example

Still in the 'tutorial' directory, lets try to open a file/directory that we know obviously doesn't exist there. We are trying to simulate an error message. 

We run the command `ls -l /bin/usr`

We will get the error message that the file/directory doesn't exist. 

Our goal is to redirect and write this error message to a text file thus we run the command `ls -l /bin/usr 2> error.txt`. 

The `2>` is the symbol used to redirect error output. 

![error.txt](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/emkjfe52rrde06rmak9h.png)

Check the contents of the folder to confirm that indeed the file 'error.txt' has been created. To see its contents, run `less error.txt`


![less error.txt](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/w5t02sutju3ffa4bverl.png)

There are instances where you are not sure whether you will get actual output from the command or an error. 

That's where `2>&1` comes in. Run the command `ls -l /bin/usr > error_output.txt 2>&1`. 

This will create and write the output or error to the file 'error_output.txt'.


This brings us to the end of this article on using pipes and redirection with Unix commands. 

I hope you've learnt something new or brushed up on some knowledge. 

In part 3 of our Command Line Basics series we will take a look at Grep (Global regular expression print). 





