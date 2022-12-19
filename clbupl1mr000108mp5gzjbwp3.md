# Deploying your API to the web

## Overview

In previous articles, we have built APIs and tested them in our local environment. In this article, we will deploy a back-end to the internet.

Here's the project we will be deploying:

%[https://github.com/DanielStai/jwt_auth] 

We will use Railway as our preferred hosting provider. There are many considerations when choosing a provider. Some considerations include cost, ease of scaling, and customer support amongst many more.

Why Railway?

*   They provide a free basic tier which is perfect for learners who want to experiment and learn how to deploy our projects. For more traffic-heavy applications, you may have to upgrade your plan. Read more about Railway [here](https://railway.app/).
    
*   Railway makes deployment for learners easy since it takes care of most of the infrastructure (servers, load balancing, reverse proxies etc) behind the scenes.
    
*   The skills acquired when deploying on Railway are transferable and can be used on other popular hosting sites
    

## Preparing for deployment

So far, we have been working on our projects in our local environment. We now want to deploy our API to the web and to do so, we need to make some changes to our API to make it 'production ready'.

First, we will set NODE\_ENV to 'production' in our `.env` file.

This will result in less verbose error messages being displayed to the user of the API, increasing efficiency.

This error-handling feature is defined in our error-handling middleware (in this case it's in the file `jwt_auth/middleware/errorMiddleware.js`).

Second, we will install `compression` and `helmet`.

Compression enables us to compress the response sent back to the client making our app more efficient. Once installed, we will use compression at the root of our project as we do with other middleware.

We will also install `helmet` in the root of our project. Helmet provides additional HTTP headers that protect our app from some vulnerabilities.

To install both helmet and compression, run `npm install helmet compression`. Import and chain to your project in `server.js` as below.

```javascript
app.use(helmet())
app.use(compression())
app.use(cors())
app.use(bodyParser.json())
app.use(bodyParser.urlencoded({extended:false}))
```

We can also make some changes to how we handle logging in this project but that will be covered in a later article.

## Creating a new project on Railway

Our project is ready to be deployed. Let's head over to the Railway website.

Create an account on Railway and start a new project by clicking on `new project`:

![new Railway project](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/jzwabnwxcene2mqqbm1h.png align="left")

Click on `deploy from GitHub repo` and install the Railway app to your GitHub account:

![deploy from github repo](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/5jf73xl1xf3cbgsiqtn5.png align="left")

Select the repo you gave railway access to. In my case, this is the `jwt_auth` repo:

![select repo](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/oiqbi4qzk972enrt8tpi.png align="left")

Click on deploy now and wait for the project to build and deploy. You might get an error since we haven't set our environment variables yet:

![deploy now](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/9esnlmm5i2e2clxvihwf.png align="left")

Set environment variables. Remember these are the variables we had defined in our .env file:

![environment variables](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/obfuj5k7fsia4uw0mk0h.png align="left")

Wait for the build and deploy process to finish and if successful, you will get a success message as below:

![succesful deployment](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/nd6aftm8g17v5855o0ml.png align="left")

Since we aim to expose our API to the world, we then generate a domain name for our API:

![generating a domain](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/5g9vcww84ktyakuujsde.png align="left")

This is what we get when we click on generate domain:

![domain generated](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/505oou29xa2o8oo7mw10.png align="left")

With that done, we have successfully deployed our API. You can monitor your deployment logs every once in a while since if an error occurs you can get the details there.

## Conclusion

We have successfully built an API with jwt authentication and deployed it to the web. In the next article, we will test our API and debug some of the common issues encountered when deploying.