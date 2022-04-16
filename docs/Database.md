# Introduction

Database operations are an important part of most APIs. Tode aims to make database operations with node.js a simple process.
As such, Tode is bundled with an ORM(Objection.js), and a query builder(Knex.js).

## ORM

Tode leverage's the Objection.js ORM, and Knex.js query builder.

Tode leverage's the [Objection.js](https://vincit.github.io/objection.js/) ORM to perform your needed database operations.

***Here's what the official docs has top say about Objection.js***

> Objection.js is an ORM for Node.js that aims to stay out of your way and make it as easy as possible to use the full power of SQL and the underlying database engine while still making the common stuff easy and enjoyable.
> Even though ORM is the best commonly known acronym to describe objection, a more accurate description is to call it a relational query builder. You get all the benefits of an SQL query builder but also a powerful set of tools for working with relations.
> Objection.js is built on an SQL query builder called knex. All databases supported by knex are supported by objection.js. SQLite3, Postgres and MySQL are thoroughly tested.

The underlying database connection is setup up using Knex.js, and the top level ORM functionalities are done using Objection.js.

## Knex.js

Tode uses knex.js to create migrations and seeders. All knex.js commands are available in tode.js via `npx knex <command_name> <args>`

## Configuring database

Install one of the following drivers depending on the database you want to use
npm install pg
npm install sqlite3
npm install mysql
npm install mysql2
Note: The pg client comes installed by default. You'll need to have PostgresQL on your machine to use it.

Update the following variables to your `.env` file

```
IS_DB_ENABLED=true
DB_NAME=
DB_CLIENT=postgresql # postgresql is the default
DB_PORT=
DB_PASSWORD=
DB_USER=
DB_HOST=
```

These variables are used to create a `database` object in the `.env.ts` file that hold that loads in environment specific values.

```javascript
// The 'database' object create in the 'env.ts`
const database = {
  isEnabled: (processEnv.IS_DB_ENABLED as unknown) as boolean,
  client: processEnv.DB_CLIENT,
  name: processEnv.DB_NAME,
  user: processEnv.DB_USER,
  password: processEnv.DB_PASSWORD,
  port: Number.parseInt(processEnv.DB_PORT, Base10),
  host: processEnv.DB_HOST,
  seeds: {
    directory: 'src\\database\\seeders'
  },
  migrations: {
    tableName: "knex_migrations",
    directory: 'src\\database\\migrations',
  },
};

```

Behind the scenes, tode then use the `database` object to configure a Knex.js connection. The configuration file, found in `src/config/database/db-config.ts` is as follows:

```javascript
// src/config/database/db-config.ts

import Knex from 'knex';
import { Model } from 'objection';
import { env } from '../../../env';

const DATABASE = env.database;
const connection = {
  client: DATABASE.client,
  connection: {
    database: DATABASE.name,
    user: DATABASE.user,
    password: DATABASE.password,
    port: DATABASE.port,
    client:DATABASE.host,
  },
  seeds: DATABASE.seeds,
  migrations: DATABASE.migrations,
};

function initializeDatabase() {

  // Initialize knex.
  // Bind all Models to a knex instance.
  if (env.database.isEnabled) {
    const knex = Knex(connection);
    Model.knex(knex);
  }
}
export { initializeDatabase, connection };
```

The `db-config.ts` file exports an object, `connection`, which is the used in the `/knexfile.ts` file to create a connection with the database. You can read more about the knexfile [here](http://knexjs.org/#knexfile).

## Models

Models within a tode generated application are provided via the Objection.js ORM.

A Model subclass represents a database table and instances of that class represent table rows. Models are created by inheriting from the `BaseModel` class which inturn inherits from the Objection.js `Model` class.

A Model class can define relationships (aka. relations, associations) to other models using the static relationMappings property.

Each model has a property named `tableName` which holds the name of the table that the model maps to. This property is a required property on all models. Below is an example of a model.

```javascript
import BaseModel from "../BaseMode";

export class Example extends BaseModel {
  // Table name is the only required property.
  public static tableName = 'example';

