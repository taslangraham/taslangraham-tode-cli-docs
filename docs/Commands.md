# Introduction

Tode CLI provides a number of commands to make your development faster and easier.

The available commands are as follows.

- `tode create-project <name>`
- `tode add:controller <name>`
- `tode add:model <name>`
- `tode add:resource <name>`
- `tode add:service <name>`
- `tode add:auth`
- `tode help <command_name>`

## Create Project

Scaffolds a fresh Tode project.

```$ tode create-project <name>```

```
USAGE
  $ tode create-project PROJECT NAME

ARGUMENTS
  PROJECT NAME  Name of project to create

OPTIONS
  -h, --help  show CLI help

EXAMPLE
  tode create-project <name>
```

## Create Controller

Adds a controller to the application.

```$ tode add:controller <name>```

```
USAGE
  $ tode add:controller name

ARGUMENTS
  name  Name of controller to create

OPTIONS
  -h, --help  show CLI help

ALIASES
  $ tode ac

EXAMPLE
  tode add:controller <name>
```

## Create Model

Adds a new Database Model.

```$ tode add:model <name>```

```
USAGE
  $ tode add:model name

ARGUMENTS
  name  Name of model to create

OPTIONS
  -h, --help  show CLI help

ALIASES
  $ tode am

EXAMPLE
  tode add:model <name>

```

## Create Resource

Adds a complete resource (model, controller, service).

```$ tode add:resource```

```
USAGE
  $ tode add:resource name

ARGUMENTS
  name  Name of resource to create

OPTIONS
  -h, --help  show CLI help

ALIASES
  $ tode rs

EXAMPLE
  tode add:resource <name>
```

## Create Service

Adds a new service file.

```$ tode add:service name```

```
USAGE
  $ tode add:service name

ARGUMENTS
  name  Name of service to create

OPTIONS
  -h, --help  show CLI help

ALIASES
  $ tode as

EXAMPLE
  tode add:service <name>
```

## Add Authentication

Adds JWT authentication.

`$ tode add:auth`


```bash
USAGE
  $ tode add:auth

OPTIONS
  -h, --help  show CLI help

ALIASES
  $ tode aa
```

### Modules generated

Controller - ```controllers/auth```

Model - ```models/user```

Services - ```services/auth```, ```services/user```

Middlewares - ```middlewares/auth```

### **Execute migration**

The necessary migration file to create a Users table in the database is also generated. Run the following command to create the Users table.

```bash
knex migrate:latest
```

## Help Command

Display help for tode.

```
USAGE
  $ tode help [COMMAND]

ARGUMENTS
  command  The command to show help for

OPTIONS
  --all  see all commands in CLI
```
