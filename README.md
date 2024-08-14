# Setup a production node.js project

### Create a new Node.js project with TypeScript and Jest

1. Initialise the [Node.js](https://nodejs.org/en/) project

   `npm init -y`

2. Add [Typescript](https://www.typescriptlang.org/)
   `npm install typescript ts-node @types/node @tsconfig/node20 --save-dev`

   - [typescript](https://www.typescriptlang.org/download/)
   - [ts-node](https://www.npmjs.com/package/ts-node)
   - [@types/node](https://www.npmjs.com/package/@types/node)
   - [@tsconfig/node20](https://www.npmjs.com/package/@tsconfig/node20)

3. Configure [tsconfig.json](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html#handbook-content)
   `touch tsconfig.json`
   Then add the following content:

   ```json
   {
     "extends": "@tsconfig/node20/tsconfig.json",
     "compilerOptions": {
       "rootDir": "./" /* Specify the root folder within your source files. */,
       "outDir": "./dist" /* Specify an output folder for all emitted files. */,
       "forceConsistentCasingInFileNames": true /* Ensure that casing is correct in imports. */
     },
     "include": ["src/**/*"],
     "exclude": ["node_modules", "dist"]
   }
   ```

   The tsconfig configuration for node20 is defined [here](https://github.com/tsconfig/bases/blob/main/bases/node20.json).

4. Add [Jest](https://jestjs.io/)
   `npm install jest ts-jest @types/jest --save-dev`

5. Configure Jest
   `touch jest.config.ts`
   Then add the following to that file:

   ```ts
   import type { Config } from "jest";

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
     }
   }
   ```

7. Add some sample code and tests to verify project settings:

   - Add `src/utils.ts` file with content:
     ```ts
     export function add(a: number, b: number) {
       return a + b;
     }
     ```
   - Add `tests/add.test.ts` file with content:

     ```ts
     import { add } from "../src/utils";

     it("should add two numbers", () => {
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

10. Add .gitignore for node
    `wget -O .gitignore  https://raw.githubusercontent.com/github/gitignore/main/Node.gitignore`
11. Initialise git
    `git init --initial-branch=main`
12. Commit all changes
    ```
    git add .
    git commit -m 'Initial Project Setup'
    ```

### Setup ESLint and Prettier

1. Install the [ESLint](https://eslint.org/)
   `npm init @eslint/config@latest`
   Then select the following options :

   ```
    ✔ How would you like to use ESLint? · problems
    ✔ What type of modules does your project use? · esm
    ✔ Which framework does your project use? · none
    ✔ Does your project use TypeScript? · typescript
    ✔ Where does your code run? · browser, node
    The config that you've selected requires the following dependencies:

    eslint, globals, @eslint/js, typescript-eslint
    ✔ Would you like to install them now? · No / Yes
    ✔ Which package manager do you want to use? · npm
   ```

   The ESLint has created a new configuration file called `eslint.config.mjs`. You can read about the different ESLint options [here](https://eslint.org/docs/latest/use/configure/)

2. Configure ESLint
   Update the `eslint.config.mjs` with the following configuration :

   ```js
   import globals from "globals";
   import pluginJs from "@eslint/js";
   import tseslint from "typescript-eslint";

   export default [
     {
       ignores: ["dist/"],
     },
     {
       // JavaScript ESLint configuration is applied to all files with the following extensions.
       files: ["src/**/*.{js,mjs,cjs,ts}", "tests/**/*.{js,mjs,cjs,ts}"],
     },
     {
       files: ["**/*.js"],
       languageOptions: { sourceType: "commonjs" },
     },
     {
       languageOptions: {
         globals: { ...globals.browser, ...globals.node },
       },
     },
     pluginJs.configs.recommended,
     ...tseslint.configs.recommended,
   ];
   ```

3. Install ESLint for Jest
   `npm install eslint-plugin-jest --save-dev`
4. Configure ESLint for Jest
   Add the following configuration to your `eslint.config.mjs` file :

   ```js
    ...
    import jest from "eslint-plugin-jest";

    export default [

      ...

      {
        files: ["tests/**/*.{js,ts,jsx,tsx}"],
        ...jest.configs["flat/recommended"],
        rules: {
          ...jest.configs["flat/recommended"].rules,
          "jest/prefer-expect-assertions": "off",
        },
      },
    ];
   ```

5. Install Prettier
   `npm install --save-dev eslint-plugin-prettier eslint-config-prettier`
   `npm install --save-dev --save-exact prettier`

6. Configure ESLint for Prettier
   Add the following configuration to your `eslint.config.mjs` file :

   ```js
    ...
    import eslintPluginPrettierRecommended from "eslint-plugin-prettier/recommended";

    export default [

      ...

      {
        rules: {
          "@typescript-eslint/no-unused-vars": "off",
          "prettier/prettier": [
            "error",
            {
              endOfLine: "auto",
            },
          ],
        },
      },
      eslintPluginPrettierRecommended,
    ];
   ```

7. Add ESLint to the `package.json` file
   ```json
   {
    ...
     "scripts": {
        "build": "tsc",
        "test": "jest",
        "test:watch": "jest --watch",
        "lint": "eslint",
        "lint:fix": "eslint --fix"
     },
    ...
   }
   ```
8. Lint our code
   `npm run lint`
9. ix any errors in linting:
   `npm run lint:fix`

## VS Code Extensions