  public name!: string;
  // This object defines the relations to other models.
  // https://vincit.github.io/objection.js/guide/relations.html
  public static relationMappings = () => ({
    // specify relation with other modules
  })
}
```

## Important  Objection.js topics

- [Models](https://vincit.github.io/objection.js/guide/models.html)
- [Relationship](https://vincit.github.io/objection.js/guide/relations.html)
- [Querying](https://vincit.github.io/objection.js/guide/query-examples.html#basic-queries)
- [Transactions](https://vincit.github.io/objection.js/guide/transactions.html)

## Migrations

Migrations allows you to create, and update your database schema as the application develops over time.

You created and use migrations in a tode app via the knex.js CLI. Example:

``` npx knex migrate:latest ```

The command above will executed available migrations.

Each migration file is used to introduce some change to your database schema. A general flow of the usage of migrations goes like the following:

- Within the migration file, you define the statements for the schema change that the migration should introduce.

- Run migrations using the knex.js command-line tool.

- Executed migrations are tracked. Thus ensuring that each migration is executed only once.

- During development, you can also roll back migrations to edit them.

### Creating your first migration

You can create a new migration by running the following knex.js command. The migration files are stored inside the `src/database/migrations` directory.

```bash
npx knex migrate:make users

# CREATE: database/migrations/20210918225359_user.ts
```

The migration filename is prefixed with the current timestamp so that the migration files are sorted in the order created.

### Migration file structure

Each migration is created with two exported functions, `up` and `down`.

- The `up` method is used to introduce new changes to the database schema.
Usually, you will create new tables/indexes or alter existing tables inside this method.

- The `down` method is used to roll back the changes made to the schema by the `up` method. For example, if the up method creates a table, the down method should drop the same table.

Each  method receives an object, `knex`, as an argument. This `knex` object has the schema builder that use to construct your queries.

```javascript
import { Knex } from "knex";

export async function up(knex: Knex): Promise<void> {
  return knex.schema
    .createTable('users', (table) => {
      table.increments('id').primary();
      table.string('email').unique();
      table.string('firstName');
      table.string('lastName');
    });
}

export async function down(knex: Knex): Promise<void> {
  return knex.schema
   .dropTableIfExists('users');
}
```

### Run & rollback migrations

Once you have created the migration files you need, you can run the following Knex command to run migrations. For example, the `migrate:latest`  command executes the up method on all the migration files.

```
npx knex migrate:latest
```

#### Rollback migrations

You can roll back migrations by running the `migrate:rollback` command. The rollback action is performed on the migrations from the most recent batch.

```
npx knex migrate:rollback
```

You can also tell Knex to rollback all migrations. For example:

```
npx knex migrate:rollback true
```

You can also specify the name of the migration which you want to roll back.

```
knex migrate:down 20210918225359_user.ts
```

***Learn more about Knex.js migrations [here](https://knexjs.org/#Migrations)***.

## Seeders

Seeding allows you to set up your application with some initial data required to run and use the application. For example:

- A seeder to insert user roles before deploying and running your application.

- Or a seeder to insert sample users inside the database for local development.

The seeders are stored inside the `src/database/seeders` directory. You can create a new seeder file by running the following Knex command.

```
npx knex seed:make user

# CREATE: src/database/seeders/user.ts
```

The scaffolded seeder file looks like the following:

```javascript
import { Knex } from "knex";

export async function seed(knex: Knex): Promise<void> {
    // Deletes ALL existing entries
    await knex("table_name").del();

    // Inserts seed entries
    await knex("table_name").insert([
        { id: 1, colName: "rowValue1" },
        { id: 2, colName: "rowValue2" },
        { id: 3, colName: "rowValue3" }
    ]);
};

```

We can update the seeder file to insert a user into the users table. For example:

```javascript
import { Knex } from "knex";

export async function seed(knex: Knex): Promise<void> {
    // Deletes ALL existing entries
    await knex("users").del();

    // Inserts seed entries
    await knex("users").insert([
        { email: 'user@example.com', firstName: 'John', lastName: 'Doe'  },
    ]);
};
```

### Running seeders

You can execute all database seeders by running the following Knex command.

```
npx knex seed:run
```

You can run a specific seeder. For example:

```
npx  knex seed:run user.ts
```
