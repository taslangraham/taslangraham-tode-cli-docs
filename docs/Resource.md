# Introduction

When developing an app you usually have different resources that the app operates on.

A resource could be "Cars". With that you find yourself needing to create:

- car controller
- car model
- car service(to store your logic)
- routes like `/car`, `/car/:id`

Tode provides a command, `add:resource`, that allows you to scaffold a controller, model, and a service file for a particular resources.

```
tode add:resource car
```

 The above command will create the following files.

- src/controllers/car/index.ts
- src/models/car/index.ts
- src/services/car/index.ts
