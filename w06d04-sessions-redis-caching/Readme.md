# Cookies, Session, Caching and Redis

## Learning Competencies
- Understand what cookies are
- Sessions: Browser session vs server session
- Learn about service workers
- Understand caching and it's uses
- Learn Redis

## Overview

### Cookies

Cookies are simple, small files/data that are sent to client with a server request and stored on the client side. Every time the user loads the website back, this cookie is sent with the request. This helps us keep track of the user’s actions.

The following are the numerous uses of the HTTP Cookies −

 - Session management
 - Personalization(Recommendation systems)
 - User tracking

To use cookies with Express, we need the cookie-parser middleware. To install it, use the following code −

```
npm install --save cookie-parser
```

Now to use cookies with Express, we will require the cookie-parser. cookie-parser is a middleware which parses cookies attached to the client request object. To use it, we will require it in our index.js file; this can be used the same way as we use other middleware. Here, we will use the following code.

```
var cookieParser = require('cookie-parser');
app.use(cookieParser());
```

cookie-parser parses Cookie header and populates req.cookies with an object keyed by the cookie names. To set a new cookie, let us define a new route in your Express app like −

```
var express = require('express');
var app = express();

app.get('/', function(req, res){
   res.cookie('name', 'express').send('cookie set'); //Sets name = express
});

app.listen(3000);
```

To check if your cookie is set or not, just go to your browser, fire up the console, and enter −

```
console.log(document.cookie);
```

You will get the output like (you may have more cookies set maybe due to extensions in your browser) −

```
"name = express"
```

The browser also sends back cookies every time it queries the server. To view cookies from your server, on the server console in a route, add the following code to that route.

```
console.log('Cookies: ', req.cookies);
```

Next time you send a request to this route, you will receive the following output.

```Cookies: { name: 'express' }```

### Adding Cookies with Expiration Time
You can add cookies that expire. To add a cookie that expires, just pass an object with property 'expire' set to the time when you want it to expire. For example,

```
//Expires after 360000 ms from the time it is set.
res.cookie(name, 'value', {expire: 360000 + Date.now()});
```

Another way to set expiration time is using 'maxAge' property. Using this property, we can provide relative time instead of absolute time. Following is an example of this method.

```
//This cookie also expires after 360000 ms from the time it is set.
res.cookie(name, 'value', {maxAge: 360000});
```

### Deleting Existing Cookies
To delete a cookie, use the clearCookie function. For example, if you need to clear a cookie named foo, use the following code.

```
var express = require('express');
var app = express();

app.get('/clear_cookie_foo', function(req, res){
   res.clearCookie('foo');
   res.send('cookie foo cleared');
});

app.listen(3000);
```

### How to use Cookies to manage sessions

HTTP is stateless; in order to associate a request to any other request, you need a way to store user data between HTTP requests. Cookies and URL parameters are both suitable ways to transport data between the client and the server. But they are both readable and on the client side. Sessions solve exactly this problem. You assign the client an ID and it makes all further requests using that ID. Information associated with the client is stored on the server linked to this ID.

We will need the Express-session, so install it using the following code.

```
npm install --save express-session
```

We will put the session and cookie-parser middleware in place. In this example, we will use the default store for storing sessions, i.e., MemoryStore. Never use this in production environments. The session middleware handles all things for us, i.e., creating the session, setting the session cookie and creating the session object in req object.

Whenever we make a request from the same client again, we will have their session information stored with us (given that the server was not restarted). We can add more properties to the session object. In the following example, we will create a view counter for a client.

```
var express = require('express');
var cookieParser = require('cookie-parser');
var session = require('express-session');

var app = express();

app.use(cookieParser());
app.use(session({secret: "Shh, its a secret!"}));

app.get('/', function(req, res){
   if(req.session.page_views){
      req.session.page_views++;
      res.send("You visited this page " + req.session.page_views + " times");
   } else {
      req.session.page_views = 1;
      res.send("Welcome to this page for the first time!");
   }
});
app.listen(3000);
```

What the above code does is, when a user visits the site, it creates a new session for the user and assigns them a cookie. Next time the user comes, the cookie is checked and the page_view session variable is updated accordingly.

