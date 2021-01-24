# Static Test tooling
It will cover the static testing tools like `eslint`, `prettier`, `typescript`, `husky` & `lint`. With the use
of these tools one can have a great development environment with automated tests.

- [Eslint](#eslint)
- [Prettier](#prettier)
- [Typescript](#typescript)
- [Husky](#husky)
- [Lint](#lint)

## Eslint <a name="eslint" />
ESlint is a tool for identifying the errors in the javascript according to the patterns found in Ecmascript.

- Install eslint (with npm or yarn)
  ```js
    npm install eslint
    ```
- You can now run `eslint` with `npx eslint .` on your command line. The `.` (dot) means all the files on the project. 
  It will print out `not matching files with '.'`. It is because it didn't found any files with extension `.js`.Let's create a file named `test.js` on `src` directory and run `npx eslint .` again. It will ask us to setup a configuration file.
- It won't work without the configuration file.
  - Now, lets's setup a `eslint` configuration file ourselves. Note that you can also run `npx eslint --init` which will create an eslint configuration files. But for now lets
    create one ourselves. On our root directory, create a file `.eslintrc` which will contain a JSON.
  - ```js
      {
        "parserOptions" : {
            "ecmaVersion" : 6,
            "sourceType" : "module",
            "ecmaFeatures" : {
                "jsx" : true
            }
        }
      }
  ```
    - Setting parser options helps `eslint` determine what is parsing error.
    - `ecmaVersion` - sets the version of the `js` you will be using. Eslint default to 4,5.
    - `sourceType` - defaults to scripts. Since, we are using module pattern we will have the value `module`.
    - `ecmaFeature` - additionally, we want to extra language feature `jsx`.
- Run the command `npx eslint .` again. Nothing will really happen because we don't have anything in out `test.js` file.
  - Add this line in `test.js`.
    ```js
      cosnt test = "test";
    ```
    Clearly, this is an error `cosnt test` won't work. Anyway, run `npx eslint .` will throw us an error `unexpected token`. Atleast we know something is wrong beforehand.
    Lets fix that with `js const test = "test"; ` and run the command again. It will work without an error.  
- Since we are running `npm run eslint` as lot so let's just set up a command on `package.json` which will check for errors and more verbose than `eslint`. Something like `npm run lint `.
  - Open `package.json` then on the `scripts` key add this line `"lint" : "npx eslint ."` as
  ```
    "scripts": {
      "lint": "npx eslint ."
    },
  ```
  Now, check if we have any error by running `npm run lint`.
- We will looking at some of the user errors that will be handled by the `eslint` perfectly. Copy the below code onto `test.js`.
  ```js
    // empty block statement.
    if (true) {

    }

    const string = "this is a string"
    const object = { name: "Rakesh Shrestha" };

    // typeof spelling errors
    typeof string === "stng";
    typeof object === "stng";

    // unused & undefined vars.
    let name = myName;

    // disallow negating the left operand of relational operators
    if (!"name" in object) {
        let info = name + " from Nepal";
    }
  ```
- Run `npm run lint`, it will execute with no errors. Let's tell `eslint` to look out for some set of mistakes. We are going set the `rules` in our `.eslintrc` for it.
  ```
  {
    "parserOptions" : {
        "ecmaVersion" : 6,
        "sourceType" : "module",
        "ecmaFeatures" : {
            "jsx" : true
        }
    },
    "rules" : {
        // you will have you rules here
    }
  }
  ```
- In our `test.js` we have an empty block statement which is sort of a mistake that we don't want.
  ```js
  if (true) {

  }
  ```
  So, inorder for eslint to catch this error we have a rule `no-empty`. 
  ```js
    "rules" : {
      "no-empty": "error"
    }
  ```
  Run `npm run lint`. Right there it will throw us an error `error  Empty block statement  no-empty`. 
  **Note** that we put `"no-empty" : "error"` it could also be `:"no-empty" : 2`. Run the command again and see it for yourself. We can also throw warnings instead of errors or turn off the rules (we will do it later).
  For now try `"no-empty" : 1` or `"no-empty" : "warn"`. So, we have `0 or off`, `1 or "warn"`, `2 or "error"`. I personally prefer the `"error" instead of 2` because it's more declarative and understandable.
  let's fix the current issue we have at hand by doing something for now.
  ```js
  if (true) {
    let whatIsCool = "Eslint is cool"
  }
  ```
  Now, that we fixed it run the linting command again. It will work now. Isn't great? eslint is able to shout us at our mistakes what is better than that during development?
  It has power to fix lots of crazy little mistakes we make everyday and also it facilates us with standard coding style. We will look at it later.
- Update your `.eslintrc` rules to below code and run `npm run lint`. It will gives us a lot's of errors. We need to fix those all.
  ```
  "rules" : {
    "no-empty" : "error",
    "valid-typeof" : "error",
    "no-unused-vars" : "error",
    "no-undef" : "error",
    "no-unsafe-negation" : "error"
  }
  ```
  - `invalid typeof comparison value` : its shouting us at `js typeof string === "stng";`. There's no data type as `stng` so make it a `string` and also we have
  `js typeof object === "obj";` change the `obj` to `object`. It will fix us `invalid typeof comparison value` error.
  - `'myname' is not defined`. There's no definition of that variable so let's fix it by defining it.
  ```js
    // unused & undefined vars.
    const myName = object.name;
    let name = myName;
  ```
  - ` Unexpected negating the left operand of 'in' operator` this error is something that is not actually an error but the actual way of doing it is with the falsy check. So, it now proves the statement 
  that I made previously "eslint also helps to write better, standard and elegant code" - Rakesh. 
  We can fix it by doing:
  ```js
    // disallow negating the left operand of relational operators
    if (!("name" in object)) {
      let info = name + " from Nepal";
    }
  ```
  If you don't like it then you can remove the rule `"no-unsafe-negation" : "error"` from `.eslintrc` or give it value of `"off"`.
- `'info' is assigned a value but never used  no-unused-vars`. We can fix it by using the info variable.
  ```js
    // disallow negating the left operand of relational operators
    if (!("name" in object)) {
      let info = name + " from Nepal";
      console.log(info)
    }
  ```
- We know have fixed all the errors quite easily. Now run `npm run eslint`. Wait what? `console` is not defined? It's expected as we haven't setup the eslint environment. `console` run on a browser environment so we have to set it up as well with `"env" : {"browser" : true}`. Finally, the `.eslintrc` looks like :
  ```
  {
    "parserOptions" : {
        "ecmaVersion" : 6,
        "sourceType" : "module",
        "ecmaFeatures" : {
            "jsx" : true
        }
    },
    "env" : {
        "browser" : true
    },
    "rules" : {
        "no-empty" : "error",
        "valid-typeof" : "error",
        "no-unused-vars" : "error",
        "no-undef" : "error",
        "no-unsafe-negation" : "error"
    }
  }
  ```
- Well done! we have now fixed all the underlying errors on our `test.js` file.
- There are lots of rules one will need. But isn't it quite tiresome to define all the rules? So, now we will be using the standard pre-built configuration that `eslint` recommends.
- Update `.eslintrc` . Add the key `extends : ["eslint:recommended"]`. You can override the this key with other pre-built configurations as well.
- With that pre-built configurations we don't need our previous set of rules as it's already configured in our `eslint:recommended` built. Find more about rules on
[rules](https://eslint.org/docs/rules/)
- `.eslintrc` will now look like :
``` 
// update here required
```
- Run our old friend `npm run lint`. It will now display us an error `Unexpected constant condition  no-constant-condition`. It's saying we have a constant condition and it's actually referring to this line in out `test.js`.
```js
  if(true){
    let whatIsCool = "javascript is cool"
  }
```
Here we don't really need the the condition. So, eslint is actually correct. But for the sake of my promise where I talked about [turning the rules of](here), we will be doing it here.
- So, we will now override the rule `no-constant-condition` given by `eslint:recommended`. We can identify the rule as the last string `Unexpected constant condition  no-constant-condition` of out warning or error.
- Set a rule `"no-constant-condition" : "off"` in `.eslintrc` and then run the eslint again. Now, it should work perfectly.
- In the future, if you don't like a rule then just override it to "off". Since, we learnt it how to use it. Let's actually fix our mistakes. Let's remove the rule `no-constant-condition: "off"` from our `.eslintrc` as we 
need this rule. Update the `test.js` as 
```js
//previously
if (true) {
    // let whatIsCool = "javascript is cool"
}

//now
let whatIsCool = "javascript is cool"
console.log(whatIsCool)
```
Run the command again. It should work just fine.

### Narrowing down the linting
We are linting all the files in our project directory that is a `.js` file. What if we are working with `babel` or `webpack`? As we know they compile down the codes from the `node_modules` and linting will actually also check errors in our compiled file.
So, we need to ignore the `node_modules` as well as the directory where the build is being made. But in `eslint` to my knowledge `node_modules` are ignored by default.

#### Setting up eslintignore
Create a directory on the root as `build` and also create a file in it with name `test.build.js`. let's consider `test.build.js` is our compiled file. Put this content inside of it `const test = "test";`.
Run the command `npm run lint`. It will gives us an error `'test' is assigned a value but never used  no-unused-vars` in the compiled file which we don't want as we are sure that we have already linted it (Standard is running lintings before compiling code).
Inorder to ignore any specific file in our case it's the compiled file we will create a `.eslintignore` in our root directory. **Note** the filename can be anything for example `.ignore`, `.gitignore` etc but look into `.gitignore` what does it mean? We can see it's ignoring files for git so `.eslintignore` is way better than simply `.ignore`.

**.eslint-ignore**
<img src="images/eslint-ignore.png"/>

Above we ignore the directory build. With that in place, you can run now `npx eslint --ignore-path .eslintignore .`.You will see that the files in the build directory are now ignored i.e eslint wont run checks on it. The `--ignore-path` tells the eslint what are the files that it should ignore.
We can refactor the command to run with the ignore path like before. 

**package.json**
<img src="https://raw.githubusercontent.com/aomini/Static-testing/master/images/eslint-ig-pkg.PNG?sanitize=true" />

### Installing Eslint Extension
Eslint extension is a great way to work with the static testing. In `vscode` install [eslint extension](https://update.com). 
After installation you may need to reload `vscode`.

### Eslint Extension
You can create a new file in src directory and write something like this:
```js
const test = "something";
```
The vscode now knows that the variable `test` is declared but never used so throws an error visible to your eyes without running `npx eslint`. This is possible because of the eslint extension that you've installed previously.
<img src="images/eslint-ex.png" />

## Prettier
You often find yourself making the code pretty like adding indentation equally in every piece of code you write, adding semicolons if that is what you're into and many more. But it's a waste of time and there's no confirmation that all your contributors or even team members follow the same style guides that you work with. So, prettier enforces a style guide as well as helps to make the code prettier in a easy scoop.

### Installing Prettier
Many of the people start using prettier just by installing extension. It's okay to do it as well but not in a project with others on it. People can have different configuration settings. And with the extension only you're settings are unique to yourself. So, we will install prettier locally to our project and have a configuration file with set of rules for prettier to follow same as eslint.
`npm install prettier -D`

## Using Prettier
Prettier comes along with the cli. **NOTE** you've installed the package locally so you've use `npx prettier` but if you had installed it globally with the `g` flag, you could do just `prettier`.
Now, you can make some code formatting errors. Previously we created a file to test our eslint extension, let's call it `test.js` (`src\test.js`);

**test.js**
```js
const test = "nothing"
```

Change this to 
```js
const test
      = "nothing"
```

This is clearly an formatting error. You can check it with prettier. Run `npx prettier .`. Well, there may be too many file that prettier tried to execute like `serviceWorker.ts`. You can narrow it down by giving the specific file location. Try it with `npx prettier src/test.js`, it will give you the output
```js
const test = "nothing"; 
```
Notice our code was not formatted like it & prettier additionally adds the semicolon as well. But if you look at your file it is still the same is used to be prettier didn't really updated it. Either you can copy paste the generated formatted code to the file that prettier just consoled out or you can run prettier to `write` access as well. Try `npx prettier src\test.js --write`. It should now update the `test.js` with prettified code.

**Configuring prettier**
You may need some other formatting or change the default behaviour of the prettier like the semicolon addition. Some prefer semicolon & some don't. You can change this sort of behaviour with the prettier config file.
Visit this link [Prettier playground](https://prettier.io/playground/). You can create a config of your own. On the left side of the playground, there's a panel choose the options which you want to have. 
For example: 
```
--parser typescript 
// parser can be anything since we are using typescript to compile our react app we will use typescript as parser but it's optional as prettier is able to identify it on run time.

--no-semi false
// I like semicolons. If you don't need semicolons check it.
```
You can go through the options you self and check what works for you best. You can now download / copy the config JSON.
On your root dir create a file `.prettierrc` & paste your copied config JSON.

**.prettierrc**
```
{
  "arrowParens": "always",
  "bracketSpacing": true,
  "htmlWhitespaceSensitivity": "css",
  "insertPragma": false,
  "jsxBracketSameLine": false,
  "jsxSingleQuote": false,
  "printWidth": 80,
  "proseWrap": "preserve",
  "quoteProps": "as-needed",
  "requirePragma": false,
  "semi": true,
  "singleQuote": false,
  "tabWidth": 2,
  "trailingComma": "es5",
  "useTabs": false,
  "vueIndentScriptAndStyle": false
}
```
The file name can be anything but by default prettier checks for config `.prettierrc`. You can also run another config file with `config` flag as `npx prettier --config .my-prettier-config .`;
Find out more about flags and options in [Prettier CLI](https://prettier.io/docs/en/cli.html).

Make some changes to your previous file to check if you're prettier is working as you wanted. Don't forget to run `npx prettier src\test.js` in the end.

**package.json**
Now that you've set up the config file, you add a `script` in your package.json to run it more efficiently. **NOTE** you don't want to run prettier in production code as it is minified as well as you only want prettier to run for the files `.js, .jsx, .ts, .tsx` inside of your `src` dir (we need globs);
```
"scripts": {
    "lint": "npx eslint --ignore-path .eslintignore .",
    "pretty": "npx prettier \"**/*.(js|json|jsx|ts|tsx)\" --write"
  },
```

**Understanding the glob**
`\"**/*.+(js|json|jsx|ts|tsx)\"` : `\"` is escaping the double quotation marks, `**/` is checking in all folders (`src/` would check inside of src dir only), `*.+(js|json|jsx|ts|tsx)` name of the file can be anything and can end with either `js or json or jsx or ts or tsx`.
So, all together the `script` will look for formatting issues within the project & format it. Make some formatting issues in your `test.js` and `App.tsx` & then run `npm run pretty` it should format all your codes.

**Problems with our current setup**
Currently, your prettier is running all of your files which we don't want. As I said we don't want prettier to pretify code in our build directory or some other directories which depends. You can handle it with `ignore-path` flag of prettier similar to `.eslintignore` path. 
**NOTE** both ignore files follow the `gitignore` pattern, you can read more about it at [gitignore patterns](https://git-scm.com/docs/gitignore#_pattern_format).
You can check that your prettier is running on the build directory as well. If you have followed from previous eslint you should have a file `test.build.js` in your `build` directory. If you don't have it create `build/test.build.js`;

**test.build.js**
```js
const test
    = "test"
```
I have a formatting error. You can run now `npm run pretty`.
```js
const test = "test";
```
Our prettier is running on build directory as well which we want to ignore.

**Ignoring files in prettier**
Create a file in the root `.prettierignore`.
```
  /build
```
Update your `scripts` in `package.json`.

**package.json**
```
"scripts": {
  "lint": "npx eslint --ignore-path .eslintignore .",
  "pretty": "npx prettier --ignore-path .prettierignore \"**/*.(js|json|jsx|ts|tsx)\" --write"
},
```

Make some formatting issues in `test.build.js` and run `npm run pretty`. `test.build.js` won't format now. You may notice `.eslintignore` & `.prettierignore` are kind of same in our case it's actually same. But one more thing to notice is they are ignoring what git is ignoring so you can use `.gitignore` as your ignore path.
**package.json**
```
"scripts": {
  "lint": "npx eslint --ignore-path .gitignore .",
  "pretty": "npx prettier --ignore-path .gitignore \"**/*.(js|json|jsx|ts|tsx)\" --write"
},
```
Try running the scripts, they should work just like before.

**Prettier Extension**
You can install prettier extension to make your work lot easier without having to run the script everytime you want to format your code.
Install `prettier` extension in `vscode`. For other text-editors visit [Prettier integration](https://prettier.io/docs/en/editors.html).

Make `prettier` your default formatter. Go to your `settings.json` and add this json:
```
  {
    "editor.defaultFormatter": "esbenp.prettier-vscode",
    "[javascript]": {
      "editor.defaultFormatter": "esbenp.prettier-vscode"
    }
  }
```
After that you can use it as 
```
1. CMD + Shift + P -> Format Document
OR
1. Select the text you want to Prettify
2. CMD + Shift + P -> Format Selection
```
You can now go to `test.js` and make some formatting errors and press `CMD + Shift + P` or `Ctrl + Shift + P` & type `format document`.
Prettier can be used on files on save as well inorder to do that update your `settings.json` as
```
  // Set the default
  "editor.formatOnSave": false,
  // Enable per-language
  "[javascript]": {
      "editor.formatOnSave": true
  }
```
Languages supported by `prettier`: 
```
javascript
javascriptreact
typescript
typescriptreact
json
graphql
```

**Problems with eslint & prettier**
Sometime eslint & prettier rules conflict withother. For example : you have `semicolons` enabled in `eslint` and disabled in `prettier` which will cause issues with `eslint`. So, we can override the `eslint` rules.
There may be other things as well so we will just use others `config`.
Install `npm i eslint-config-prettier -D`

**.eslintrc**
```
{
    "parserOptions" : {
        "ecmaVersion" : 6,
        "sourceType" : "module",
        "ecmaFeatures" : {
            "jsx" : true
        }
    },
    "env" : {
        "browser" : true
    },
    "extends" : ["eslint:recommended", "prettier"],
    "rules" : {   
        "no-constant-condition" : "off"    
    }
}
```

## Populars javascript style guide integration (needs work)
I personally prefer (google) style guides... 

### TYPESCRIPT

## Installation
Since, I believe you already have installed `typescript` if not then let's just install it.
```
npm i typescript -D
```
You can now create a file `test.ts`.
**test.ts**
```ts
let a: string = "string";
export default a;
``` 
If you see any eslint errors ignore them for now.
You can run `npx tsc` to compile the `.ts` files with typescript. Nothing will happen as it compiles successfully. Update the `test.ts` file with following code :
**test.ts**
```ts
let a: string = "string";
a = 2;
export default a;
``` 
Here, you have declared `a` as a `string` but assigning it a `number` which is a type error so run `npx tsc` then it will throw an error on that line.
Since, you'll be running it again & again how about creating an alias script on `package.json`. Add this line to your package.json scripts key.
```
  "check:types": "tsc",
  "validate:code": "npm run check:types & npm run pretty"
```
If you only want to check types you can do `npm run check:types` or if you want to check types as well as validate code the run `npm run validate:code`.
**test.ts**
```ts
let a: string 
  = "string";
a = 2;
export default a;
``` 
Here, you should make a format error & run `npm run validate:code`. **Notice** it fixes the formatting issue. Normally, it prettier doesn't works with the `.ts` or `.tsx` files but it's because of the glob that you
have added in the glob of `npm run pretty` script.

## Integrating eslint & typescript
If you go back to `test.ts` you'll have some linting errors.
<img src="images/ts-lint-error.png"/>

But if you run our previous eslint command `npm run lint` it won't throw you the error. It's because our eslint only supports or looks at `.js` files, so you need to extend it's area of scope. You can do that with eslint flag `--ext`. 
Update the lint script in your `package.json`.

**package.json**
```
"lint": "npx eslint --ext .js,.jsx,.ts,.tsx --ignore-path .gitignore ." 
```
With that it will lint now `js, jsx, ts & tsx` extension. You can run `npm run lint` it will now throw the error as `unexpected token :`.

This is because `: string` doesn't mean anything in javascript, so you've to tell eslint that this is a typescript. You can now add typescript plugin to work with files with extension `.ts` & `.tsx`.
Now, run : `npm install @typescript-eslint/parser @typescript-eslint/eslint-plugin -D`
Read more : [typescript-eslint](https://github.com/typescript-eslint/typescript-eslint)

You can now update the eslint configuration file.
**.eslintrc**
```
{
    "parserOptions" : {
        "ecmaVersion" : 6,
        "sourceType" : "module",
        "ecmaFeatures" : {
            "jsx" : true
        }
    },
    "env" : {
        "browser" : true
    },
    "extends" : ["eslint:recommended", "prettier"],
    "rules" : {   
        "no-constant-condition" : "off"    
    },
    "overrides": [
        {
            "files" : "**/*.+(ts|tsx)",
            "parser" : "@typescript-eslint/parser",
            "parserOptions": {
                "project" : "./tsconfig.json"
            },
            "plugins" : ["@typescript-eslint/eslint-plugin"],
            "extends" : []
        }
    ]
}
```
you have know override eslint for files with extensions `ts | tsx`. Go to your `test.ts` file you won't have that linting error now but we have another problem.
Previously, you have looked at a `typeof error` let's do that again in `test.ts`.

**test.ts**
```ts
let a: string = "string";
typeof a === "stng"
export default a;
```
You'll have two error one with eslint & another being typescript.
<img src="images/typescript-eslint-multi-error.png">

Since, we want typescript to do most of the stuffs in our `.ts | .tsx` file so you will want to show the error with typescript. For this we will extends some predefined eslint-typescript configs.
<img src="images/typescript-eslint-config.png">

Now, run `npm run lint`. Ignore all the warning's for now mostly they be of `unused vars` which is not a problem. Currently, at `test.ts` you will see two errors
You may see two errors again in `let a : string = "string"` but they are different now if you take a look.
- **'a' is never reassigned. Use 'const' instead.**
  - This error is from eslint you can fix it by simply doing `const a : string = "string"`.
- **Type string trivially inferred from a string literal, remove type annotation.**
  - This error is from typescript and it's saying the string literal infers the type so no need to give it `:string`.
  - ` const a  = "string`.

**test.ts**
```ts
const a = "string";
typeof a === "string"
export default a;
```

## Prettier & typescript
Sometime, prettier & typescript conflicts with eachother. So, we want typescript to be in sync with our prettier configs. So, we have to add one more plugin.
(UPDATE REQUIRED HOW THEY CONFLICT)

**.eslintrc**
```
"overrides": [
        {
            "files" : "**/*.+(ts|tsx)",
            "parser" : "@typescript-eslint/parser",
            "parserOptions": {
                "project" : "./tsconfig.json"
            },
            "plugins" : ["@typescript-eslint/eslint-plugin"],
            "extends" : [
                "plugin:@typescript-eslint/eslint-recommended",
                "plugin:@typescript-eslint/recommended",
                "eslint-config-prettier/@typescript-eslint"
            ],
            "rules" : {
              // Removes the ts interface name prefix rule
               "@typescript-eslint/interface-name-prefix" : "off"
            }
        }
    ]
```











