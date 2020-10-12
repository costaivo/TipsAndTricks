# Training Tacker  Application

## Adding API documentation

### Required Packages

``` bash
# using npm
npm install --save @nestjs/swagger swagger-ui-express

#using yarn
yarn add @nestjs/swagger swagger-ui-express
```

### Bootstrap

`main.ts` file
```typescript
    async function bootstrap() {
      const app = await NestFactory.create(AppModule);
      
      const options = new DocumentBuilder()
      .setTitle('Training Tracker')
      .setDescription('The API for Training Tracker Application')
      .setVersion('0.1')
      .build();
    const document = SwaggerModule.createDocument(app, options);
    SwaggerModule.setup('api', app, document);

      await app.listen(4200);
    }
    bootstrap();

```


### Adding Annotations for DTO and entity for spitting documentation

`base-emplooyee.dto.ts` file
``` typescript
  
import { IsEnum, IsISO8601, IsNotEmpty, IsNumberString, IsOptional } from "class-validator";
import { Designation } from "../employee.entity";
import {ApiProperty} from '@nestjs/swagger';

export class BaseEmployeeDto{
    @ApiProperty()
    @IsNotEmpty()
    firstName:string;

    @ApiProperty({required:false})
    @IsOptional()
    middleName:string;

    @ApiProperty()
    @IsNotEmpty()
    lastName:string;

    @ApiProperty({required:false})
    @IsOptional()
    teamName:string;
    
    @ApiProperty({enum:Designation,default:Designation.JUNIOR_DEVELOPER})
    @IsEnum(Designation)
    designation:Designation;
    
    
    @ApiProperty()
    @IsISO8601()
    dateOfJoining:Date;
    
    @ApiProperty()
    @IsNumberString()
    mobileNumber:string;
    
    @ApiProperty()
    @IsNotEmpty()
    emailAddress:string;
}
```

### Adding Documentation using plugins

``` typescript
export class BaseEmployeeDto{
    @IsNotEmpty()
    firstName:string;

    @IsOptional()
    middleName?:string;

    @IsNotEmpty()
    lastName:string;

    @IsOptional()
    teamName?:string;
    
    @IsEnum(Designation)
    designation:Designation;
    
    @IsISO8601()
    dateOfJoining:Date;
    
    @IsNumberString()
    mobileNumber:string;
    
    @IsNotEmpty()
    emailAddress:string;
}
```

`nest-cli.json` Add the compilerOptions section for nestJs Plugin
``` json

{
    "compilerOptions": {
        "plugins": ["@nestjs/swagger/plugin"]
    }
}

```

OR

``` json
{
    "compilerOptions": {
        "plugins": [{
            "name": "@nestjs/swagger",
            "options": {
                "introspectComments": true
            }
        }]
    }
}
```

### Documenting controllers

``` Typescript
@ApiTags('employee')
export class EmployeeController {
}
```


``` Typescript
   @ApiResponse({
        status: 200,
        description: 'The found record',
        type: Employee,
      })
    @Get()
    getEmployees(): Promise<Employee[]> {
        return this.employeeService.getEmployees();
    }
```

``` Typescript
@ApiBearerAuth()
@ApiTags('employee')
@ApiForbiddenResponse({ description: 'Forbidden.'})
export class EmployeeController {
}
```
