# How to connect a React front-end to an Express server

## Introduction

In this post we will create a skeleton react app and connect it to an express rest API. 

This is intended to be an entry point for beginner frontend or backend devs who want a sneak peek into how they can build fullstack apps. 

Without further ado, lets get right into it

## Project setup

We will create a folder called `fullstack` which will host both our client and server apps. In your terminal or through your OS's GUI, create the following project structure

![project structure](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/4dmypqwsj8nhummr0xey.png)
Lets set up the server side code first. 

## Server code

In your code editor of choice, `cd` into the server directory and run `npm init -y`. 

This will initialize a node project for us and create a package.json file.

I have written a previous article on how to set up a basic express server and so we delve into the details of that. You can [read that article here](https://dev.to/danielstai/how-to-set-up-a-basic-http-server-in-node-express-10i).

In your server dierctory, create a `server.js` file. Make sure you update your `package.json` file to reflect that the entry point to our server is `server.js` rather than the default `index.js`. 

This is the code we have in our server.js file

``` javascript
import express from 'express'
import cors from 'cors'
import bodyParser from 'body-parser'

const app = express()
const PORT = process.env.PORT || 4000

app.use(cors())
app.use(bodyParser.json())
app.use(bodyParser.urlencoded({extended:false}))

app.get('/api', (req, res) => {
    res.json({"names":["Daniel","Theodore","Jackson"]})
})

app.listen(PORT, () => {
    console.log(`Server running on localhost: ${PORT}`)
})
```
As per the above code block, you need express, cors, bodyparser and nodemon installed. All the details you'll need to get your simple express server up and running can be [found here](https://dev.to/danielstai/how-to-set-up-a-basic-http-server-in-node-express-10i). 

Let's look at one code bock though:

``` javascript
app.get('/api', (req, res) => {
    res.json({"names":["Daniel","Theodore","Jackson"]})
})
```

In the above code snippet from our `server.js` file, we are setting the route `/api`. 

When the client app sends a GET request to that route, the response will be a json object `names` which is an array of names as seen above. 

## Browser code

We will now create a react front end that will connect to our express server and display our `names` object to the screen.

`cd` into our `client` directory and run the command `npx create-react-app .` 

This will create a react app in the current directory in this case the `client` directory. 

To start the development version of your react app in the browser, run `npm start` in the terminal

Lets go ahead and get rid of the contents of App.css as we wont be needing them. In App.js, lets also just remain with a boilerplate component as seen below

![app.js](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/lz6rut2o4swc7kcbdlzb.png)

We will start by using the `useState` hook to define `backendData` and `setBackendData`. 

We will then use the `useEffect` hook to pull in the data using Fetch API. 

You can read more about react hooks [here](https://reactjs.org/docs/hooks-intro.html)

Finally, we will define how we want our data rendered our browser. We do this inside the div tag in the return statement in App.js.

We've done so much but this is the entirety of our App.js file in our `client/src` directory so far

``` javascript
import React, {useEffect, useState} from 'react'

const App = () => {

  const[backendData, setBackendData] = useState([{}])

  useEffect(() => {
    fetch("http://localhost:4000/api").then(
      response => response.json()
    ).then(
      data=> {
        setBackendData(data)
      }
    )
  },[])


  return (
    <div>
      {(typeof backendData.names === 'undefined') ? (
        <p>Loading...</p>
      ):(
        backendData.names.map((name, i)=>(
          <p key={i}>{name}</p>
        ))
      )}
    </div>
  )
}

export default App
```
Save your App.js file and navigate to `http://localhost:3000 `if not already open

Let's check our react app in the browser and see if we have managed to display our names list.

![react app](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/1uxm906u9bnedn9tqkt5.png)

Voila! 

We have managed to build a skeleton react app and a simple express server and integrated them. 

To confirm that the fetch request we are making in our `useEffect` hook works, lets check out the network tab in our browser.

![network](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/j3vcp6i9uzavhnpuh3op.png)

Success! 

The fetch request is successful and returns a json object which is displayed on the browser.

## Conclusion

The concepts discussed above are basic react and express concepts that can be built upon to create powerful apps that use Javascript for the whole stack. 

To further build on the knowledge learnt here, we will create a React frontend for the express REST API we created in [this article](https://dev.to/danielstai/node-express-basics-part-ii-building-a-restful-api-3fj9) in my next article.

Till then, adios muchachos



























