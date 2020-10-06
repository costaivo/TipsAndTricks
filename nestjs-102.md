# NestJS 102

## TypeOrm Config file 

``` typescript
export const TypeOrmConfig: TypeOrmModuleOptions = {
    type: 'postgres',
    host: 'localhost',
    port: 5432,
    username: 'postgres',
    password: 'sa123456#',
    database: 'training-Tracker-DB',
    entities: [__dirname + '/../**/*.entity.{ts,js}'],
    synchronize: true,
}

```


## App Module Setup

app.module.ts

``` typescript
import { Module } from '@nestjs/common';
import { TypeOrmModule } from '@nestjs/typeorm';
import { TypeOrmConfig } from './config/typeorm.config';
import { EmployeeModule } from './employee/employee.module';


@Module({
  imports: [
    TypeOrmModule.forRoot(TypeOrmConfig),
    EmployeeModule],
})
export class AppModule {}

```


## Entity


employee.entity.ts

``` typescript
import { IsEmail } from "class-validator";
import { BaseEntity, Column, Entity } from "typeorm";
import { PrimaryGeneratedColumn } from "typeorm/decorator/columns/PrimaryGeneratedColumn";

@Entity()
export class Employee extends BaseEntity{
    @PrimaryGeneratedColumn()
    id: number;

    @Column({unique: true})
    @IsEmail()
    emailAddress: string;

    @Column()
    firstName: string;

    @Column({ nullable: true })
    middleName: string;

    @Column()
    lastName: string;
}
```

## Repository

### Repository file

employee.repository.ts

``` typescript
import { EntityRepository, Repository } from "typeorm";
import { Employee } from "./employee.entity";
import { v4 as uuidv4 } from 'uuid';
import { CreateEmployeeDto } from "./dto/create-employee.dto";
import { BadRequestException } from "@nestjs/common";


@EntityRepository(Employee)
export class EmployeeRepository extends Repository<Employee> {

    async createEmployee(createEmployeeDto: CreateEmployeeDto): Promise<Employee> {

        const { firstName, lastName, emailAddress, } = createEmployeeDto;

        const employee = new Employee();
        employee.emailAddress = emailAddress;
        employee.firstName = firstName;
        employee.lastName = lastName;
        await employee.save();
        return employee;

    }

    async getEmployees(): Promise<Employee[]> {
        const query = this.createQueryBuilder('employee');
        const employees = await query.getMany();
        return employees;
    }
}
```

### Repository Setup

employee.module.ts

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
