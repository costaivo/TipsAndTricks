# NestJS 102


## Add the required dependencies

``` bash
yarn add @nestjs/typeorm typeorm pg
```

or

``` bash
pnpm add @nestjs/typeorm typeorm pg
```

## Connecting to the database

### Create a entity **Task**

- create a file `task.entity.ts`
- add the following code the entity

``` typescript
@Entity()
export class Task{
    @PrimaryColumn()
    id:string;

    @Column()
    title:string;

    @Column()
    description:string;

    @Column()
    status:string;
}
```

- Open the app.module.ts file to add the connection details to connect to the database

``` typescript
@Module({

  imports: [  TypeOrmModule.forRoot({
    type: 'postgres',
    host: 'localhost',
    port: 5400,
    username: 'taskmgmt',
    password: 'taskmgmt',
    database: 'task',
    entities: ['dist/**/*.entity.{ts,js}'],
    synchronize: true,
}),TaskModule],
})
export class AppModule {}
```

- run the application. Connect to the pgAdmin and check if the table Task is created in the database

## Repository

### Repository file

- create a file `task.repository.ts`

``` typescript
import { EntityRepository, Repository } from "typeorm";
import { CreateTaskDto } from "./create-task.dto";
import { Task } from "./task.entity";

@EntityRepository(Task)
export class TaskRepository extends Repository<Task>{

    async createTask(createTaskDto: CreateTaskDto): Promise<Task> {
        const newTask = this.create({
      ...createTaskDto,
      status:'new'
        });
        await this.save(newTask);
        return newTask;
    }
}
```

### Repository Setup

task.module.ts

``` typescript
import { Module } from '@nestjs/common';
import { TypeOrmModule } from '@nestjs/typeorm';
import { EmployeeController } from './employee.controller';
import { EmployeeRepository } from './employee.repository';
import { EmployeeService } from './employee.service';

@Module({
  imports:[TypeOrmModule.forFeature([EmployeeRepository])
],
  controllers: [EmployeeController],
  providers: [EmployeeService]
})
export class EmployeeModule {}

```

### Using Docker and NestJS
[NestJS Docker](https://blog.logrocket.com/containerized-development-nestjs-docker/)


## Optimizing the Connection Settings
## TypeOrm Config file 

typeorm.config.ts

``` typescript
export const TypeOrmConfig: TypeOrmModuleOptions = {
    type: 'postgres',
    host: 'localhost',
    port: 5400,
    username: 'taskmgmt',
    password: 'taskmgmt',
    database: 'task',
    entities: ['dist/**/*.entity.{ts,js}'],
    synchronize: true,
}

```


## App Module Setup

app.module.ts

``` typescript

@Module({
  imports: [
    TypeOrmModule.forRoot(TypeOrmConfig),
    TaskModule],
})
export class AppModule {}

```