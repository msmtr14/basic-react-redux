A brief description of the architecture

The main building blocks of the application are modules, features and services.

## Service

This functionality is often used by modules and features, knows nothing about the rest of the application. May contain:
* class that encapsulates any service-specific logic. An instance of this class must be bound to a DI container.
* red-logic (its own branch in the stack, a set of action-creators, reysery and, possibly, the saga)
* react containers
* React Higher-Order Components (HOC), can encapsulate work with the service class, throw certain props into wrapped containers or control the drawing of the wrapped container depending on the state of the service.

> Examples: the i18n service contains: red-logic for receiving and storing translations, changing the current language; a class that will track the language change, update the translation function and notify subscribers; HOC, which passes the translation function to the component being wrapped, and when the language is changed, calls forceUpdate, thereby triggering a redraw and passing the updated translation function.

## feature

The main architectural block, basically the whole project is a set of features. A feature is a narrowly focused functionality that can be implemented using the React + Redux bundle, and has its own branch in the redux side. At the output, the feature gives react-containers with the lowest possible interface and a set of functions for connecting the feature to the redux-server.

He knows nothing about routs, he can use services and shared components. Cannot directly access another feature. This reduces the hooking of the features and allows you to put features into separate bundles that will be loaded only when this feature is really needed.

If you need to use a react-container from another feature, you can request a container of a specific signature in the prop and get it from the client or using the HOC `ContainersProvider` ( containers in wrapped container).

You need to highlight a separate feature if:
* This functionality is used in several places (modules);
* functionality can be unambiguously classified (for example, authorization, downloading news);
* there are no routs and no interaction with them in the process of work is necessary.

> Examples: the product search feature contains: red-logic on loading, storing, filtering and pagination of the product list; container search line, container with pagination management. The client can get search results, for example, using the onLoad callback of the search box container.

[Description of the principles of working with features] (./ feature / index.md)

## Module

The module associates features, expands their functionality and distributes them to receptions. May contain redox logic and react-containers. It is required to provide a root routing, inside of which the module's routing tree may be implemented, and which will be drawn in an empty div container.

You need to select a module if:
* there will be a routing, at each address of which there is an independent and heterogeneous functional, which can be distinguished into an independent part of the program;
* The functionality is really heterogeneous and more extensive than just loading the user and displaying his data;

> Examples: the Profile module contains features: editing personal information, viewing personal information, setting up notifications, viewing messages.