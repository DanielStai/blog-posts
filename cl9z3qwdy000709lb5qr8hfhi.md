# Node + Express Basics Part II: Building a RESTful API

This article is Part II in our ongoing three Part series: Node + Express Basics. 

In Part I, we built a basic HTTP server and defined what middleware is, which is how express manages to abstract out all the details of dealing with requests and responses.

In this article we will define what REST APIs are and what they do. 

We will then go ahead to extend our basic server from [Part I](https://dev.to/danielstai/how-to-set-up-a-basic-http-server-in-node-express-10i) into a fully fledged REST API. 

What is a REST API? 

According to [this article by Amazon](https://aws.amazon.com/what-is/api/), REST stands for Representational State Transfer and defines a set of functions like GET, PUT, DELETE, etc. that clients can use to access server data. Clients and servers exchange data using HTTP.

**The main feature of REST API is statelessness**. Statelessness means that servers do not save client data between requests. 

Client requests to the server are similar to URLs you type in your browser to visit a website. 

The response from the server is plain data, without the typical graphical rendering of a web page.

## 1. Project set up

What are we building? 

We will build a REST API that'll serve a front end notebook application. We will build our REST API with end points that correspond to the HTTP methods of GET, POST, PUT/PATCH and DELETE. 

We will do that by extending the basic express HTTP server we made in the [previous example](https://dev.to/danielstai/how-to-set-up-a-basic-http-server-in-node-express-10i).

We will then test our API using Postman in Part III.


This is the code we have so far from the previous article. For this tutorial, create a new file and lets name it `note-api.js` and paste the following starter code.

``` javascript
import express from 'express'
import cors from 'cors'
import bodyParser from 'body-parser'

const app = express()
const port = process.env.PORT || 4000

app.use(cors())
app.use(bodyParser.json())
app.use(bodyParser.urlencoded({extended:false}))
```
In setting up our project in Part I, we installed `express` and `cors`. 

We will now install some more middleware and in this case we will install the middleware `bodyParser`. As the name suggests, we will be using this middleware to get information from the request body of our API calls. 

To install `bodyparser`, go ahead and run the following in our project folder in the terminal

```
npm install bodyparser
```
The last three lines of the example code above illustrate how to load our middleware. 

In general, we use `app.use(<middleware function>)` when mounting middleware at root level i.e. to be used on all requests. 

If we wanted to say for example mount middleware for post requests only, we would use `app.post(<middleware function>)`

Now that our app is correctly set up, lets go ahead an start building our API.

## 2. Defining data storage

For our API, we will make use of temporary data stored in an array. Take note of the temporary nature of the data. This means that any changes or modification done to our array will cease to exist once we restart our server. 

Here's a sample of one entry in our array:

``` json
{
    noteid: '1',
    title: 'First Note',
    author: 'Daniel Stai',
    content: 'This is my very first note ever',
    date_created: '2022/10/28'
  }
```
We can see the various fields one note entry has. Lets go ahead and create an empty array called `notes` and add 3 sample notes to work with. 

``` javascript
let notes = [{
    noteid: '1',
    title: 'First Note',
    author: 'Daniel Stai',
    content: 'This is my very first note ever',
    date_created: '2022/10/28'
  },
  {
    noteid: '2',
    title: 'Second Note',
    author: 'Jack Sparrow',
    content: 'This is my second note',
    date_created: '2022/10/28'
  },
  {
    noteid: '3',
    title: 'Third Note',
    author: 'Aegon Targaryen',
    content: 'This is my second note',
    date_created: '2022/10/28'
    
  }
```
In subsequent articles, we will make our data persist by using a database such as MongoDB and also generate unique ids to our entries for ease of access. 

## 2. Getting all the notes

Now that we have some sample data to work with, lets go ahead and add a few endpoints to our API.

As we saw in our REST API definition, the requests made by the client to the server (the endpoints) are similar to the URLs we normally type in a browser.

Our server is currently running on port 4000 so what happens when one sends a `GET` request to `http:localhost:4000/notes`? 

Ideally we want them to get back a list of all the notes currently in our array. Lets go ahead and write that route. 

Express routes are of the form `app.METHOD(PATH, HANDLER)`.

`METHOD` is the corresponding HTTP method in lowercase, `PATH` is the relative path in our server and `HANDLER` is the function that Express will call once our route is matched. 

In our case to retrieve all notes we have:

``` javascript
app.get('/notes', (req, res) => {
    res.json(notes);
});
```

And to retrieve a single note, we have:

``` javascript
app.get('/note/:noteid', (req, res)=>{
    const noteid = req.params.noteid

    for (let note of notes){
        if (note.noteid === noteid){
            res.json(note)
            return
        }
    }
    res.status(404).send('Note not found')
})
```
In the code block above, when for example we get a get request from `say http://localhost:4000/note/1`, we extract the note id using` req.params.id`. 

This enables us to use this id value in the request body to check whether there is a corresponding match in our array `notes`.

If such a match exists, we return that particular note. If it doesn't exist, we return a `404 error` with the custom message that 'Note not found'.

## 3. Creating a new note

To create a new note, lets create a skeleton html file that we will use to add data to our `notes` array. Create a new file called `new-note.html` and paste the following code

``` html
<div class="container">
    <hr>
    <h1>Create New Note</h1>
    <hr>

    <form action="http://localhost:4000/note" method="POST">
        <div class="form-group">
            <label for="noteid">Note ID</label>
            <input class="form-control" name="noteid">
        </div>

        <div class="form-group">
            <label for="Title">Title</label>
            <input class="form-control" name="title">
        </div>

        <div class="form-group">
            <label for="Author">Author</label>
            <input class="form-control" name="author">
        </div>

        <div class="form-group">
            <label for="Content">Content</label>
            <input class="form-control" name="content">
        </div>
        
        <button type="submit" class="btn btn-primary">Submit</button>
    </form>
</div>
```
I wont go to the details of the html code but if you run the live preview on vscode or go to `http://localhost:3000/new-note.html` on your browser, you'll see our form that we'll use to post a new note entry. 

![new note html](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/m1fap9mzpkbc8zmxfh41.png)

Back in our `note-api.js` file, lets deal with the logic of making this happen. Following the same structure for our get requests, lets write our post request

``` javascript
app.post('/note', (req, res)=>{
    const note = req.body

    console.log(note)
    notes.push(note)
    res.send('Note added to notebook')
})
```
We will assign the request body i.e. `req.body` to a variable `note` and then push this new note to our already existing notes array using `notes.push(note)`. 

We also log the new note that has been created to the console. This might help when debugging potential issues.

When that is successful, we will send back a response i.e. 'Note added to notebook'. 

With your server running, go ahead and fill our form. We will get the following if successful:

![new note confirmation](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/rhtgm5jyg1m90u5ghp4r.png)

## 4. Editing a note

Once you have created a note, you might need to edit the contents of the note. Let's add that logic to our API. 

``` javascript
app.post('/note/:noteid', (req, res)=>{
    const noteid = req.params.noteid
    const newNote = req.body

    for(let i=0; i<notes.length; i++){
        let note = notes[i]
        if (note.isbn === isbn){
            notes[i] = newNote
        }
    }
    res.send('Note has been edited')

})
```
We will assign the variables `noteid` and `newNote` to the note id and request body respectively. 

We will then loop through our notes and check if there exists a note with a matching id to the note we want to edit. If it exists, we replace its contents with `newNote`. 

We also send back a response of 'Note has been edited'.

## 5. Deleting a note

So far we've dealt with creating a new note, retrieving an individual note, retrieving all notes and editing an existing note. 

The last part of the logic of our API will deal with deleting a note from our data storage. 

``` javascript
app.delete('/note/:noteid', (req, res)=>{
    const noteid = req.params.noteid

    notes = notes.filter(i =>{
        if (i.noteid != noteid){
            return true
        } 
        return false
    })
    res.send('Note has been deleted')
})
```
With deletion, we need to be careful so as not to inadvertently delete all records in our data store. 

To do so, we will filter our notes array looking for a record with a note id equivalent to the note id in the request.

Once found, that record is filtered out, returning the filtered array without the desired record.

## Conclusion

And with that we have a fully functional RESTful API. How do we know it does what it says it does? In Part III, we will test our API using Postman. You can go ahead and [read the article here](https://dev.to/danielstai/node-express-basics-part-iii-testing-a-restful-api-with-postman-4c2b).

