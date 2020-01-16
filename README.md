# React Issues

![React Issues Demo](/cover.gif)

#### Project setup

```
npm install
```

#### Compiles and hot-reloads for development

```
npm run serve
```

#### Compiles and minifies for production

```
npm run build
```

#### Deployed to Netlify (with continuous deployment)

URL: https://react-issue-search.netlify.com/

## Frameworks + Tools

- Vue.js project (managed using Vue CLI)
- NPM for package and dependency management
- Sass as a CSS pre-processor
- Babel as a Javascript compiler
- Webpack for module bundler
- ESLint for a code linter

## Code Style and Formatting

- Javascript: Airbnb JavaScript Style Guide
- CSS: BEM (Blocks, Elements and Modifiers) naming convention
- Prettier: Basic code fomatting

## Features

#### Search Bar

- Autocomplete search bar which fetches matching issues from the facebook/react repository using the Github Search API
- Navigate the search results using keyboard shortcuts
- See issue title, tags (colored), status and date in results
- Open issue directly from the search bar

#### Github API

- The Github Search API is used to fetch the matching issues
- The API has a max-limit of 30 calls/minute (due to not being a Github App), and a message is displayed when the limit is exceeded
- A debouncer is used to make ensure that the API calls are used effectively and not wasted
- If no results are found, a message is displayed too

#### Styling

- Hierarchical styling in Sass using BEM class naming convention
- General styling inspiration from Github repositories
- Typography
  - Font-family: Inter UI, a great open source font made especially for UI design

#### Future Improvements

- Increase the Github API call limit. This would require setting up the app as a Github App and having users sign in with their Github accounts.

#### Mouse and Keyboard Actions

- Start search input: <kbd>TAB</kbd> or click
- End cell input: <kbd>ESC</kbd>
- Navigate search results
  - Up: <kbd>&uarr;</kbd>
  - Down: <kbd>&darr;</kbd>
- Open selected result: <kbd>ENTER</kbd> or click
