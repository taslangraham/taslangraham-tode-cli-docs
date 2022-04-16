# Introduction

We keep the bulk of our logic inside service files. This keeps our controllers slim.

Below shows how service files fits into a regular HTTP request flow:

- App receives HTTP request
- Request gets passed to handler(controller)
- Controller calls necessary service files to perform logic. Service file uses Models to query database if necessary
- Service file returns result to controller
- Controller process response and continue handling HTTP request

## Creating service files

To create a new service file you run the `add:service` tode command. Services are store in `src/services` directory.

```
tode add:service example

# CREATE: src/services/example/index.ts
```

This will generate a file like the following:

```javascript
import { ServiceReponse } from "../../config/constants";

class ExampleService {
  private _foo = "foo";

  constructor() {
    //
  }

  get foo() {
    return this._foo;
  }

  set foo(val: string) {
    this._foo = val;
  }

  public foobar() {
    //
  }
}

const exampleService = new ExampleService();

export { exampleService };

```

You then add the necessary methods to the `ExampleService` class.