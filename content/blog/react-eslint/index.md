---
title: Using ESLint and Prettier in Create React App
date: "2021-01-28"
---

![Jars with food and spices](./agence-olloweb-d9ILr-dbEdg-unsplash.jpg "Photo by [Agence Olloweb](https://unsplash.com/@olloweb?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/examination?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)")

Recently I needed to set up ESLint and Prettier in my project that was based on Create React App. In this article I describe the steps that I did and also summarize some key aspects of this topic.

## What is linting?

In simple terms, linting is a process of analyzing a code to find programming errors or undesired code constructs. Linters such as [ESLint](https://eslint.org/) are comparing the code against a defined set of rules. The rules might concern:

- code quality, e.g. presence of unused variables or unreachable code, variables defined in global scope, usage of alerts etc.
- code formatting, e.g. using spaces in indentations, having lines no longer than 80 characters, requiring a trailing commas etc.

Linters might return their results in console during code build. We can also use an IDE extension to see the hints and errors directly in the code.

So, linters helps us write better code by enforcing following of some rules. There is however no one golden set of rules to follow. We can specify our own set of rules, use already existing shared configurations or combine those two approaches.

It is worth noting that linters do not find bugs in the app. The code might be perfectly fine according to the linting rules, but still crash or return wrong results, e.g. if our business logic is incorrect. But using linters is definitely going to help us in preventing some bugs, because our code is more consistent and easier to understand for developers working on it.

## What is prettifying?

Prettifying is a process of ensuring that our code follows specified coding style. This concerns the formatting of the code, but not the code quality. Prettifying tools can notify us about the issues and also format our code automatically. We can also use extensions for IDE to format the code automatically after each save. When using prettifiers, developers can focus on what is important when writing the code, instead of being distracted by incorrect indentations or missing commas.

One of the most popular tools for this is [Prettier](https://prettier.io/). It works with JavaScript and multiple other languages. It is opinionated, which means that it enforces using some rules without the possibility of changing them. This might be considered a disadvantage, but on the other hand it simplifies things for us.

## ESLint in Create React App

Create React App comes with ESLint config which we can additionally extend and configure according to our needs. The code is linted during each compilation. If there are errors, compilation fails with a `Failed to compile` message in the browser and in the console. This helps us writing the code, because we can immediately see what is wrong and the problems are not piling up.

## Adding and configuring ESLint and Prettier

An important thing about the setup described below is that Prettier is added as one of the configs used by ESLint. Thus, ESLint will not only check the code quality, but also format the code for us by using Prettier under the hood. This simplifies things for us, because the linting and formatting configuration will be stored in one file.

So, here is a list of steps that I followed to add and configure both ESLint and Prettier to Create React APP project:

1. Add ESLint as as a dev dependency:

```shell
yarn add eslint --dev
```

2. Create the `.eslintrc` configuration file by using command:

```shell
yarn run eslint --init
```

When this command is run, we will have to answer few questions that will help ESLint to prepare appropriate configuration for us. The questions and my answers are listed below. In one of the questions, we can opt to use one of the popular style guides like the one from Airbnb. This is a great starting point, because we don't need to create full set of rules from scratch.

- **How would you like to use ESLint?** - To check syntax, find problems and enforce code style
- **What type of modules does your project use?** - Javascript module
- **Which framework does your project use?** - React
- **Does your project use TypeScript?** - No
- **Where does your code run?** - Browser
- **How would you like to define a style for your project?** - Use a popular style guide
- **Which style guide do you want to follow?** - Airbnb
- **What format do you want your config file to be in?** - JSON

After answering the questions the required packages will be added to our dependencies.

3. Add Prettier and its ESLint config and plugin as a dev dependency:

```shell
yarn add --dev --exact prettier eslint-config-prettier eslint-plugin-prettier
```

More on ESLint configs and plugins in next section.

4. Create `.eslintignore` and `.prettierignore` files in the project root and define files or directories that should be ignored:

```shell
build
node_modules
```

5. Update ESLint configuration in `.eslintrc` file. The configuration below is what works for me currently, each setup is a combination of personal preferences and project needs:

```json
{
  "plugins": ["react", "prettier"],
  "extends": ["react-app", "airbnb", "prettier", "prettier/react"],
  "rules": {
    "prettier/prettier": [
      "error",
      {
        "singleQuote": true
      }
    ],
    "react/jsx-filename-extension": [
      "warn",
      {
        "extensions": [".js", ".jsx"]
      }
    ]
  }
}
```

It is tough to understand what is going on here at the beginning, especially because the [ESLint configuration guide](https://eslint.org/docs/user-guide/configuring) is quite vast, and many articles about setting up the ESLint in CRA just provide you similar snippets to copy-paste without giving much explanation. See next section for details about this configuration.

## Configuration details

Here are some explanations what is happening in the configuration shown above.

### Plugins

By default, ESLint defines multiple [rules](https://eslint.org/docs/rules/) that we can use, e.g. `no-unused-expressions` or `space-before-function-paren`. It is possible however to use a parser to write additional rules - imagine for example a rule to enforce having a special copyrights comment at the beginning of each file.

Plugins are collections of such rules. In the `plugins` property of ESLint configuration we specify names of the plugins that we want to include. It is important to know that including a plugin does not mean that its rules are automatically applied in our linting. We need to specify the rules we want to apply in the `rules` property. A plugin might also export an ESLint config with preconfigured rules, which we can apply in the `extends` property.

There is a special and slightly complicated [naming convention](https://eslint.org/docs/user-guide/configuring#naming-convention) for including a plugin. Comments below refer the names of the packages with plugins that we include:

```json
  "plugins": [
    "react", // eslint-plugin-react
    "prettier" // eslint-plugin-prettier
  ]
```

Package [eslint-plugin-react](https://github.com/yannickcr/eslint-plugin-react) contains React specific ESLint rules, and [eslint-plugin-prettier](https://github.com/prettier/eslint-plugin-prettier) implements Prettier rules as ESLint ones.

### Extends

In the `extends` property we can specify one or more shareable ESLint configs that we want to apply. As mentioned previously, configs can contain preconfigured rules, so instead of configuring all rules manually we can just use already existing config that does that for us. The [naming convention](https://eslint.org/docs/developer-guide/shareable-configs#using-a-shareable-config) for using a config is also quite complicated. Comments below refer the names of the packages with configurations that we we are including:

```json
  "extends": [
    "react-app", //eslint-config-react-app
    "airbnb", // eslint-config-airbnb
    "prettier", // eslint-config-prettier
    "prettier/react" //eslint-config-prettier/react,
  ]
```

CRA comes with an [`eslint-config-react-app`](https://www.npmjs.com/package/eslint-config-react-app) base ESLint configuration. Here we are keeping it and adding additional other configs on top of it. Package [`eslint-config-airbnb`](https://www.npmjs.com/package/eslint-config-airbnb) is a config of the popular style guide that we added during ESLint init process. The last two configs come from [`eslint-config-prettier`](https://github.com/prettier/eslint-config-prettier) package and they disable all ESLint rules that might conflict with Prettier.

### Rules

In the `rules` property we can enable and configure ESLint rules. This is the place where we are applying rules from plugins that we previously included in the `plugins` property. Each rule has a severity that we can specify:

- `0` or `"off"` - rule is disabled
- `1` or `"warn"` - rule violation causes a warning
- `2` or `"error"` - rule violation causes an error with exit code 1

Some rules can have also additional options to set. In our config we have two rules specified. See the comments below for the detailed breakdown:

```json
  "rules": {
    "prettier/prettier": [ // "prettier/prettier" rule from "eslint-plugin-prettier"
      "error", // violation of the rule will cause an error
      {
        "singleQuote": true // option that sets single quotes as the valid quotes
      }
    ],
    "react/jsx-filename-extension": [ //"react/jsx-filename-extension" rule from "eslint-plugin-react"
      "warn", // violation of the rule will cause a warning
      {
        "extensions": [".js", ".jsx"] // option to allow using JSX syntax within both ".js" and ".jsx" file extensions
      }
    ]
  }
```

## Using ESLint CLI

After all setup is done, we can use our configured ESLint to check the JavaScript files in our app. The basic ESLint CLI command allows us to lint specific files or all files in a location:

- `yarn eslint fileA.js fileB.js` - runs ESLint to check specified files
- `yarn eslint location/**/*.js` - runs ESLint to check all `.js` files in specified location

The result will be printed to console where we can examine the problems. Some problems, such as incorrect quotes, can be fixed automatically by using additional `--fix` flag:

- `yarn eslint location/**/*.js --fix`.

Instead of typing the commands manually each time, we can create handy scripts in the `package.json`:

```json
"scripts": {
  "lint": "eslint 'src/**/*.{js,jsx}'",
  "lint-fix": "npm run lint -- --fix"
}
```

So if we want to lint all files in `src` folder, we can just use the `npm run lint` command.

More details about ESLint CLI can be found [here](https://eslint.org/docs/user-guide/command-line-interface).

## Using ESLint in VSCode

This part is not required for ESLint to work, but it is very convenient to have the IDE configured. The instruction below is for VSCode. General idea is to configure IDE to see all problems directly in the code as we type. IDE can also fix some of the issues and format the code for us. Here are the steps to do it:

1. Install extension for ESLint. I am using [VS Code ESLint](https://github.com/Microsoft/vscode-eslint).

2. Install extension for Prettier. I am using [Prettier Formatter for Visual Studio Code](https://github.com/prettier/prettier-vscode).

3. Configure VSCode settings. Below are the relevant settings that we can add in the VSCode `settings.json` file.The important thing here is that we are enabling the editor to use ESLint to fix all fixable problems (including incorrect formatting) after each file save. Because of that, to avoid conflicts, we need to disable the default IDE and Prettier formatting in `.js` files.

```json
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  },
  "[javascript]": {
    "editor.formatOnSave": false
  },
  "eslint.codeAction.showDocumentation": {
    "enable": true
  },
  "eslint.alwaysShowStatus": true,
  "prettier.disableLanguages": [
    "js"
  ]
```

When the steps are done, we should be able to see the problems in the code. All problems will be also listed in the "Problems" panel.
