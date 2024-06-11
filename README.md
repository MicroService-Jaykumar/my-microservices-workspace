# MyMicroservicesWorkspace

<a alt="Nx logo" href="https://nx.dev" target="_blank" rel="noreferrer"><img src="https://raw.githubusercontent.com/nrwl/nx/master/images/nx-logo.png" width="45"></a>

# Current configurations Checks

- `node -v 20.13.1`
- `npm -v 10.5.2`

# Setup Commands:

- `npm install -g nx@19.1.0`
- `npx create-nx-workspace@latest my-microservices-workspace`

- Workspace command selections:

  ```
  √ Which stack do you want to use? · none
  √ Package-based monorepo, integrated monorepo, or standalone project? · package-based
  √ Set up CI with caching, distribution and test deflaking · skip
  √ Would you like remote caching to make your build faster? · skip
  ```

- `nx add @nestjs/schematics`
- `nx generate @nestjs/schematics:application microservice-one`
- `nx generate @nestjs/schematics:application microservice-two`
- `nx generate @nestjs/schematics:application microservice-three`

# Step 1: Modify project.json to Use the Custom Script

- Modify the project.json of each microservice to use the custom TypeScript script for serving the application.
- For microservice-one (apps/microservice-one/project.json):

  ```
  {
    "name": "microservice-one"
  }
  ```

# Step 2: Update the Script in package.json

- Code:
  ```
  {
    "name": "my-microservices-workspace",
    "version": "0.0.0",
    "license": "MIT",
    "scripts": {
        "serve-one": "npx nx run microservice-one:start:dev",
        "serve-two": "npx nx run microservice-two:start:dev",
        "serve-three": "npx nx run microservice-three:start:dev",
        "serve-all": "npm-run-all -p serve-one serve-two serve-three",
        "install-microservice-one": "cd ./microservice-one && npm install",
        "install-microservice-two": "cd ./microservice-two && npm install",
        "install-microservice-three": "cd ./microservice-three && npm install",
        "install-one-two-three": "npm run install-microservice-one && npm run install-microservice-two && npm run install-microservice-three",
        "install-all": "find . -name 'package.json' -not -path './node_modules/*' -execdir npm install \\;",
        "clean": "rm -rf ./**/node_modules && rm -rf node_modules"
    },
    "private": true,
    "devDependencies": {
        "@nestjs/schematics": "^10.1.1",
        "@nx/js": "19.1.0",
        "nx": "19.1.0"
    },
    "workspaces": [
        "packages/*"
    ]
  }
  ```
- Install necessary dependency to serve project:
  `npm i npm-run-all`
- Examples: 
    - `npm run install-microservice-one && npm run install-microservice-two && npm run install-microservice-three`
    - `npm run clean`
    - `npm run install-all`
    - `npm run serve-one`
    - `npm run serve-all`

# Step 3: Create "tsconfig.base.json"

- Code:
  ```
    {
      "compilerOptions": {
        "baseUrl": ".",
        "paths": {
          "@myorg/*": ["packages/*"]
        },
        "lib": ["es2017", "dom"],
        "skipLibCheck": true,
        "target": "es2017",
        "module": "commonjs",
        "moduleResolution": "node",
        "outDir": "./dist/out-tsc",
        "sourceMap": true,
        "declaration": false,
        "noImplicitAny": false,
        "noImplicitReturns": true,
        "noImplicitThis": true,
        "strictNullChecks": true,
        "types": ["node"],
        "esModuleInterop": true,
        "allowSyntheticDefaultImports": true
      },
      "exclude": ["node_modules", "tmp"]
    }
  ```
- Install NX Node Plugin:
  `npm install --save-dev @nrwl/node`

# GPT Reference:

- Link: https://chatgpt.com/share/79b5ca30-dafc-4d8f-a621-a142d5b83f14

# Step 3: Configure Each Microservice

- Navigate to the microservice-one directory:
  `cd microservice-one`
- Install necessary dependencies:
  `npm install @nestjs/graphql graphql-tools graphql apollo-server-express mongoose @nestjs/mongoose`
- Set up GraphQL and MongoDB in microservice-one:

  - Update app.module.ts:

    ```
    import { Module } from '@nestjs/common';
    import { GraphQLModule } from '@nestjs/graphql';
    import { MongooseModule } from '@nestjs/mongoose';
    import { HelloWorldModule } from './hello-world/hello-world.module';

    @Module({
        imports: [
            GraphQLModule.forRoot({
                autoSchemaFile: true,
            }),
            MongooseModule.forRoot('mongodb://localhost/nx_microservice_one'),
            HelloWorldModule,
        ],
    })
    export class AppModule {}
    ```

  - Create hello-world module;
    `nx generate @nestjs/schematics:module hello-world`

  - Create hello-world resolver and service
    `nx generate @nestjs/schematics:resolver hello-world`
    `nx generate @nestjs/schematics:service hello-world`

  - Update hello-world.resolver.ts:

    ```
    import { Resolver, Query } from '@nestjs/graphql';
        @Resolver()
        export class HelloWorldResolver {
        @Query(() => String)
        hello() {
            return 'Hello World!';
        }
    }
    ```

  - Update hello-world.module.ts:

    ```
    import { Module } from '@nestjs/common';
    import { HelloWorldResolver } from './hello-world.resolver';
    import { HelloWorldService } from './hello-world.service';
    @Module({
        providers: [HelloWorldResolver, HelloWorldService],
    })
    export class HelloWorldModule {}
    ```

# Step 4: Repeat for Other Microservices

---

# Step 1: Create the Library

- Navigate to your NX workspace root:
  `cd my-microservices-workspace`
- Install NX Node Plugin
  `npm i --save-dev @nrwl/node@19.2.3 @nx/js@19.2.3 nx@19.2.3`
- Generate a new library: This will create a new library named logger in the libs directory.
  `nx g @nrwl/node:library logger --directory libs/logger`

# Step 2: Create the Helper File

- Navigate to the logger library
  `cd libs/logger/src/lib`
- Create a new file named logger.helper.ts:
  ```
  // logger.helper.ts
  export function logMessage(message: string): void {
    console.log(`[LOG]: ${message}`);
  }
  ```

# Step 3: Export the Helper Function

- Update the index.ts file in the logger library:
  ```
  // index.ts
  export * from './logger.helper';
  ```

# Step 4: Build the Library

- Build the logger library:
  `nx build logger`

# Step 5: Use the Library in a Microservice

- Navigate to microservice-one:
  `cd apps/microservice-one`
- Install the built library
  `npm install ../../dist/libs/logger`
- Use the logMessage function in microservice-one:

  - Open app.module.ts and use the logMessage function

    ```
        import { Module, OnModuleInit } from '@nestjs/common';
        import { GraphQLModule } from '@nestjs/graphql';
        import { MongooseModule } from '@nestjs/mongoose';
        import { HelloWorldModule } from './hello-world/hello-world.module';
        import { logMessage } from '@my-microservices-workspace/logger';

        @Module({
          imports: [
            GraphQLModule.forRoot({
              autoSchemaFile: true,
            }),
            MongooseModule.forRoot('mongodb://localhost/nx_microservice_one'),
            HelloWorldModule,
          ],
        })
        export class AppModule implements OnModuleInit {
          onModuleInit() {
            logMessage('Microservice One Initialized');
          }
        }
    ```
