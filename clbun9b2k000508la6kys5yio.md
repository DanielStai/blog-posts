# User authentication in an Express app

## Overview

In this article, we will delve into authentication using JSON web token (JWT). User authentication is the process of verifying a user attempting access to an application or part of an application.

In the API we will build, we will implement authentication by building middleware and using the middleware to protect the routes we need to limit access to.

%[https://github.com/DanielStai/jwt_auth] 

## How does JWT work

Let's see how the JWT approach to authentication works.

Say a user gives the correct credentials and logs in. The server will generate a token and send it back in the response. Subsequent requests will have the token in the authorization header.

If the token is missing or expired, the user will not be able to continue accessing protected endpoints.

The token consists of three parts i.e. the header, payload and the signature. You can [read more about what each of the parts of a jwt does here.](https://jwt.io/introduction)

Below is an overview of the JSON web authentication process

![jwt auth](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/h8ilyulokazborame911.png align="left")

We will also use the package `bcrypt`.

## Project set up

To show how to implement jwt authentication, we will use a simple API that implements login functionality for the users of a blog. Here are the endpoints of the API:

| URL | METHOD | DESCRIPTION |
| --- | --- | --- |
| api/users/ | POST (public) | register a user |
| api/users/login | POST (public) | logs in a user |
| api/users/me | GET (private) | gets the info of the logged in user |

As described above, we don't need to implement authentication to register a user and to log in the user. We however need to ensure that when returning user info, we only do so for the currently logged-in user. This will require some form of authentication.

We will create a folder named `jwt_auth`. This is where all our API code will reside.

Let's create the `config` (where our models reside), `routes` (where we define the various routes in our API), `middleware` (where we will build error handling and authentication middleware) and `controllers` (where we will define the callback functions fired when the routes in the `Routes` directory are matched) directories.

To set up the whole project you can refer to my articles on [how to build a REST API](https://dev.to/danielstai/how-to-build-an-express-rest-api-for-a-simple-blog-app-2m6k) and [how to set up MongoDB for your project](https://dev.to/danielstai/setting-up-mongodb-and-mongoose-for-your-project-5bob).

Here's the structure of our project at the end:

![auth structure](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/92dip0x7u1zckrfdne8b.png align="left")

## Implementing `bcrypt` and `jwt`

Now that we've seen our project structure, we will implement our authentication in 3 steps:

*   Encrypt password after logging in and generate a token
    
*   Create middleware to check if the token exists and its validity
    
*   Chain authentication middleware to the routes you need to protect
    

You can view the full project before and after adding authentication here (the version with authentication is on the branch 'authentication'):

%[https://github.com/DanielStai/netninja-react-backend ] 

This project has been discussed over several blog posts hence we will only concern ourselves with implementing some authentication

Let's view our `jwt_auth/controllers/userController.js` file and see how we hash our passwords and generate our tokens.

```javascript
const asyncHandler = require('express-async-handler')
const User = require('../models/userModel')
const mongoose = require('mongoose')
const bcrypt = require('bcryptjs')
const jwt = require('jsonwebtoken')

const registerUser = asyncHandler(async (req, res) => {
    const { name, email, password } = req.body

    if(!name || !email || !password){
        res.status(400)
        throw new Error ('Please fill in all fields')
    }

    const userExists = await User.findOne({ email })

    if(userExists){
        res.status(400)
        throw new Error ('User already exists')
    }


    const salt = await bcrypt.genSalt(10)
    const hashedPassword = await bcrypt.hash(password, salt)

    const user = await User.create({
        name,
        email,
        password:hashedPassword
    })

    if(user){
        res.status(201).json({
            _id:user.id,
            name:user.name, 
            email:user.email,
            token:generateToken(user._id)
        })
    } else{
        res.status(400)
        throw new Error ('Invalid user data')
    }

})

const loginUser = asyncHandler(async (req, res) => {
    const { email, password } = req.body

    const user = await User.findOne({email})

    if(user && await bcrypt.compare(password, user.password)){
        res.status(201).json({
            _id:user.id,
            name:user.name,
            email:user.email,
            token:generateToken(user._id)
        })
    }else{
        res.status(400)
        throw new Error ('Invalid credentials')
    }
})


const getThisUser = asyncHandler(async (req, res) => {
    res.status(200).json(req.user)
  })


const generateToken = (id) => {
    return jwt.sign({id}, process.env.JWT_SECRET, {
        expiresIn:'30d'
    })
}


module.exports = {
    registerUser,
    loginUser,
    getThisUser
}
```

Encrypting the password with `bcrypt` is as follows:

```javascript
const salt = await bcrypt.genSalt(10)
const hashedPassword = await bcrypt.hash(password, salt)
```

We first create a salt with bcrypt's `genSalt` method and use this salt and the inputted password by the user to generate the `hashedPassword`. This is the password that will be stored in the database and not the user's real password. This is a security measure to prevent malicious parties from accessing passwords stored in a database.

One would then ask, how can we confirm a user's password if we don't have the raw password stored in the database? In our case, we will use bcrypt's handy `compare()` method.

Let's see it in action in our `userController.js` file when we log in a user.

```javascript
const loginUser = asyncHandler(async (req, res) => {
    const { email, password } = req.body

    const user = await User.findOne({email})

    if(user && await bcrypt.compare(password, user.password)){
        res.status(201).json({
            _id:user.id,
            name:user.name,
            email:user.email,
            token:generateToken(user._id)
        })
    }else{
        res.status(400)
        throw new Error ('Invalid credentials')
    }
}
```

Above is the `loginUser` function which is called when the route /`api/users/login` is matched. We first obtain the email and password by destructuring the request body. We then use the Mongoose method `findOne()` to find the exact user with the specified email address. To verify the user, we compare the inputted password and the password in the database using `bcrypt.compare()`. It takes the keyed-in password and the password in the database as arguments and if the passwords match it returns true.

If the user exists in the database and the password is verified, `loginUser` returns the user's details including a token that we can use to access some restricted content.

Let's see how we generate the token:

```javascript
const generateToken = (id) => {
    return jwt.sign({id}, process.env.JWT_SECRET, {
        expiresIn:'30d'
    })
}
```

Our `generateToken` function takes in the user's id as an argument. We will then use jwt's `sign()` method to create and return the token. `sign()` takes in 3 arguments:

*   id: The id of the user logging in
    
*   secret: This is a private signing key that we store in our dotenv file. It confirms the origin of the token
    
*   expiring time: We can add an optional argument to specify when the token expires
    

We will now create our authentication middleware which will make sure that protected routes are accompanied by a valid token. This is how we implement authentication with jwt in express apps.

## Authentication middleware

Some routes need to be accessed privately (as specified in the API definition) and the way we do so is by chaining some middleware to the route definition.

We will create our authentication middleware in our `middleware` directory in the file `authMiddleware.js`.

Let's take a look at our auth middleware:

```javascript
const jwt = require('jsonwebtoken')
const asyncHandler = require('express-async-handler')
const User = require('../models/userModel')

const protect = asyncHandler(async(req, res, next) => {
    let token

    if(req.headers.authorization && req.headers.authorization.startsWith('Bearer')){
        try {
            //Get token from header
            token = req.headers.authorization.split(' ')[1]

            //Verify token
            const decoded = jwt.verify(token, process.env.JWT_SECRET)

            req.user = await User.findById(decoded.id).select('-password')

            next()
        } catch (error) {
            console.log(error)
            res.status(401)
            throw new Error ('Not authorized')
        }
    }
})

module.exports = {
    protect
}
```

The code above might seem like a lot but it's quite simple. We are simply using javascript methods to get a token from the request header and verify this token. We also set the error message if the token doesn't exist or isn't valid ('Not authorized').

In the end, we export our authentication function as `protect`.

How can we utilize this protect function to protect some routes?

Let's have a look at our `routes` file now:

```javascript
const express = require('express')
const router = express.Router()
const { registerUser, loginUser, getThisUser } = require('../controllers/userControllers')
const { protect } = require('../middleware/authMiddleware')

router.post('/', registerUser)
router.post('/login', loginUser)
router.get('/me', protect, getThisUser)

module.exports = router
```

The only route we need to protect is the `api/users/me` route and we do so by including `protect` in the route definition as seen above. It's that easy!

## Testing our authentication

Let's do a recap of what we have so far. We have defined several routes for a REST API that implements some login functionality. For one specific route that returns a specific user's info, we need to do some form of authentication to prevent unwanted access. To do so we implement some middleware and chain it to our route definition.

Now that we have added authentication to our project, let's test if it works

We will start by creating a new user called `Daniel` with the email address `daniel@yahoo.com` and a password of `1234`:

![create user](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/6ybn290oo1ue7qa2w4he.png align="left")

Note that a token is generated when we create a user.

Now let's log in using the user details we have created:

![logon user](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/22omwzwy1bzzipmjssa5.png align="left")

To confirm our error handling works, let us use a wrong password and see the response generated:

![invalid credentials](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/4jqhjcit4xpqg8p6bzh5.png align="left")

We get the error message "Invalid credentials" as desired

Let's now try accessing `GET api/users/me` without using our token:

![not authorized](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/5ichy9hbo6en51srm9zb.png align="left")

We get an error message that we are not authorized to access that particular route.

Let's try accessing the same route but this time we will pass in the correct token:

![authorized](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/740yw3dd66tmj5md3jnc.png align="left")

As you can see, we have no error messages and we get the content we want back from a protected route. Success!

## Conclusion

In this article, we implemented authentication using jwt. In the next article, we will deploy our express app to the internet using Railway. You can read that article here.