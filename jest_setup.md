# Jest setup

## Installation
- npm install jest
- npm install @testing-library/react

## Setting jest environment
> Jest by default sets the environment to DOM env. You can validate it by logging out `window` in one of your `tests` file. Since, node doesn't have a window object it should throw error but it doesn't which means that jest is running in a DOM environment. You can be more explicit about it by updating your config.
- Create `jest.config.js`
- Then, set the test environment to jsdom. 
```js
module.exports = {
  testEnvironment: 'jest-environment-jsdom' // also has jest-environment-node
}
  ```
 
 ## Supporting css imports
 > Jest by default tries to resolve css imports as `commonjs` modules. So, we need to map `.css`, `.module.css` or it's preprocessors with some modifiers. We can do this by adding a new object `moduleNameMapper`.
   - Mapping `.css`.
   > Create a file on the root dir as `test/style.mock.js` and export a default empty object as `module.exports = {}`. Then, add it to jest config.
 ```js
  moduleNameMapper: {      
    '\\.(css)$': require.resolve('./test/style.mock.js'),
  },
 ```
  - Mapping `.module.css`.
    > Using module won't show any errors by your styles wont be used as `.css` is map to empty object. so, for modules we need to install `identity-obj-proxy`.
 ```js
    moduleNameMapper: {
      '\\.module\\.css$': 'identity-obj-proxy',
      '\\.(css)$': require.resolve('./test/style.mock.js'),
    },
  ```
  
  ## CSS in js (emotion)
  > Used in snapshot testing for legible errors & output. Rather than having generated className you can helpful emotion classes like `emotion: 0`. We can do this by doing snapshot serializers. 
  - Install `jest-emotion`
  - Update your jest config:
  ```js
    snapshotSerializers: ['jest-emotion'],
  ```
  
  ## Resolve modules
  > If you have used some module resolvers either from webpack or typescript then jest won't be able to resolve a relative directory used a node modules. So, we can add a modules config to jest config.
  ```js
  moduleDirectories: ['node_modules', path.join(__dirname, 'src'), 'components'],
  ```
  
  ## Better DOM assertions & meaningful error message
  > Jest throws errors like `expected : x received: y`, if you want to have a better assertion then you can use testing library/jest-dom package.
  - Install `@testing-library/jest-dom `
  - Import it on your file as `import * as JestDom from '@testing-library/jest-dom`.
  - Then add it to jest assetions which mean it will add new assertions to your file `expect.extend(JestDom)`. You can also escape this step by importing jest-dom/extend-expect utility as `import '@testing-library/jest=dom/extend-expect' which will do the same. 
  
  ## configure jest to run setup for all tests
  > Run particulars setups or files for all test file. Here, we are going to use `@testing-library/jest-dom/extend-expect` to all files.
  - This can be done by the property `setupFilesAfterEnv`.
  - ```setupFilesAfterEnv: ['@testing-library/jest-dom/extend-expect']```
  
  ## Global test utilities
  > You do this by simply giving the path to the folder containing the module nad put it inside of `moduleDirectories`. 
  ```js
    moduleDirectories: ['node_modules', path.join(__dirname, 'src'), 'components', 'testUtilsFolder'],
    ```
  > This will throw an eslint error so inorder to get over that. Install `eslint-import-resolver-jest` and update your `eslintrc`.
  ```js
  overrides: [    
    {
      files: ['**/__tests__/**'],
      settings: {
        'import/resolver': {
          jest: {
            jestConfigFile: path.join(__dirname, './jest.config.js'),
          },
        },
      },
    },
  ],
  ```
  > For vs-code. Create a `jsconfig.json` in root dir. This helps vscode to navigate to the module.
  ```js
  {
  "compilerOptions": {
    "baseUrl": "./",
    "paths": {
      "*": ["src/*", "src/shared/*", "test/*"]
    }
  },
  "include": ["src"]
}

```

## Debugger
> Add This command to `package.json` and open it in chrome as `chrome://inspect`.
```js
"test:debug" : "node --inspect-brk ./node_modules/jest/bin/jest.js --runInBand --watch",
```

## Test Coverage
> You can see your test coverage by appending the option `coverage`. `jest --coverage` which will create a folder and open the `index.html` which contains the coverage report. You can also set the coverage report from particular test directory. We don't want to see the test coverage from test utils so we can update the jest config  to only cover test from `src`.
```js
collectCoverageFrom : ['**/src/**.*js']
```

  
