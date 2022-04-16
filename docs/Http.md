## Routing

The users of your website or web application can visit various URLS like /articles, /posts. To make these URLs available you will have to define them as routes.

You define routes inside `src/routes.ts`file using the `App` module(which is instance of the underlying express app).

A route typically accepts the route pattern as the first argument and route handler as the second argument. For example:

```javascript
import { App, loadController, Response, Request, withMiddleware } from '../config/core';

App.get("/", async (request: Request, response: Response) => {
  return response.status(200).send(`Hello world`);
});
```

The route handler can also reference a controller method via the loadController method.

```javascript
import { App, loadController, Response, Request, withMiddleware } from '../config/core';

App.get('/example', loadController('example.index'));
```

You can create chainable route handlers for a route path by using App.route(). For example:

```javascript
App.route('/articles')
  .get(async (request: Request, response: Response) => {});
  .post(async (request: Request, response: Response) => {});
  .put(async (request: Request, response: Response) => {});
```

## HTTP methods

You can create routes with commonly used HTTPs. For example:

### Post

```javascript
App.post('posts', async (request: Request, response: Response) => {});
```

### Put

```javascript
App.put('posts/:id', async (request: Request, response: Response) => {});
```

### Patch

```javascript
App.patch('posts/:id', async (request: Request, response: Response) => {});
```

### Delete

```javascript
App.delete('posts/:id', async (request: Request, response: Response) => {});
```

## Apply middleware to route

You can also pass middleware methods to your routes. Middleware methods will be executed each time a request is sent to the route. For example:

```javascript
App.post('posts', withMiddleware('auth'), async (request: Request, response: Response) => {});
```

You can also pass an array of middlewares to be applied to the route. For example:

```javascript
App.post('posts', withMiddleware(['auth', 'secondMiddleware']), async (request: Request, response: Response) => {});
```

The `withMiddleware()` method allows you to apply middlewares to a route without having to import the actual middleware method into your route file. Thus, keeping your route file slim.

Middlewares are created in the src/middlewares folder. Read more about how to create and register middlewares [here](#middleware).

### Create routes using Express.js' router module

You can also use express.js' router module to create routes. This is especially useful when you want to group routes or have more modularity. For Example:

First, import the module inside your route.ts file.

```javascript
import { Router } from '../config/core';
```

Then create your routes:

```javascript
const newsRouter = Router();
newsRouter.get('/', async (request: Request, response: Response)=> {});
App.use('/news', newsRouter);

// Create router and apply middleware to router - option 1
const newsRouter = Router();
newsRouter.use(withMiddleware('example'));

// Create router and apply middleware to router - option 2
newsRouter.get('/', async (request: Request, response: Response)=> {});
App.use('/news', newsRouter);
```

Read more about express.js' routing [here](https://expressjs.com/en/guide/routing.html).

## Controllers

Controllers are methods that handles the HTTP request for the routes defined inside the src/routes/index.ts file.

```javascript
import { Request, Response } from "../../config/core";

export async function index(request: Request, response: Response) { };

export async function store(request: Request, response: Response) { };

export async function show(request: Request, response: Response) { };

export async function edit(request: Request, response: Response) { };

export async function update(request: Request, response: Response) { };

export async function destroy(request: Request, response: Response) { };
```

Each controller method accepts a request context, `request`, and a response context, `response`.

The request context extends express.js' `ExpressRequest` interface, and adds an `Auth` property which provides details of the authenticated user if authentication is active within the application.

The response object is the regular express.js response object.

The `Auth` object on the request interface holds the following properties:

```javascript
{
    id: string;
    email: string;
    firstName: string;
    lastName: string;
}
```

## Request

The `request` object holds information about the current HTTP request made to the app. The interface for the`request` object is defined in `src/config/core.ts`. The `Request` interface extends the original express.js Request interface, adding the following properties:

- Auth: property which provides details of the authenticated user on authenticated endpoints/requests.

## Response

The `response` object allows you to send HTTP responses from your endpoint. The interface for the`response` object is defined in `src/config/core.ts`. It extends the original express.js `Response` interface but currently has no extra properties.

## Middleware

Middleware functions get executed during an HTTP request before it reaches the route handler.

Middleware functions are functions that have access to the `request` object (request), the `response` object (response), and the `next` middleware function in the application’s request-response cycle. The `next` middleware function is commonly denoted by a variable named `next`.

Middleware functions can perform the following tasks:

- Execute any code.
- Make changes to the request and the response objects.
- End the request-response cycle.
- Call the next middleware function in the stack.

If the current middleware function does not end the request-response cycle, it must call `next()` to pass control to the next middleware function. Otherwise, the request will be left hanging.

## Creating middlewares

You create your middlewares inside designated files in the `src/middlewares` folder. Here's an example middleware called 'example':

```javascript
// src/middlewares/example.ts

import { NextFunction } from "express"
import { Request, Response } from '../config/core'

export default class Example{
  public handler(req: Request, res: Response, next: NextFunction) {
    console.log("Example middleware");
    next();
  }
}
```

You then register your middleware inside the `src/middlewares/index.ts` file. For example:

First, import your middleware

```javascript
// src/middlewares/index.ts

import Example from './example';
Then register it by adding it to the middleware constant.

const middleware = {
  // ......,
  example: Example,
}
```

You then use the withMiddleware() method to use the middleware. For example:

```javascript
App.get('posts', withMiddleware('auth'), async (request: Request,response: Response) => {});
```

Note: you apply application level middlewares inside initialize() method in thesrc/app.ts file.
Read more about express.js middleware [here](https://expressjs.com/en/guide/using-middleware.html).