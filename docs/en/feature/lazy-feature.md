# Lazy feature

Due to the fact that most feature containers are not used on all pages of the application (and sometimes not even for all types of users), it can be concluded that it is better to load the code for a specific feature at the time of need, and not during the initialization of the application. Lazy loading features allows you to significantly speed up the initial loading and initialization of the application.

Connection of lazy features to the redux-site occurs automatically using the HOCs described below. If the feature is not lazy, then you need to collect data to connect to the store (`IReduxEntry`) and make a manual connection in the file` src / core / configureApp.tsx`.

## What needs to be done to make the feature become lazy

1. Collect all useful feature data in the entry.ts file (there is a `makeFeatureEntry` helper for this) and export the resulting object and the type of this object.
`` typescript
const entry = makeFeatureEntry (
  containers, actions, selectors,
  {
    reducers: {categorySelect: reducer},
    sagas: [getSaga],
  },
);

type Entry = typeof entry;

export {Entry, entry};
`` `
2. In the file `loader.ts` you need to write a loader, which will contain a dynamic import of an object with feature data from` entry.ts`.
`` typescript
import {Entry} from './entry';

export function loadEntry (): Promise <Entry> {
  return import (/ * webpackChunkName: "featureName" * / './entry').then (feature => feature.entry);
}
`` `
3. In the `index.ts` file you need to export the loader and the type of the object with the feature data.
`` typescript
export {Entry} from './entry';
export {loadEntry} from './loader';
`` `
> You cannot import feature files directly from other places of the application, otherwise the selection of the code in a separate bundle will not work and this code will fall into the main bundle of the application

## How to use a lazy feature

### Basic method

From a lazy feature, we can take only the loader function and the type of the object with the feature data, so we can call the loader and wait for the promise returned by it to fill the data. In order for react-containers features to work properly, you need to remember to connect the feature to the redux-server.

### HOC `featureConnect`

With the help of HOC's `featureConnect`, you can simplify the process of obtaining feature data and connecting the feature to the redux-server. It can be used only in modules. It accepts a map object with loaders of the features of interest to us and a react component at the input; the keys of this object must match the keys of the props of the react component being wrapped. After successful loading of features, they will be automatically connected to the redux-server and objects with these features will be thrown into the reactable component to be wrapped. You can also transfer a preloader to it, which will be displayed during feature loading.

`` typescript
import * as React from 'react';
import {featureConnect} from 'core';

import {RouteComponentProps} from 'react-router-dom';

import * as lazyFeature from 'features / lazyFeature';

interface IOwnProps {
  lazyFeatureEntry: lazyFeature.Entry;
}

type Props = IOwnProps;

class SomeModuleComponent extends React.PureComponent <Props> {
  public render () {
    const {LazyFeatureContainer} = this.props.lazyFeatureEntry.containers;

    return (
      <LazyFeatureContainer />
    );
  }
}

export default (
  featureConnect ({lazyFeatureEntry: lazyFeature.loadEntry}, <Preloader />) (
    SomeModuleComponent,
  )
);

`` `

### HOC `containersProvider`

If in a certain feature we need the functionality of another already existing lazy feature, then we can use the `containersProvider` HOC. It performs the same work as the HOC `featureConnect`, but accepts an array of keys with the names of the required containers. The reactable component to be wrapped must accept the corresponding signature for these ReactType keys.

Unlike `featureConnect` requires preliminary configuration, in the file` ContainersProvider.tsx` you need to import the loader and the object type with the feature data, and extend the interface `IContainerTypes` and the object` containerLoadersDictionary`. The interface `IContainerTypes` is exported and we can get through it access to the types of containers connected to HOC features to typify the props of the react-containers wrapped by this HOC.

File `ContainersProvider.tsx`:

`` typescript
import * as firstLazyFeature from 'features / firstLazyFeature';
import * as secondLazyFeature from 'features / secondLazyFeature';

interface IContainerTypes {
  ContainerFromFirstFeature: firstLazyFeature.Entry ['containers'] ['ContainerFromFirstFeature'];
  ContainerFromSecondFeature: secondLazyFeature.Entry ['containers'] ['ContainerFromSecondFeature'];
}

const containerLoadersDictionary: LoadersMap = {
  ContainerFromFirstFeature: firstLazyFeature.loadEntry,
  ContainerFromSecondFeature: secondLazyFeature.loadEntry,
};

...

export {IContainerTypes};
`` `

Example of use:

`` typescript
import * as React from 'react';
import {IContainerTypes, containersProvider} from 'core';

interface IProps {
  ContainerFromFirstFeature: IContainerTypes ['ContainerFromFirstFeature'];
  ContainerFromSecondFeature: IContainerTypes ['ContainerFromSecondFeature'];
}

class SomeFeatureComponent extends React.PureComponent <IProps> {
  public render () {
    const {ContainerFromFirstFeature, ContainerFromSecondFeature} = this.props;

    return (
      <div>
        <ContainerFromFirstFeature />
        <ContainerFromSecondFeature />
      </ div>
    );
  }
}

export default (
  containersProvider (['ContainerFromFirstFeature', 'ContainerFromSecondFeature'], <Preloader />) (
    SomeFeatureComponent,
  )
);
`` `