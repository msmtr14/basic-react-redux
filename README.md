# react-redux-boiler-plate
Modular starter kit for React+Redux+React Router projects.

* [NPM scripts](#NPM-scripts)
* [Features](#Features)
* [Testing](#Testing)
* [Documentation](./docs/ru/main.md)

## NPM scripts
### To start localy
- ```npm run dev``` for development environment in watch mode
- ```npm run prod``` for production environment in watch mode

### To build localy (see build folder)
- ```npm run build:dev``` for development environment without watch mode
- ```npm run build:prod``` for production environment without watch mode
- ```npm run build:gh-pages``` for building production bundle for gh-pages

### To start bundle analyzer
- ```npm run analyze:dev``` for development environment
- ```npm run analyze:prod``` for production environment

### To start isomorphic server
- ```npm run server:dev``` for development environment in watch mode
- ```npm run server:prod``` for production environment without watch mode

### To deploy
- ```npm run deploy:gh-pages``` for gh-pages

### To start yeoman generator create-feature
- ```npm run yeoman```

## Features
- [x] Typescript 2.x
- [x] React 16.x
- [x] React-router 4.x
- [x] Redux
- [x] Redux-saga for side-effects
- [x] SCSS
- [x] React-JSS
- [x] BEM methodology
- [x] Webpack 4.x
- [x] Tests (Jest, sinon, enzyme)
- [x] Test coverage
- [x] Hot reload
- [x] Yeoman blocks generator tasks (features, modules, ... )
- [x] Code splitting (async chunks loading)
- [x] Isomorphic
- [x] Material-UI


## Testing

Tests use framework [Jest](http://facebook.github.io/jest/)

### Launch

* `npm test` OR `npm t` - one-time test run
* `npm run test:watch` - running tests in watch mode
* `npm run test:debug` - run with connectivity for debugging
(
  [Chrome](http://facebook.github.io/jest/docs/en/troubleshooting.html#content) /
  [VSCode](http://facebook.github.io/jest/docs/en/troubleshooting.html#debugging-in-vs-code) /
  [Webstorm](http://facebook.github.io/jest/docs/en/troubleshooting.html#debugging-in-webstorm)
).
* `npm run test:coverage` - launch with code card generation Results can be opened in a browser.
`<projectDir>/coverage/lcov-report/index.html`.

### [Snapshot Testing](http://facebook.github.io/jest/docs/en/snapshot-testing.html#content)

```typescript
import React from 'react';
import { shallow } from 'enzyme';
import GenericDateInput from './../GenericDateInput';

it('renders correctly', () => {
  const component = shallow(<GenericDateInput />)

  expect(component.debug()).toMatchSnapshot();
});
```

After the first run of the test, a reference snapshot is created, which will be placed in the `__snapshots__` folder next to the file
test. It needs to be checked for correctness. After changes in the layout of the component, changes will be displayed in the terminal.
occurred in the component, and if these changes are expected, then new snapshots need to be fixed, for that it’s enough
in the terminal, press the `" u "` key (provided that the tests are run in watch mode). `WARNING !!! All snapshots will be overwritten,
not coinciding with the reference! `

> You can use [it.only (name, fn, timeout)] (http://facebook.github.io/jest/docs/en/api.html#testonlyname-fn-timeout) or [describe .only (name, fn)] (http://facebook.github.io/jest/docs/en/api.html#describeonlyname-fn) if we want to update snapshots for the test group.

If errors occur during testing in watch mode:

For MacOS (`Error: watch EMFILE`): Delete the watchman globally installed via npm or yarn, if any, and reinstall via brew.

For Linux (`Error ENOSPC`): use this command:
```
echo fs.inotify.max_user_watches=524288 | sudo tee -a /etc/sysctl.conf && sudo sysctl -p
``` 
[reference to issue](https://github.com/facebook/jest/issues/3254)