Now if you run the app and go to localhost:3000, the following output will be displayed.

- If you revisit the page, the page counter will increase. The page in the following screenshot was refreshed 42 times.

### Service Workers

Web push and service workers enable exciting new ways to notify your users. You may have seen more of these lately.

Browser notifications have been around for a while, but what makes this different — you can notify users after they have left your page.

Service Workers make this possible, which is Javascript code that runs in the 
background in your browser.

There is some plumbing needed for Service Workers. We’ll make a NodeJS module that abstracts some of this and serves up the necessary code needed to make these work, all in one neat package.

Normally, some files are used to make this Service workers and web possible.

- a manifest.json file is put on the server which describes the web app
- a service-worker.js file which handles a push notification sent from a message provider (in Chrome browsers this is Google itself)
- an sw.js file which contains script to register the service worker in the browser

However, we’ll use some trickery, and bundle these all into a Node (server) module, that will serve up the code from these files. So, in our HTML we can simply use this:

push.html
```html
<link rel="manifest" href='manifest.json'>
<script src='sw.js'></script>
```

On the server (Express) we can just use this:

server.js
```js
var express = require('express');
var app = express();
var bodyParser = require('body-parser')
app.use(express.static('public'));
app.use(bodyParser.json())

var pushapp = require('./serviceworker-push.js')(app);

app.post('/register', function(r, s) {
    s.sendStatus(201);
 
    pushapp.push(u.endpoint, {
      title: 'Hi there!', 
      body: "I'm a web push notification", 
      icon: '', 
      badge: '', 
      actions: [
        {action: 'like', title: '👍Like'},
        {action: 'reply', title: 'Reply'}
      ]
    })

});

  
app.listen(process.env.PORT, function() {
  console.log('Node app is running on port', process.env.PORT);
});
```

In this example, it is pushing a notification immediately when visiting the page. The push normally would happen somewhere else, but could be called from anywhere.
Now for the serviceworker-push.js module that handles all the server-side files.


serviceworker-push.js

```js
var ajax = require('ajax.js')

module.exports = app => {

  var swp = {}

  swp.push = (endpoint, resp, cb) => {
    const id = endpoint.split('/').pop()
    var headers = {Authorization: `key=${process.env.GCM_SERVER_KEY}`, "Content-Type": "application/json", TTL:"10"}
    ajax.post('https://fcm.googleapis.com/fcm/send', {headers: headers, data: {"to": id, "data": {"message": "hi"}}}).then(mid => {
       console.log(mid)
       resp.messageid = mid
       app.get('/pushdata', (r, s) => s.end(JSON.stringify(resp)))
    }, console.error)
  }
  
  app.get('/sw.js', (r, s)=>{
    s.end(`
      var regserver = sub => {
        var muser = 'undefined' === typeof user ? null : user
        fetch('/register', { method: 'post', headers: { 'Content-type': 'application/json' }, body: JSON.stringify({user: muser, endpoint: sub.endpoint}) });
      }
      
      navigator.serviceWorker.register('/service-worker.js');
      navigator.serviceWorker.ready.then(reg => {
        reg.pushManager.getSubscription().then(sub => {
          if(sub) return regserver(sub)
          reg.pushManager.subscribe({userVisibleOnly: true}).then(regserver)
        })
      })
      
    `)
  })
  
  app.get('/service-worker.js', (r, s)=>{
    s.setHeader("Content-Type", 'text/javascript')
    s.end(`
      self.addEventListener("push", (event) => {
        fetch('/pushdata').then(r => r.text().then(options => {
           options = JSON.parse(options)
           self.registration.showNotification(options.title, options);
        }))
      });
      self.addEventListener('notificationclick', ev => {
        ev.notification.close()
        return clients.openWindow('/'+ ev.action)
      }, false);
    `)
  })
  
  app.get('/manifest.json', (r, s) => {
    s.end(`{
      "name": "Push Notifications",
      "short_name": "push Notifications",
      "start_url": "/index.html",
      "display": "standalone",
      "gcm_sender_id": "${process.env.GCM_SENDER_ID}",
      "gcm_user_visible_only": true,
      "permissions": ["gcm"]
    }`)
  })

  return swp
}
```

