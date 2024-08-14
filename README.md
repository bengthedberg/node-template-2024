# Setup a production node.js project

### Create a new Node.js project with TypeScript and Jest

1. Initialise the [Node.js](https://nodejs.org/en/) project

   `npm init -y` 

2. Add [Typescript](https://www.typescriptlang.org/) 
   `npm install typescript ts-node @types/node @tsconfig/node20 --save-dev`

   * [typescript](https://www.typescriptlang.org/download/) 
   * [ts-node](https://www.npmjs.com/package/ts-node)
   * [@types/node](https://www.npmjs.com/package/@types/node)
   * [@tsconfig/node20](https://www.npmjs.com/package/@tsconfig/node20)

3. Configure [tsconfig.json](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html#handbook-content)
   `touch tsconfig.json`
   Then add the following content:
   ```json
    {
      "extends": "@tsconfig/node20/tsconfig.json",
      "compilerOptions": {
        "rootDir": "./",                                     /* Specify the root folder within your source files. */                          
        "outDir": "./dist",                                  /* Specify an output folder for all emitted files. */
        "forceConsistentCasingInFileNames": true,            /* Ensure that casing is correct in imports. */
      },
      "include": [
        "src/**/*"
      ],
      "exclude": [
        "node_modules",
        "dist"
      ]
    }
   ```
   The tsconfig configuration for node20 is defined [here](https://github.com/tsconfig/bases/blob/main/bases/node20.json). 

4.  Add [Jest](https://jestjs.io/)
    `npm install jest ts-jest @types/jest --save-dev`

5.  Configure Jest
    `touch jest.config.ts`
    Then add the following to that file:
    ```ts
    import type {Config} from 'jest';

    const config: Config = {
      // Use ts-jest preset for testing TypeScript files with Jest
      preset: "ts-jest",
      // Set the test environment to Node.js
      testEnvironment: "node",

      // Define the root directory for tests and modules
      roots: ["<rootDir>/tests"],

      // Use ts-jest to transform TypeScript files
      transform: {
        "^.+\\.tsx?$": "ts-jest",
      },

      // Regular expression to find test files
      testRegex: "((\\.|/)(test|spec))\\.tsx?$",

      // File extensions to recognize in module resolution
      moduleFileExtensions: ["ts", "tsx", "js", "jsx", "json", "node"],
    };

    export default config;
    ```
6. Add scripts to `package.json` file:

   ```json
   {
      "scripts": {
        "build": "tsc",
        "test": "jest",
        "test:watch": "jest --watch"
      },
   }
   ```
7. Add some sample code and tests to verify project settings:
   - Add `src/utils.ts` file with content:
     ```ts
     export function add(a : number, b : number) {
      return a + b;
     }
     ```
   - Add `tests/add.test.ts` file with content:
     ```ts
     import { add } from "../src/utils";

     it('should add two numbers', () => {
        expect(add(1, 2)).toBe(3);
     });
     ```
8. Enforce typescript in our tests  
    Add another `tsconfig.json` file in the `tests` directory with the content:
    ```json
    {
      "extends": "@tsconfig/node20/tsconfig.json"
    }
    ```
9. Run tests
   `npm run test`

    ```
    > template-2024@1.0.0 test
    > jest

    PASS  tests/add.test.ts
      ✓ should add two numbers (1 ms)

    Test Suites: 1 passed, 1 total
    Tests:       1 passed, 1 total
    Snapshots:   0 total
    Time:        0.575 s
    Ran all test suites.`
    ```


