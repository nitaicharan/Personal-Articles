# Protecting resources with the Node ACL module in Node.js

Created: May 4, 2022 10:51 AM
Finished: No
Source: https://blog.codecentric.de/en/2018/07/protecting-resources-with-node_acl-module-in-nodejs/
Subjects: security
Tags: #nodejs

## What is NODE ACL and why should you use it?

Well, if you are looking for a flexible and elegant way to protect specific resources in your application, Node ACL (Access Control List for Node) is a module that can solve your problem, providing a smooth way to create roles and permissions, and assign those roles to specific users. After that, with node_acl middleware, which is express-compatible middleware, you can protect your resources.

Node ACL can be used with Redis, MongoDB and in-memory-based backends. It is also applicable to other third-party
 backends such as Knex, Firebase, and Elasticsearch.

In this blog, I will show you some examples using MongoDB as a backend and a passport as authentication middleware.

## Getting started

To get started with Node ACL, first you’ll need your database instance, and then you can create your ACL module by requiring and instantiating the module. Since I’m using MongoDB, below you can see how to create an ACL module.

```
const node_acl = require('acl');
const mongodb = require('mongodb');
```

In your config file, you can specify the database host, port, and the name of your database, and then create the database URL:

```
const config = require('./config.js');
const databaseURI = `mongodb://${config.database.host}:${config.database.port}/${config.database.name}`;
```

After that, you can instantiate your ACL module with the backend instance:

```
let acl = null;
mongodb.connect(databaseURI, (error, db) => {
 if (error) {
   throw error;
 }
 acl = new node_acl.mongodbBackend(db, '_acl');
});
```

After instantiating your ACL module, we can start with assigning permissions to roles.
 It can be done in multiple ways.

```
acl.allow('admin', '/api/users', ['GET', 'POST', 'PUT', 'DELETE'], error => {
 if (error) {
   console.log('Error while assigning permissions');
 }
 console.log('Successfully assigned permissions to admin role');
});
```

With these lines of code we say that the admin user is allowed to do GET, POST, PUT and DELETE operation on users.
 ‘allow’ is a function that returns the promise and optionally can take callback with error parameter.

Another way to assign permissions to roles is shown in the code below:

```
acl.allow([
  {
    roles: ['user'],
    allows: [
      {
        resources: ['/api/events', '/api/categories'],
        permissions: ['get', 'post', 'put', 'delete'],
      },
    ],
  },
  {
    roles: ['admin'],
    allows: [
      {
        resources: ['/api/users'],
        permissions: ['get', 'post', 'put', 'delete'],
      },
    ],
  },
]);
```

You usually use this method when you need to set permissions on many different roles and resources. By looking at the code above, you can tell that the user has permissions over more resources than the admin, which does not make sense, but we will handle that in next line of code.

```
acl.addRoleParents('admin', 'user');
```

*addRoleParents* is a function you can use to tell that every admin is allowed to do what users can do.
 If you want to remove role parents, you can do that with the *removeRoleParents* function and it should look like this:

```
acl.removeRoleParents('admin', 'user', err => {
   if (err) {
     console.log(err);
   } else {
     console.log('Role parents successfully removed.');
   }
});
```

Also, if you’re not satisfied with the roles and permissions you’ve created, this can be easily deleted:

```
acl.removeAllow('user', ['/api/events', '/api/categories'], err => {
   if (err) {
     console.log(err);
   } else {
     console.log('Allow user removed');
   }
});
```

After assigning various permissions to our roles, we should now assign roles to users, which can be done with the following piece of code.

```
user
  .find()
  .exec()
  .then(users => {
    users.forEach(user => {
      acl.addUserRoles(user._id.toString(), user.role, err => {
        if (err) {
          console.log(err);
        }
        console.log('Added', user.role, 'role to user', user.firstName, 'with id', user._id);
      });
    });
  });
