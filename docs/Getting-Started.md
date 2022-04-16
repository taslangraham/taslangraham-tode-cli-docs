# Meet Tode CLI

Tode CLI (often shorten to simply Tode) is a CLI that wraps around the popular express.js framework. Tode provides a scaffolded approach to building node.js/express.js APIs. The Tode CLI comes packed with features that aims to speed up your development with node/express.js.

Whether you are experienced or new to node/express, Tode makes developing APIs fast, simple, and elegant.

## Prerequisites

Tode is a Node.js tool, and hence it requires Node.js to be installed on your computer. To ensure a smooth, bug free experience, we need at least the latest release of Node.js v14.

You can check the Node.js and npm versions by running the following commands.

```Bash
# check node.js version
node -v
```

If you don't have Node.js installed, you can download the binary for your operating system from the official website.

## Installation

With at least the latest release of Node.js v14 installed oin your machine, you can go ahead and install Tode.

```bash
npm install -g tode-cli
```

## Creating a new project

Creating a new project is simple.

```bash
tode create-project hello-world
```

### Project Structure

## Starting the development server

After creating the application, you can start the development server by running the following command.

```bash
npm run dev
```

The above command will start the HTTP server. Nodemon is used to start the development server to watch changes to the file system and automatically restart the server.

## Environment Configuration

A Tode generated project utilizes a single env file to store environment variables that changes between your local and production environment. For example, JWT secret key, database credentials, and so on. You define these variable in a single ```.env``` file.

### Access environment variables

Node.js allows you to access the environment variables using the process.env object. For example:

```Bash
process.env.NODE_ENV
process.env.HOST
process.env.PORT
```

Tode sticks to this style of reading environment variables. However, all environment variables are read in a single location, and made available to the application via a single module, ```env```, exported from ```.env.ts``` found in the root directory of the app.

The following is a snippet of the default ```env.ts``` module.

```javascript
/**
 * Loads in application environment variables
 */
import { config as initEnv } from 'dotenv';
import { Base10 } from "./src/config/constants";
initEnv();

interface Process extends NodeJS.ProcessEnv {
  DB_NAME: string;
  DB_CLIENT: string;
  DB_PORT: string;
  DB_PASSWORD: string;
  DB_USER: string;
  JWT_SECRET: string;
  IS_DB_ENABLED: string;
  DB_HOST: string;
}

const processEnv = process.env as Process;

// Configuration properties for database
const database = {
  isEnabled: (processEnv.IS_DB_ENABLED as unknown) as boolean,
  client: processEnv.DB_CLIENT,
  name: processEnv.DB_NAME,
  user: processEnv.DB_USER,
  password: processEnv.DB_PASSWORD,
  port: Number.parseInt(processEnv.DB_PORT, Base10),
  host: processEnv.DB_HOST,
  // .....,
};

const projectDirectory = __dirname;

export const env = {
  database,
  JWT_SECRET: processEnv.JWT_SECRET,
  projectDirectory,
};
```

### Using the env module

```javascript
// src/example.ts

import { env } from './env'

// Get database port
const databasePort = env.database.port
```
