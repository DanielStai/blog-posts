# Node + Express Basics Part III: Testing a RESTful API with Postman

## Introduction

In Part II of our "REST API with Express" series, we built a simple REST API with Express.js.

In this article we will finish the API development process by testing if our API works as desired using Postman

Postman is an API platform for developers to design, build, test and iterate their APIs.

Go ahead an install Postman as per your operating system requirements. You can get instructions on how to do so [here](https://www.postman.com/downloads/)

For Linux users, we will install through the snappy packaging system by running the following in the terminal: 

```
sudo snap install postman
```

Postman will be installed as a desktop app built with Electron for all operating systems. 

Finish setting up your account and open the desktop app

You can check out the code we will be testing here on [my github](https://github.com/DanielStai/basic_express_rest_api). 

We will test the five endpoints we made in the previous article

## 1. Getting all notes

As you may recall from the previous article, we built an endpoint to fetch all the notes from our array (acting as our data storage at this point). 

Lets see whether it serves up the three notes we saved in our `notes` array. 

Get the server up and running by navigating to our project folder that contains `note-api.js` and run `npm run dev`.

You should see the following in your terminal: 

![terminal](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/0rotj9z2ggn4vatx02n2.png)

Fire up Postman and open up a new tab. 

Select `GET` as the method you want to test. In the entry box alongside, type the URL to your API which in this case is http://localhost:4000/notes. 

This is what we have so far:

![get all notes](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ipba3mzc7f3pqoi2gdt3.png)

When we click on send, we will get the following response:

![get all notes response](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/tgbxx2qt48cn5q3l4h38.png)

Which works as expected by returning all notes in our array as a JSON object.

## 2. Getting an individual note

The process is similar to the process of retrieving all notes. 

Lets try to get back the note with a `noteid` of 1. 

Type in http:localhost:4000/note/1 as the URL we want to send our request to and `GET` as the HTTP method. 

Click on send and this is the response we get:

![single note](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/du5vjujnlkftngnajt5f.png)

What happens if we try to get a note with an id that doesn't exist? 

Go ahead and replace the previous query with http:localhost:4000/note/4. 

Note that in our notes array, no note exists with a `noteid` of 4. This is the response we get which is in line with our API logic:

![note not found](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/d58ti1l1z9t2u1n12v46.png)

Fantastic!!

## 3. Creating a note

In our API we defined an endpoint for us to create a new note. Lets see if this `POST` method works correctly. 

In the same postman tab, navigate from `GET` to `POST` using the drop-down menu. On the panel just below, move your selection from 'Params' to 'Body'. Also click on 'raw' and input type as JSON. In the entry box below, lets make a new entry with id = 4.

That's a lot to take in but this is what we have so far:

![new note](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/zkuo9d7yo3o3xdm2u945.png)

When we click on 'Send', we have succesfully created a new note. To confirm this, lets `GET` all notes and see if our note has been created:

![confirm new note](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/h7utfapkkvfx1gw3gwzr.png)

Voila! We now have 4 notes from our previous 3 notes. 

You can also confirm by invoking the `GET` method on our endpoint and see whether you get a response back with our newly created note

## 4. Editing a note

Editing a note is almost the same as posting a new note. We will simply use the POST method and specify the current `noteid` of our note as the same as edited noteid. 

Not making much sense? 

Lets see what all the above looks like:

![editing note](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ylkvtwg9m7gozi6mockz.png)

We are editing the note with id = 1.

To confirm the edits have been done after clicking send, lets `GET` the note with `noteid` = 1:

![confirm edit](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/flj1sy8r1qvbz8nidfrr.png)

Edit done!

## 5. Deleting a note

So far we have created a note and saved it in our notes array, retrieved all notes, retrieved an individual note and edited a specific note.

We will finish our tests by testing our deletion endpoint that we created in `note-api.js`.

As per our API, we simply need to specify the HTTP method as `DELETE` and then specify the `noteid` of the note we want to delete.

Lets see all that in action below:

![delete note](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/94cjppppax1p2prstw6h.png)

When we click 'send', our note is deleted and we get a response back from the server that 'note has deleted'.

To confirm our data has been deleted, lets GET the note with id = 1 and see if it has been deleted. 

![confirm delete](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/l0r7txvry833kuy8skqd.png)

The note has been successfully deleted as expected

## Conclusion

By the end of Part III of our three part series of articles, we have created a basic HTTP server with express, extended it to a REST API and finally tested it with Postman.

Congratulations to us! In our next series (Node + Express Intermediate), we will dive deeper into middleware and take advantage of middleware to build better REST and GraphQL APIs.