```

Output:
 // Added user role to user Pero with id 5b263f5319703219ab25fb1b
 // Added admin role to user Vladislav with id 5b1fa76f5acb3a07897458b2

With this code, we are finding all users in our database and then going through
 the list of users and assigning roles to every user.

Afterwards, Node ACL gives us the possibility to check what we did so far. If we want to check which users we have given access to resources, and what are those resources, we can do this easily using the *allowedPermissions* function:

```
user
  .find()
  .exec()
  .then(users => {
    users.forEach(user => {
      acl.allowedPermissions(user._id.toString(), ['/api/users', '/api/events', '/api/categories'], (_, permissions) => {
        console.log(user.firstName, ' ', user.role);
        console.log(permissions);
      });
    });
  });
```

*acl.allowedPermissions* is a function that returns all permissions that we granted specific users to access given resources. As the first parameter, it takes a user id as a string, and the second parameter is an array of resources that we defined.
 The last parameter (which is optional) is a callback which you can use to show permissions.
 Output:

```
Vladislav   admin
{ '/api/categories': [ 'delete', 'get', 'post', 'put' ],
  '/api/events': [ 'delete', 'get', 'post', 'put' ],
  '/api/users': [ 'delete', 'get', 'post', 'put' ] }
```

```
Pero   user
{ '/api/categories': [ 'delete', 'get', 'post', 'put' ],
  '/api/users': [],
  '/api/events': [ 'delete', 'get', 'post', 'put' ] }
```

From the output we can see that Vladislav the admin has permissions to all categories, events and users resources, but Pero, who is a user, only has permissions to access categories and events resources, just like we specified with the *acl.allow()* function.

## Creating Node ACL middleware

All lines of code that we have written so far are part of the node_acl configuration. It’s really simple, right?

The fun begins with in the following part. What we are going to do next is to apply node_acl middleware to routes and then test to see if we have configured node_acl in the right way.

```
const NUM_PATH_COMPONENTS = 2;

router.get('/api/users/', checkForPermissions(), userController.getAllUsers);

function checkForPermissions() {
  return acl.middleware(NUM_PATH_COMPONENTS, getUserId);
}

function getUserId(req) {
  if (req.user) {
    return req.session.passport.user;
  }
}
```

The *checkForPermissions* function will return ACL middleware which should protect the resource that is named with *req.url* (it is really important to specify resources in the *acl.allow()* function in the way you use them in the router, because otherwise the ACL middleware will not operate in the right way), then it will take a user from the session and check the permission for req.method. In my case, I’m using the *getUserId* function to get the user ID, since I’m using a passport for the user authentication. NUM_PATH_COMPONENTS is a constant that you can use to set a number of components in the URL to be considered as part of the URL.
 If I set NUM_PATH_COMPONENTS to be 1, then ACL middleware will only take ‘/api’ as the URL.

If everything goes smoothly, you should get all users. But if you try to get users by sending a request with the user
 that isn’t an admin, you’ll get the following error:

```
error: {
  message: 'Insufficient permissions to access resource'
}
```

Another way to do this is by using the *acl.isAllowed(userId, resource, permissions, (error, allowed))* function which will check if the user is allowed to access the resource for the given permissions.

```
function checkPermissions(req, res, next) {
 if (req.user) {
   acl.isAllowed(
     req.session.passport.user.toString(),
     req.url, req.method.toLowerCase(), (error, allowed) => {
       if (allowed) {
         console.log('Authorization passed');
         next();
       } else {
         console.log('Authorization failed')
         res.send({ message: 'Insufficient permissions to access resource' })
       }
     });
 } else {
   res.send({ message: 'User not authenticated' })
 }
}
```

This function will first check if the user exists and then it will use the *acl.isAllowe*d function to protect the resource. As the first parameter, the *isAllowed* function takes *userId*, the second parameter is the resource that it should protect, then the third parameter is the requested method, and as the last parameter it takes the callback function which takes two parameters: error and allowed.

If an allowed parameter is true, then authorization has passed, and if it is not, authorization has failed.

## Conclusion

As I showed in this blog post, a Node ACL module is really simple and easy, and that’s all you should know about it. The important thing that we’ve seen is that it provides you with a nice way to protect resources in your application.
 I hope this blog will help you with the implementation of the *node_acl* module.