# How to build an Express REST API for a simple blog app

## Introduction

In this article I will build a simple express server for the React tutorial by Net Ninja on YouTube. Here's a link to [that tutorial](https://www.youtube.com/watch?v=j942wKiXFu8). Here's how the front-end client we are building the API for looks like:

![blog app](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/c6xuyo28komng63dapxj.png)

The front-end app enables one to view blogs, click on a blog to view a single blog, delete a single blog and post a new blog. The YouTube tutorial uses json server which is an important tool for front end developers to simulate calls to a REST API. 

We will use this as the motivation to build and test a fully functional REST API with Express.js to serve this blog app.

The code for this tutorial can be found [here on my GitHub](https://github.com/DanielStai/netninja-react-backend).

## Endpoints
As described above, we are building an express api that will serve data to a simple front end blog app. 

Users will be able to view all posts, view a single post, post a new blog and finally delete a blog post. Our task will therefore to build the endpoints that will actualize this functionality.

Let's discuss the endpoints we will build out:

| URL     | METHOD | DESCRIPTION    |
| :---        |    :----:   |          ---: |
| api/blogs/     | GET    | fetches all blogs |
| api/blogs/{id}   | GET  | fecthes a single blog |
| api/blogs/     | POST | posts a new blog |
| api/blogs/{id}   | DELETE | deletes a single blog |


## Project setup

In my previous articles, we have built out all our API functionality in our `server.js` file. 

For this tutorial, we will adopt some best practices and separate our routes and logic into separate folders. More on that later

Let's create a folder to host our project. I'll call mine server. Let's `cd` into the server folder. In the terminal, we will run` npm init -y` to build a skeleton node project. 

Lets create the js file that will hold all our API code and call it `server.js`. This will be the 'entry point' to our API.

While still in your project directory, lets run `npm install express cors body-parser` in the terminal. 

This will install `express`, `cors` and `body-parser`, packages that will make our API development process smoother. 

To read further on the same you can read [an article I wrote](https://dev.to/danielstai/how-to-set-up-a-basic-http-server-in-node-express-10i) about setting up a simple express server that explains how the above packages are used. 

While we are at it, lets also install nodemon by running `npm install -D nodemon`. 

Nodemon refreshes our server for us. Otherwise we would be forced to stop and start our server every time we make a change.

The -D flag ensures nodemon is installed as a dev dependency

In `package.json`, we will edit `"main":"main.js"` to `"main":"server.js"`. Last but not least, under scripts we will add a new script to run nodemon as follows `"dev":"nodemon server.js"`. Lets start writing our api code in server.js

As stated earlier, we will have our routes and logic in different folders. Lets create a 2 new directories in our server directory and name them `routes` and `controllers`


## Defining Routes

We will start by importing `express`, `cors` and `bodyparser` and mounting all the required middleware. Here's what we have so far in `server.js`:

``` javascript
const express = require('express')
const cors = require('cors')
const bodyParser = require('body-parser')

const app = express()
const port = process.env.PORT || 4000

app.use(cors())
app.use(bodyParser.json())
app.use(bodyParser.urlencoded({extended:false}))
```

For a simple API, we could in `server.js` define all the endpoints and the functions that are fired when a request is made to that endpoint, but for this tutorial, we will take a different approach.

Let's assume your API serves a huge front end application and many different endpoints for different features are needed. It would be very messy and difficult to maintain if all this functionality was in the `server.js` file

It's for this reason that we will create a `blogRoutes.js` file in our `routes` folder for all the routes in our blogs feature. 

If, for instance, we added users to our app and needed to provide authentication etc, we would create a file for those routes in our `routes` folder and name it, say, `userRoutes.js`.

This will be our file structure:
![structure](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/4z0gyey5yghbp3d80ry8.png)

Let's add the following line in `server.js`

``` javascript
app.use('/api/blogs', require('./routes/blogRoutes'))

```
This defines our base route as `/api/blogs` and also tells us that the various routes for our API are set in our `/routes/blogRoutes` path.

Now let's see what we have in blogRoutes.js file:

```javascript
const express = require('express')
const router = express.Router()

const { getBlogs, postBlog, getBlog, deleteBlog} = require('../controllers/blogControllers')


router.get('/', getBlogs)
router.post('/', postBlog)
router.get('/:id', getBlog)
router.get('/:id', deleteBlog)

module.exports = router
```
We will import `express` and the `router` method to use in our `blogRoutes` file. We will also import some functions from `../controllers/blogControllers` (which we haven't built out yet)

From our `server.js` file, we know that our base API route is `/api/blogs`. 

Here in `blogRoutes.js`, we will define what happens when we access the endpoints `api/blogs/` and `/api/blogs/{id}` with either GET, POST or DELETE requests.

Let's take a look at `router.get('/', getBlogs)`. When the endpoint `/api/blogs/` is accessed, it will fire a function `getBlogs` which retrieves all blog posts.

We can further clean up our code by condensing the four lines that define our routes as follows:

``` javascript
router.route('/').get(getBlogs).post(postBlog)
router.route('/:id').get(getBlog).delete(deleteBlog)
```

Up next we will have a look at `blogControllers.js` and see what the functions `getBlogs`, `getBlog`, `postBlog` and `deleteBlog` do. 

Don't forget to `module.exports = router` as this line enables us to export the routes we have created to be used in our `server.js` file.

## Defining controllers

Let's recap what we have so far. 

In our `server.js` file, we added this line of code (`app.use('/api/blogs', require('./routes/blogRoutes'))`) that instructs our API to look in the folder `./routes/blogRoutes` for the specific implementation of our endpoints.

In `blogRoutes.js`, we defined what happens when we hit various endpoints with various request methods. 

We specifically looked at what happens when we get a `GET` request to `/api/blogs/`. We saw that a function called `getBlogs` (that has been imported from `../controllers/blogControllers`) is fired and it returns all the blog posts in our data storage. 

This is the same for all the other routes in `blogRoutes.js`

Recap done! Now let's have a look at the four functions we imported into blogRoutes.js namely `getBlogs`, `getBlog`, `deleteBlog` and `postBlog`. These are what we'll call our controller functions.

``` javascript
let blogs = [
    {
      title: "My First Blog",
      body: "Insert some text here",
      author: "mario",
      id: "1"
    },
    {
      title: "My Second Blog",
      body: "Insert some more text here",
      author: "yoshi",
      id: "2"
    }
  ]

// @desc Get all blogs
// @route /api/blogs/
// @access public
const getBlogs = (req, res) => {
    res.json(blogs)
}


// @desc Get single blog
// @route /api/blogs/{id}
// @access public
const getBlog = (req, res) => {
    const id = req.params.id 

    for (let blog of blogs){
        if (blog.id===id){
            res.json(blog)
            return
        }
    }
    res.status(404).send('Blog not found')
}


// @desc Post a blog
// @route /api/blogs/{id}
// @access public
const postBlog = (req, res) => {
    const blog = req.body
    blogs.push(blog)

    res.send('New post has been added')

}

// @desc Delete a blog
// @route /api/blogs/{id}
// @access public
const deleteBlog = (req, res) => {
    const id = req.params.id

    blogs = blogs.filter(i => {
        if (i.id !== id){
            return true
        }
        return false
    })

    res.send('Blog has been deleted')
}

module.exports = {
    getBlogs,
    getBlog,
    postBlog,
    deleteBlog
}
```
Pasted above is the entirety of our `blogControllers.js` file. 

Let us discuss the code. 

We start with creating an array called blogs which acts as our data storage for our blogs for now. This means our data is temporary. In a later tutorial we will implement a database which will help make our data persist. 

We then define four functions i.e. `getBlogs`, `getBlog`, `postBlog`, `deleteBlog`. These functions hold the logic that makes our endpoints do exactly what we want them to do. 

After defining these functions, we export them to `blogRoutes.js` file where we will combine the path, the request method and logic to return or receive the data we want

Let's take a look at `getBlog` for instance:

``` javascript
const getBlog = (req, res) => {
    const id = req.params.id 

    for (let blog of blogs){
        if (blog.id===id){
            res.json(blog)
            return
        }
    }
}
```
This function returns a single blog post which matches the `id` in the request. 

As specified in `blogRoutes.js`, the function is fired when we get a `GET` request to the route defined as `/blogs/:id`. 

The : indicates that the id variable can take any value. We then use `req.params.id` to retrieve the id from the incoming request and we store it in a variable `id`. 

We will then check our `blogs` array for `blog.id === id`. Once we have it, we will send a response back in the form of a json object. If it doesn't exist, we will send back an error message indicating the same.

Have a look at the rest of the functions and try and figure out what each does. 

Each controller function has a comment with a description, route and who can access that route.

Do not forget to export the functions created (using `module.exports`) so that we can be able to use them elsewhere in our app.

## Testing the API

We now have all the 4 endpoints we set out to build from the start. 

How can we tell if they work as we want them to?

One way is to integrate them with our frontend app (which has already been built out in [this tutorial](https://www.youtube.com/watch?v=j942wKiXFu8))and see if we get the desired results.

However, a better way to test your APIs as a backend developer is using Postman to test your endpoints. 

Let's test two of our endpoints namely `GET api/blogs/` and `GET api/blogs/{id}`

First you need to start your server if it wasn't running already with `npm run dev`. If you get errors in your terminal, you can always go through the tutorial again or check out the code in [its GitHub repo](https://github.com/DanielStai/netninja-react-backend).

Fire up Postman in your computer. If not yet installed, you can [find instructions to do so here](https://www.postman.com/downloads/).

Open a new tab, choose `GET` as the method you want to use and in the query box alongside it, type in your API link in this case `http://localhost:4000/api/blogs/`. Click on send:

![test all blogs](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/e7au2bv9y89r35z585sp.png)

Both our objects in the blogs array are returned. Success!

Let's try returning the blog with the id of 1:

![test_one](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/mqtaj189d3e94mdqi5oa.png)

Again, success.

What happens if you try to access a blog that doesnt exist in our `blogs` array? Lets try fetching a blog with an id of 3:

![not found](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/spn7bnqbiiz3wzmc3hua.png)

We don't get a blog object back. As expected, we get a message back that the blog has not been found.

What about our post and delete endpoints? If you've followed along so far, please test them out and let me know in the comments section.

## Conclusion

That's the end of our tutorial on how to build a REST API for a simple client. To take this to the next level, we will add a database so as to make our data persist in subsequent articles.