You need secret keys from the Google Developers Console which are stored in process.env. This module adds the routes to manifest.json, service-worker.js, and sw.js all wrapped into one. Your web push is ready to go.

Web push is an emerging technology, that you can read about here:

**https://developers.google.com/web/updates/2015/03/push-notifications-on-the-open-web**


### Redis

#### QUICK OVERVIEW

Redis (REmote DIctionary Server) is an in-memory, key-value database, commonly referred to as a data structure server. One of the key differences between Redis and other key-value databases is Redis’s ability to store and manipulate high-level data types. These data types are fundamental data structures (lists, maps, sets, and sorted sets) that most developers are familiar with. Redis’s exceptional performance, simplicity, and atomic manipulation of data structures lends itself to solving problems that are difficult or perform poorly when implemented with traditional relational databases.

#### COMMON USE CASES

Caching – Due to its high performance, developers have turned to Redis when the volume of read and write operations exceed the capabilities of traditional databases. With Redis’s capability to easily persist the data to disk, it is a superior alternative to the traditional memcached solution for caching.
Publish and Subscribe – Since version 2.0, Redis provides the capability to distribute data utilizing the Publish/Subscribe messaging paradigm. Some organizations have moved to Redis and away from other message queuing systems (i.e., RabbitMQ, zeromq) due to Redis’s simplicity and performance.

Queues – Projects such as Resque use Redis as the backend for queueing background jobs.

Counters – Atomic commands such as HINCRBY, allow for a simple and thread-save implementation of counters. Creating a counter is as simple as determining a name for a key and issuing the HINCRBY command. There is no need to read the data before incrementing, and there are no database schemas to update. Since these are atomic operations, the counters will maintain consistency when accessed from multiple application servers.
KEY FEATURES

High-Level Data Structures – Provides five possible data types for values: strings, lists, sets, hashes, and sorted sets. Operations that are unique to those data types are provided and come with well documented time-complexity (Big O notation).

![Redis](images/ds-redis.png)


High Performance – Due to its in-memory nature, the project maintainer’s commitment to keeping complexity at a minimum, and an event-based programming model, Redis boasts exceptional performance for read and write operations.
Lightweight With No Dependencies – Written in ANSI C, and has no external dependencies. Works well in all POSIX environments. Windows is not officially supported, but an experimental build is provided by Microsoft.
High Availability – Built-in support for asynchronous, non-blocking, master/slave replication to ensure high availability of data. There is currently a high-availability solution called Redis Sentinel that is currently usable, but is still considered a work in progress.

#### COMPANIES USING REDIS

Twitter – Deployed a massive Redis cluster to store the timelines for all users. Utilizing the list data structure, Twitter stores the 800 most recent incoming tweets for a given user. View the presentation given by Twitter on how they distribute timelines at scale.

Pinterest – Stores the user follower graphs in a Redis cluster where data is sharded across hundreds of instances. Pinterest turned to Redis after finding that their original solution of MySQL and memcached was reaching its limits. More on how Pinterest is using Redis.

Github – An early adopter of the Redis project, Github has developed and open-sourced the library, Resque, to facilitate the execution of background jobs that have been placed on a queue. Github developers took advantage of the fact that Redis had solved many of the difficult queueing issues, so the developers could stay focused on the difficult worker scheduling issues. More on how Github uses Redis for their job queueing needs.

##### TAKE AWAY

The combination of high-level data structures, high performance, and overall intuitiveness allow Redis to fill the role as the Swiss Army knife of data stores for a developer.

Redis is well suited to solving challenges encountered when developing real-time systems, thanks to the predictability of operations applied to the data in the database.

Redis attributes much of its performance to that fact that the data always resides in-memory. Data can be persisted to disk, but incorrect configuration could lead to data loss when Redis is shutdown improperly.


## Exploration
- [Learn Redis Commands](https://redis.io/commands)
- [Memcached](https://www.memcached.org)
- [Service workers](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API)
- [Cookie Session npm](https://www.npmjs.com/package/cookie-session)
