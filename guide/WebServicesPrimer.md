# An HTTP and Web Services Primer

Most of the web as we know it, and as applies to the common usage of server side development, is built upon only a few main technologies.

This document will attempt to provide an overview of some of these as they relate to Perfect, server-side Swift, and general application programming interface (API) development.

### What Is an API?

An API is a bridge between two systems. It generally accepts a standardized type and style of input, transforms it internally, and works with another system to perform an action or return information.

An example of an API is GitHub's API: most will be familiar with using GitHub's web interface, but they also have an API which allows applications such as task and issue management systems like [Jira](https://www.atlassian.com/software/jira) and continuous integration (CI) systems such as [Bamboo](https://www.atlassian.com/software/bamboo) to link directly to your repositories to **provide greater functionality than any one system could by itself.**

An API like these are generally built upon HTTP or HTTPS.

### HTTP and HTTPS

"HTTP" is the common acronym for "HyperText Transfer Protocol". It is is a set of standards that allows users to publish multimedia documents on the internet and exchange information they find on webpages.

"HTTPS" is a protocol for secure, encrypted HTTP communications. Each end of the communication agrees on a trusted "key" that encrypts the information being transmitted, with the ability to decrypt it on receipt. The more complex the security, the harder it is for a third party to intercept and read it, and potentially change it.

When you access a website in your browser, it will likely be over HTTP, or if the "lock" shows it will be using HTTPS.

When an iOS or Android application accesses a backend server to get information such as the most recent weather report, it is using HTTP or, more likely, HTTPS.

## The General Form of an API

### Routes

An API consists of "routes", which are similar to directory/folder name paths through a file system to a document. Each route points to a different action you wish to perform. A "show me a list of users" route is different from a "create a new user" route.

In an API, these routes will often not exist as actual directories and documents, but as pointers to functions. The "directory structure" implied is usually a way of logically grouping functionality. For example:

```
/api/v1/users/list
/api/v1/users/detail
/api/v1/users/create
/api/v1/users/modify
/api/v1/users/delete

/api/v1/companies/list
/api/v1/companies/detail
/api/v1/companies/create
/api/v1/companies/modify
/api/v1/companies/delete
```

This example illustrates a typical "CRUD" system. "CRUD" means "Create, Read, Update, Delete". The difference between the two groups seems to only be the `users` versus `companies` part, but they will point to different functions.

Additionally, the "detail", "modify", and "delete" routes will include a unique identifier for the record. For example:

```
/api/v1/users/list
/api/v1/users/detail?id=5d096846-a000-43db-b6c5-a5883135d71d
/api/v1/users/create
/api/v1/users/modify?id=5d096846-a000-43db-b6c5-a5883135d71d
/api/v1/users/delete?id=5d096846-a000-43db-b6c5-a5883135d71d
```

The example above shows these routes passing to the server an id parameter that relates to a specific record. The list and create routes don't have an id because for those routes an id parameter is irrelevant.

In addition to routes, a request to each of these individual routes will include an HTTP "verb".

### HTTP Verbs

HTTP verbs are additional pieces of information that a web browser or mobile application client supplies with every request to a route. These verbs can give additional "context" to the API server as to the nature of the data being received.

Common HTTP verbs include:

#### GET 
The GET method requests a specified route, and the only parameters passed from client to server are in the URL. Requests using GET should only retrieve data and have no other effect. Using a GET request to delete a database record is possible but it is not recommended.

#### POST
Normally used for sending info to create a new record in a database, a POST request is what normally gets submitted when you fill in a form on a website. The name-value pairs of the data are submitted in a POST request's "POST Body" and are read by the API server as discrete pairs.

#### PATCH
PATCH requests are generally considered to be the same as a POST but they are used for updates.

#### PUT 
Used predominantly for file uploads, a PUT request will include a file in the body of the request sent to the server.

#### DELETE 
A DELETE request is the most descriptive of all the HTTP verbs. When sending a DELETE request you are instructing the API server to remove a specific resource. Usually some form of uniquely identifying information is included in the URL.


### Using HTTP Verbs and Routes to Simplify an API

When used together, these two components of a request can reduce the perceived complexity of an API.

Looking at the following structure with the HTTP verb followed by a URL, you will see that the process is much simpler and more specific:


```
GET     /api/v1/users
GET     /api/v1/users/5d096846-a000-43db-b6c5-a5883135d71d
POST    /api/v1/users
PATCH   /api/v1/users/5d096846-a000-43db-b6c5-a5883135d71d
DELETE  /api/v1/users/5d096846-a000-43db-b6c5-a5883135d71d
```

At first glance, all seem to be pointing to the same route: `/api/v1/users`. However, each route performs a different action. Similarly, the difference between the first two GET routes is the id appended as a URL component, not a parameter as shown in the earlier example. This command is usually achieved with a "wildcard" that is specified in the route setup.

The next step is to review the [Handling Requests](handlingRequests.md) chapters in the Perfect documentation. These sections will outline how to implement routes pointing to functions and methods, how to access information passed to the API server from a frontend, and whether it be a web browser or a mobile application.
