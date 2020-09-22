# Frontend

## Requirements

- Yarn
- Node
  - See `.nvmrc` for exact version
  - To take the version in use if using nvm, use `nvm use`
- ESlint & Prettier editor plugins if available

## Code style & linting

Code is styled using [Prettier](https://prettier.io/). In your editor settings, turn on automatic formatting if available and let Prettier handle how code looks. [ESlint](https://eslint.org/) is also used to avoid common pitfalls.

To run Prettier manually, use `yarn format:check`. If you want to manually fix all errors, run `yarn format:fix`.

To check for linting errors, run `yarn lint`.

## Development

Run `yarn start` to start development server.

## Issues?

Linting not working in VSCode? Add to your workspace settings the following line:

```
"eslint.workingDirectories": ["./frontend"]
```
