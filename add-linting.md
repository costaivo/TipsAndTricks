## Adding Linting to your Project

## Before we begin, you will have to install npm and then npx.

- Download the ESLint and Prettier extensions for VSCode.
- Install the ESLint and Prettier libraries into your project.
- In your project’s root directory, run command:

  ```cmd
  npm install -D eslint prettier
  ```

- Install the Airbnb config. run command:

  ```cmd
  npx install-peerdeps --dev eslint-config-airbnb
  ```

- Install eslint-config-prettier (disables formatting for ESLint) and eslint-plugin-prettier (allows ESLint to show formatting errors as we type)

```cmd
  npm install -D eslint-config-prettier eslint-plugin-prettier
```

- Create `.eslintrc.json` file in your project’s root directory:

```json
{
  "extends": ["airbnb", "prettier"],
  "plugins": ["prettier"],
  "rules": {
    "prettier/prettier": ["error"]
  }
}
```

- Create `.prettierrc` file in your project’s root directory. This will be where you configure your formatting settings. I have added a few of my own preferences below, but I urge you to read more about the Prettier config file

```json
{
  "printWidth": 100,
  "singleQuote": true
}
```

- The last step is to make sure Prettier formats on save.
  Insert **"editor.formatOnSave": true** into your User **Settings** in VSCode.
