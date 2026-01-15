---
title: CS 35L Project Guide

---

# CS 35L Project Guide

- link to feedback form: <https://forms.gle/CrCaC7nNA1rdsbA98>

## Introduction

The final project for CS 35L is designed to allow you to express your creativity with a real-world project: there are a couple requirements on what you have to do, almost no restrictions, and you have free rein over what you want to do.

This guide is intended to help you figure out how to get started, as well as to clarify common web development concepts that students frequently get confused by, such as how to properly manage a Git repository between 5 people or how to create an API contract.

## Installations

For the project, we recommend developing on your local machine, not `lnxsrv`. Thus, you will need to install some software onto your computer.

Most people should have at least [Git](https://git-scm.com/) installed for version control.

:::warning
**Warning**: We do not penalize for other version control systems, but we will eventually need a link to a GitHub mirror if not already on GitHub.
:::

As far as everything else goes, it is up to your group what you decide to use! You will eventually need to pick a tech stack (see [below](#Picking-a-Tech-Stack)), and based on your tech stack, you will need to install certain software. It may be helpful to use a **package manager** to install software and manage dependency versions.

- For Mac users, we recommend using [Homebrew](https://brew.sh/) as a package manager.
- On Linux, use whatever package manager is on your system (e.g. `pacman` on Arch).
- For Windows users, we recommend using [WSL](https://learn.microsoft.com/en-us/windows/wsl/install) and using whatever package manager is on your system just like for Linux users.

    - Assuming you installed Ubuntu, the package manager is `apt`.
    - Consider using the Docker installation instructions under Assignment 3 if you are having difficulties with certain packages.

## Getting Started with GitHub

GitHub is the biggest Git provider, and is highly recommended for the project. Everyone in the group should have their own GitHub account, and you should make a **private** repository for the project, [adding everyone as a collaborator](https://docs.github.com/en/account-and-profile/setting-up-and-managing-your-personal-account-on-github/managing-access-to-your-personal-repositories/inviting-collaborators-to-a-personal-repository). We highly recommend you only make **one** repository that contains **both** the frontend and backend, as it makes the development process much easier down the line.

You can **test that your GitHub account** works by doing the following:

1. Create a new repository (e.g. `test-repo`).

    - You should also click "Private" to make this repository private. Then, just click "Create repository."

2. Follow the instructions under "...or create a new repository on the command line." It's recommended to [set up an SSH key](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account) to authenticate to the repository, but you can also set up a Personal Access Token. To do so:

    - Click your profile picture in the top right, then click "Settings" > "Developer settings" > "Personal access tokens" > "Tokens (classic)" > "Generate new token" > "Generate new token (classic)"
    - Make sure to at least check "repo" (everything under repo should automatically get checked as well).
    - Set the expiration to last throughout the entire course (e.g. 90 days).
    - Click the "Generate token" button at the bottom and save this token somewhere. You will use this for pushing. Make sure to save this token somewhere secure.

3. Refresh the website with your GitHub repository. You should see your changes.

## Git Best Practices

While Git is very useful for collaborative development, it can be a bit clunky to use at first. Here are some tips to use Git effectively:

1. **Have a proper `.gitignore` file.** The `.gitignore` file, stored in the top-level directory of your repo, specifies which files to not commit. This is very useful to avoid uploading installed packages (e.g. `node_modules/`), build artifacts (e.g. `dist/`), secrets (e.g. `.env`), and editor-specific configuration (e.g. `.vscode/`). You can use [gitignore.io](https://gitignore.io) to generate a `.gitignore` file for your languages and code editors.
2. **Commit frequently with descriptive messages.** It's easy to forget about Git, work on a load of features, and commit it all in one big commit. However, this makes the repository harder to work with in the future (e.g. it's very easy to revert a whole commit but much harder to revert part of a commit), and makes it easier for merge conflicts to pop up. It's better to make smaller commits, and have descriptive commit messages in order to make tracking them easier (e.g. `Fix /api/register sometimes erroring out` instead of `bug fix` or `bruh`).
3. **Work on separate, short-lived, branches.** It may seem like a pain to have to deal with creating and merging branches, but if everyone works on the main branch you may run into errors (e.g. need to rebase).

## Frontend vs. Backend

A web app consists of _three_ components: the **frontend**, which is what runs in the user's browser (e.g. React code), the **backend**, which runs on your server (e.g. an express server in Node.js), and the **database** which either runs on your server (e.g. sqlite) or is a separate instance (e.g. MongoDB).

> Note: In either case, think of the database as only being interactable via the backend. Your backend server is the only way of creating/reading/updating/deleting persistent data.

As the browser, you will directly contact the frontend server and backend server. To receive the HTML, CSS, and JS, your browser contacts the frontend server and will receive the raw HTML, CSS, and JS that the browser renders. Your frontend JS will contain code that makes HTTP requests (e.g. the [`fetch` API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)), and your browser will run the JS that makes an HTTP request.

Your backend will read and write to the database. Your frontend HTML/CSS/JS can use the data received from the backend to display the data.

## API Contracts

Before beginning developing, your team needs to determine how the frontend and backend will communicate with each other. Frequently, when working in team projects, you will need to divide the work. When dividing the work, everyone must be clear on how certain systems will interact with each other.

For example, let's say person A works on a `BigButton` component and person B needs to use `BigButton`s in their component. Person A originally says that you need to pass a radius to `BigButton` to determine the size.  Person B specifies `BigButton`s' sizes by their radii. However, after some time, person A instead decides to change the interface to accept a side length instead. Now, suddenly every `BigButton` is half as large as before, and person B will need to fix their code.

To avoid such an issue, person A and person B **ideally agree** upon how a `BigButton` works before person A starts implementing `BigButton` and before person B starts using `BigButton`. Do your best to try and maintain consistent contracts between your frontend and backend. If the frontend expects the backend to accept an email address in `/api/login`, the backend must continue to support email login on `/api/login`. Such agreements form an **API contract**.

Sometimes, you may realize that _your original contract is not possible_. For example, maybe both the frontend and backend forgot that you must include a password in `/api/login`, so their original contract doesn't require a password. However, when implementing, the backend will realize that they can't make a proper login API endpoint without changing the contract. _When changing a contract, you must communicate this clearly to everyone._

### HTTP Semantics

When designing an API contract, it is helpful to understand what you have to work with in HTTP.  Typically, we consider the following parts:

- **endpoint**: represents a logical resource. For example, `/api/profile` might represent a given user's profile.
- **method**: what we intend to with an endpoint. Typically, one of:

    - `GET`: retrieve/read the resource
    - `POST`: create a resource or update a resource based on its previous value (e.g. increment a number)
    - `PUT`: update the resource independent of its previous value (e.g. set a number)
    - `DELETE`: delete a resource
    
- **URL parameters**: options supplied to specify something about the endpoint. For example, `GET /api/profile?truncate=true` could mean that you want to only retrieve part of a profile.
- **data parameters**: options supplied via form data which can be custom. This is used for `POST` and `PUT` requests where you want to be able to specifically specify what fields you wish to update. For example, `{username: "my cool new username"}` could be passed as form data to `PUT /api/profile` to change my username to "my cool new username".
- **request headers**: headers you supply as part of the request. For example, if I want `json` format, I could specify `Content-Type: application/json`. Note that this is essentially just a hint to the server; it is up to the server to correctly implement handling headers. Typically, request headers will only be used for passing authorization cookies via `Authorization: Bearer [...]`.
- **response**: a [status code](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status) and optionally some headers or data (see above).

    - 1XX: continue
    - 2XX: good
    - 3XX: redirection
    - 4XX: error because the client did something wrong (e.g. your request is missing data parameters)
    - 5XX: error because the server ran into something unexpected (e.g. connection to the database suddenly closed)

> Note: 1XX and 3XX response codes are typically unused in 35L. However, they are common in other application so it may be nice to know. For example, 101 can be used to change an HTTP connection to a WebSocket connection.

### Example API Contract

Suppose we want users to be able to register, login, and view or edit their profile.

Then, we need to have endpoints for each of these functions: `/api/register`, `/api/login`, and `/api/profile`. Then, we need to decide which methods we allow for each endpoint.

We use `POST` for `/api/register` and `/api/login` in accordance with REST API conventions. We specify the headers here, but typically, you won't need to worry about explicitly handling the headers outside of cookies.

Then, `/api/profile` refers to the same resource: the logged in user's profile. Thus, we should use `GET` for retrieving the profile data and `PUT` to update the profile data.

Then, we go about thinking about possible outcomes based on what the user supplied. Every HTTP endpoint ought to have a **2XX** and **4XX** response. After all, any endpoint can succeed or fail. You can get more specific with user successes and errors by specifying something other than just 200 OK and 400 Bad Request. For example, 401 Unauthorized is common for indicating invalid authentication credentials (you are not logged in).

Most HTTP endpoints ought to also have a **5XX** response. In general, you should assume that your database is reliable and your connection will persist, but in the event something goes wrong, it's helpful to know what went wrong. It's typical to include a 500 Internal Server Error for catching exceptions.

You _do not_ have to go into so much detail, but you should at least keep some of these ideas in mind. Some **key takeaways** are:

- use `GET` for reading, `POST`/`PUT` for writing, and `DELETE` for deleting
- indicate what some 2XX, 4XX, and 5XX response status codes ought to mean
- include a brief description to indicate what the endpoint + method does

Below is a sample API contract. Notice that _there is no code_. You should not be writing too much code before you have a contract.

> Note: Of course, you should still familiarize yourself with the tools you plan to use. However, don't work too much on implementing specific functionality before you understand the details you must account for.

```
POST /api/register
------------------
Register a user.

- URL parameters: none
- data parameters:

    - email: string
    - username: string
    - password: string
    
- response:

    - 201: successfully created new user
    - 400: cannot register user with existing username/email
    - 500: internal server error (e.g. could not connect to database)

POST /api/login
---------------
Login a user by setting a cookie.

- URL parameters: none
- data parameters:

    - email: string
    - password: string
    
- headers:

    - Content-Type: application/json

- response:

    - 200: successful login
    
        - headers:
        
            - Authorization: Bearer token
    
    - 400: invalid authentication
    - 422: incomplete data parameters
    - 500: internal server error (e.g. could not connect to database)
    
GET /api/profile
----------------
Get a user profile, assuming they have logged in.

- URL parameters: none
- data parameters: none
- headers:

    - Authorization: Bearer token
    - Content-Type: application/json

- response:

    - 200: successfully retrieved data
    
        - data:
        
            - username: string
    
    - 401: invalid authentication (missing/invalid cookie)
    - 500: internal server error (e.g. could not connect to database)
    
PUT /api/profile
----------------
Edit a user profile, assuming they have logged in.

- URL parameters: none
- data parameters:

    - username: string

- headers:

    - Authorization: Bearer token
    - Content-Type: application/json

- response:

    - 200: successfully updated data    
    - 401: invalid authentication (missing/invalid cookie)
    - 500: internal server error (e.g. could not connect to database)
```

## Picking a Tech Stack

A _tech stack_ is a list of technologies you intend to use to implement your application. Your tech stack should always cover a frontend, backend, and database.

> Note: For some reason, CS nerds love abbreviating tech stack names e.g. MERN = MongoDB + Express.js + Node.js + Node.js.

Generally speaking, your choice of tech stack does not matter, but some may be nicer for your specific needs. Assuming you pick popular enough technologies, you should basically be able to mix and match them. For example, MongoDB is a popular database with official support Python, JavaScript, and C++ among [other languages](https://www.mongodb.com/resources/languages).

Most databases require additional setup. This is because you'll be sending and receiving data across the network. If you want to avoid setting up a database over the network, consider using a **mobile database** like sqlite. The data will be stored locally; your database is basically just a file on your local machine. Note that using a mobile database will mean that you cannot share data across multiple people unless you send a copy of your database to someone else.

### Recommended Tech Stack

Other tech stacks are definitely viable, but for our recommendation and the code snippets, we will be using **sqlite, Express.js, React.js, and Node.js** (SERN).

Other options you _might consider_ looking into are as follows:

- database

    - MongoDB: just be careful about setting up all of the tooling correctly; come to office hours if you need help!
    
- backend

    - FastAPI with Python (if you come to Eric's office hours, he will probably be using FastAPI)
    - Flask with Python (if you come to Benson's office hours, he will probably be using Flask)

- frontend

    - Svelte

Some options we specifically don't recommend are as follows:

- Firebase: does too much for you
- Ruby on Rails: does too much for you

## Secrets

If you are using external services, you probably have some sort of private key to access those services. Make sure to distribute these private keys securely. **Private keys should not be committed to a public GitHub repository.** Even if you remove the file containing the private key, since you committed the private key, it will still be in the commit history.

> Note: GitHub has some automatic protection in place to prevent people from accidentally committing private keys, but be careful!

To avoid doing so, it is common to create a file named `.env` or `.secrets.env` and add this filename as an entry to your `.gitignore`. Thus, you can safely use the secrets without accidentally committing them. Note that this means you will need to share your private keys outside of GitHub (e.g. by email).

## Connecting Your Project

_Written by Benson Liu (CS 35L S24 LA)._

One of the most critical parts of the project you probably are leaving with your group to the last minute is connecting your frontend and backend together. This is actually one of the **hardest** parts of your first software construction project; especially, when trying to deal with user **authentication** and **cookies**. This is meant as sort of a quick one page document to crash course you into figuring out and debugging some of the problems that you might run into. But also, **don't leave this to last minute**. ;-;

### Web Communcation Fundamentals

For this document, I will be explainining how application communication works in terms of **REST APIs** and using the browser's [**Fetch API**](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API). There are technically a few ways to do this but this is one of the more widely used ways to do it. Your group is free to pursue alternative solutions such as using axios and other non-REST forms of APIs. I will be making some assumptions while explaining this document such as your group is working using the **MERN** stack where your React frontend is running on `http://localhost:3000` and your Express.js backend is running on `http://localhost:5000`.

#### REST APIs

RESTful or REST Application Programming Interfaces (APIs) are meant as a way to communicate between clients and servers in full-stack applications using HTTP. There are actually a couple different ways to communicate in networked applications (e.g. gRPC) but REST is one of the most popular and the one we talk about most for this class. REST breaks down communication between **endpoints** which are specific parts of your server where you separate functionality (e.g. `/api/user` to handle user creation, deletion, etc). The way you can distinguish between different actions to a particular endpoint is by using the HTTP request **method**. There are technically quite a few methods but these are the most commonly used.

- OPTIONS: used to see which HTTP request methods can be used on an endpoint
- GET: retrieving data from and endpoint (what your browser and fetch does by default)
- POST: often used when trying to **create** data
- PUT: used when trying to **update** data
- DELETE: used when trying to **remove** data

Often times data sent over REST APIs include a request body (note: GET and OPTIONS do not include a request body but the others do) which is commonly in the form of JavaScript Object Notation (**JSON**). An example JSON is shown below.

```javascript=
{
    "username": "admin",
    "password": "password",
    "extra": [1, 2, 3, 4, 5],
    "cool": {
        "a" : 1,
        2: "z",
    }
}
```

![image](https://hackmd.io/_uploads/rkAs9Bt3a.png)

#### Fetch API

The fetch API is a builtin JavaScript API in the browser that allows you to make HTTP requests. Another popular library that is used is `axios` but for the sake of this document, we will use fetch instead (mostly because I am more familiar with it but also because it is native to the browser). An example request is shown below.

```javascript=
fetch("https://localhost:3000");
```

By default, fetch makes a get request but if you try running this in the console, you may see something confusing about **Promises**. fetch is an example of a asynchronous function in JavaScript. These are meant to allow for other parts of the code to execute while the rest is executing. This is common in network requests because these can often take a long period of time so a Promise is returned and is resolved once the request is completed or failed. However, in some cases, you care about the specific response that is returned. An example of making a POST request is shown below.

```javascript=
let res = await fetch('http://localhost:5000/api/user', {
    method: 'POST',
    headers: {
        'content-type': 'application/json'
    },
    body: JSON.stringify({
        user: 'admin',
        pass: 'password123'
    }),
});

let json = await res.json();
console.log(json);
```

A quick note is that if you are writing functions and need to use async/await, you need to include this inside other async functions. An example of this is shown below. You may need to be careful when doing this such as including these inside a `useEffect` as there are some [quirks](https://stackoverflow.com/questions/53332321/react-hook-warnings-for-async-function-in-useeffect-useeffect-function-must-ret) with this.

```javascript=
async function createUser(username, password) {
    let res = await fetch('http://localhost:5000/api/user', {
        method: 'POST',
        headers: {
            'content-type': 'application/json'
        },
        body: JSON.stringify({
            user: 'admin',
            pass: 'password123'
        }),
    });
    if(await res.status !== 200) {
        alert("Creating a user failed!");
        return;
    }

    let json = await res.json();
    console.log(json);
}
```

### Why is connecting things hard?

When first trying to connect your frontend to your backend, you may run into this error.

![image](https://hackmd.io/_uploads/S1OKrzO36.png)

What exactly is this error? Why is it occuring? To get a better sense of why this occurs we need to understand some of the basics of **web security**.

### Same-Origin Policy

The Same-Origin Policy is a security isolation technique implemented in browsers to separate different websites' resources from each other. The big idea behind this is that sensitive resources such as cookies and content cannot be accessed **cross-origin**. This is great defense since it ensures that if an adversaries opens up a tab in one one window, that they would not be able to access the resources of a different website open in another window. An **origin** is composed of three different parts: **scheme/protocol** (e.g. https), **host/domain name** (e.g. example.com), and the port (e.g. 443). A quick note is that there are a couple implied ports such as port 80 when visiting a website over HTTP and 443 when visiting a website over HTTPS.

![image](https://hackmd.io/_uploads/Hk_J8GOn6.png)

The Same-Origin Policy can be thought of as a simple if-statement where if the origin of where a JavaScript request is coming from does not match the origin of where it is trying to access (e.g. via a fetch request). Then the browser would not execute this request at all (even if one part is different such as the port number).

![image](https://hackmd.io/_uploads/BkCQLGOha.png)


The **Same-Origin Policy** is a big problem for your CS 35L final project because you are making requests **cross-origin**. This is because if we compare the origin of your frontend React app (i.e. http://localhost:3000) and backend Express.js app (i.e. http://localhost:5000) these are **different origins** (the reason is because the **port numbers** are **different**). This is a big problem since we not only need to figure out how to make requests cross-origin but also how to send **sensitive information** like cookies. How can we do this?

### Cross-Origin Resource Sharing (CORS)

A way to allow cross-origin requests is to whitelist certain origins from the server. **Cross-Origin Resource Sharing (CORS)** are rule set by the server but enforced by the browser. If a browser is about to make a reques cross-origin, it first sends a **preflight OPTIONS** request to figure out of there are any special rules that allow it to make the request cross origin. If there, are then it finally makes the request itself.

![image](https://hackmd.io/_uploads/BJy5wrKha.png)

There are a few special headers that allow cross-origin requests to be made. An example of the following that would sufficient for your project is shown in the example HTTP response below.

```
HTTP/1.1 200 OK
Access-Control-Allow-Origin: http://localhost:3000
Access-Control-Allow-Credentials: 'true'
Access-Control-Allow-Methods: OPTIONS, GET, POST, PUT, DELETE
...
<rest of HTTP response>
```

`Access-Control-Allow-Origin` specifies the specific origin which is allowed to make cross-origin requests to this server. `Access-Control-Allow-Credentials` determines whether or not the browser is allowed to send cookies in the request. `Access-Controlw-Allow-Methods` specifies the specific type of HTTP request methods that are allowed to be made to this specific origin. In this case, you need all parts to make sure that your application is working smoothly in this specific context. Most modern backend frameworks like Express.js + Flask allow you to manually set the headers.

Note that an easy mistake is to try and set the header, `Access-Control-Allow-Origin: *`. Browsers do not send cookies to origins that have the `Access-Control-Allow-Origin` header set to `*` for security reasons. You will need to specify the specific origin of your frontend on your backend server.

But wait, isn't this a lot to remember? 😅 Luckily, all major backend frameworks implement a library that tries to abstract CORS away to make your life easier. If you are using Express.js, you can check out the [CORS middleware](https://expressjs.com/en/resources/middleware/cors.html) to easily integrate this into your project. Some students have had some trouble using this framework in the past. Feel free to come to office hours if you need some help. You could also implement the above mentioned headers manually as this is something that every CORS library does under the hood.

### How do we code this?

An example of using the fetch API is shown below to implement this. As you can see, we are able to use `credentials: 'include'` to send cookies. The part of this that indicates that we are allowed to send the request cross-origin is the special HTTP headers sent by the server.

```javascript=
fetch('http://localhost:5000/api/user', {
    method: 'POST',
    headers: {
        'content-type': 'application/json'
    },
    credentials: 'include',
    body: JSON.stringify({
        user: 'admin",
        pass: "password123'
    }),
});
```

A quick note about a solution that **does not** work. Examine the following code snippet bellow.

```javascript=
fetch('http://localhost:5000/api/user', {
    method: 'POST',
    headers: {
        'content-type': 'application/json'
    },
    credentials: 'include',
    mode: 'no-cors',
    body: JSON.stringify({
        user: 'admin',
        pass: 'password123'
    }),
});
```

Some folks may find some misleading posts online about using `mode: 'no-cors'` in order to bypass the Same-Origin Policy or CORS errors. The reason why this **does not** work is because `no-cors` **does not** send cookies and ignores the `credentials: 'include'` and defeats the whole point of trying to make these cross-origin requests for authentication.

### Is everything completely "fixed"?

Short answer is **no**. But this is enough information in order for you to be able to complete your CS 35L project. 🫠

Technically, it may be possible to avoid some of this while tackling your project by using a very hacky solution of sending your authentication tokens through the body of your request and avoid using cookies all together. If your group finds this easier, that should be okay for this project. However, this is the more precise solution.

If you are interested in **deploying** your app, you will need to speak to a TA or LA during office hours because you may need to configure **SameSite** cookies or **TLS**. If web security sounds cool to you, feel free to come talk to Benson during office hours. 🤗
