# Static Test tooling
It will cover the static testing tools like `eslint`, `prettier`, `typescript`, `husky` & `lint`. With the use
of these tools one can have a great development environment with automated tests.

## Eslint
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
        "browser" : "true"
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
<!-- - For now, we will check for the errors on our `src` directory. we will use a glob `/**/*.src`. So, our previous validate command will be `npm run lint` -->








