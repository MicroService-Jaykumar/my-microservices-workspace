# [MyMicroservicesWorkspace](https://github.com/MicroService-Jaykumar/my-microservices-workspace/blob/main/README.md)

# Type A: Monorepo with Common Packages

# Setup Commands:

- `npm install -g nx@19.2.3`
- `npx create-nx-workspace@latest my-microservices-workspace`

- Workspace command selections:

- ```
  √ Which stack do you want to use? · none
  √ Package-based monorepo, integrated monorepo, or standalone project? · Integrated
  √ Set up CI with caching, distribution and test deflaking · skip
  √ Would you like remote caching to make your build faster? · skip
  ```

- ```cd my-microservices-workspace```
- ```npm i @nrwl/nest@19.3.1```
- ```nx g @nrwl/nest:application apps/my-microservice-one```
- ```nx g @nrwl/nest:application apps/my-microservice-two```

# Step 1: Update the Script in package.json
- Install necessary dependency to serve project:
  - `npm i npm-run-all`

- Code:
  ```
  {
    ...
    "scripts": {
        ...
        "serve-one": "npx nx run my-microservice-one:serve",
        "serve-two": "npx nx run my-microservice-two:serve",
        "serve-all": "npm-run-all -p serve-one serve-two"
        ...
    },
    ...
  }
  ```
  
- Examples (cd <TO NX ROOT DIR>): 
    - `npm run serve-one`
    - `npm run serve-two`
    - `npm run serve-all`

# Step 2: Create Library

- `nx g @nrwl/node:library libs/logger`

# Step 3: Add library to *my-microservice-one*:
- cd my-microservice-one
- Code (./apps/my-microservice-one/src/app/app.service.ts):
  ```
    ...
    import { logger } from "@my-microservices-workspace/logger";
    ...
    export class AppService {
      ...
      getData(): { message: string } {
        logger(); // Use library
        return { message: 'Hello API' };
      }
      ...
    }

  ```
