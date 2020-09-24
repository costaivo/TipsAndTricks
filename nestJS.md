# NestJS

## Installing nestJS

``` cmd
# using npm 
npm install -g @nestjs/cli

# Using Yarn
yarn global add @nestjs/cli
```

## Creating new Application

``` cmd
# Creating a new application 
nest new training-tracker-api
```

## Generating Components using CLI


### Create new Module
``` cmd
nest g module employee
```

### Create new Service
``` cmd
nest g service employee
```

### Create new Controller
``` cmd
nest g controller employee
```


### Installing Typeorm & Postgres drivers

``` cmd
yarn add @nestjs/typeorm typeorm pg
```

## Running the project
``` terminal
# run with watch enabled
yarn start:dev
npm start:dev

# run without watch
yarn start
npm start
```

[NestJS Docker](https://blog.logrocket.com/containerized-development-nestjs-docker/)

