# Ngrx
State management in Angular

## Project setup
Use Node v18.12.0

Use NVM to switch node versions.
```
nvm list
nvm install 18.12.0
nvm use 18.12.0
```

Starting the project:

```
npm install
cd server  
npm run server (backend server)

To start UI:
npm start  (on root directory)
http://localhost:4200
```

See proxy.json, and url call of type /api/ gets routed to our backend server running in localhost on port 9000.

The Project already includes NgRx, and NgRx Devtools (used to visualise sate in browser devtools), but if setting up a project from scratch, run these commands:

```
ng add @ngrx/store  //Creates entry for StoreModule in app.module.ts
ng add @ngrx/store-devtools //Creates entry for StoreDevtoolsModule in app.module.ts
```

Install Redux DevTools browser extension for your respective browser, to visualise the state in devtools. 

## Concepts

As a pre-requisite to using ngrx, go yhrough the rxjs repo, as ngrx sate is based on reactive programming.

Why use state management solutions? Ans: If you keep data in data members of components, then the lifecycle of the data is tied to the lifecycle of the component. ie. if you move away from component to a different screen, the component gets destroyed, and when you come back, it gets recreated, and hence you again have to fetch data from the backend to store the data in it's members.

Ngrx is the Angular version of React's Redux. NgRx stores the application state in an RxJS observable inside an Angular service called Store. At the same time, this service implements the Observable interface.

## Ngrx components:

**Actions:** Action is an event that you dispatch to update the data in the state. Every action that you dispatch has an Action-type and a payload (payload is optional).

**Reducers:** Reducer is a function that acts on Action events to update the state. A reducer is named a reducer, as it is based on reduce functional programming: ita takes original state, and action as 2 parameters, and returns a new updated state. Reducer function should return a new state instead of modifying existing, else time travelling debugger would not work. 

**Selectors:** Ngrx store is basically an observable, which you can subscribe throughout your application and listen to state changes. So you can do store.pipe(map(state=> ...) as you do with Rxjs observables. The Selectors make querying of data from the store more efficient, by listening to the store only when the data has changes, ie removing the duplicates values, and preventing them from reaching the frontend each time. Select also do the Rxjs work of map() method.  

**Effects:** Used to perform some side effects on store updating actions. Like calling a backend to save the data, or updating the data in browser's localStorage.

### Things to note in this POC code base

1. App.modue.ts eagerly loads only the auth module. All other modules are loaded lazily, ie only logged in user should get other routes available.

https://github.com/ramit21/ngrx/blob/813fd92f243543546f31c619ae6d28cad703ac40/src/app/app.module.ts#L56

Lazy load course module:

https://github.com/ramit21/ngrx/blob/813fd92f243543546f31c619ae6d28cad703ac40/src/app/app.module.ts#L31

2. Login auth state management: 

Dispatch login action on successful user login:

https://github.com/ramit21/ngrx/blob/813fd92f243543546f31c619ae6d28cad703ac40/src/app/auth/login/login.component.ts#L50

Create your action using ngrx provided 'createAction'. The first argument in [] is a string, which specifies a unique source. The second string after [] is the event/or the command this action corresponds to. And the final argument is the data being passed over to the action.

https://github.com/ramit21/ngrx/blob/813fd92f243543546f31c619ae6d28cad703ac40/src/app/auth/auth.actions.ts#L5

Also note that in the dispatch method above, we simply send user as a parameter in login actionCreator. Actually, it is equivalent to login({user:user}). It is a Typescript feature that if the value of the property has the same name as the property itself, you can simply pass in the property name.

Note the logout action, which is an example of an Action without a payload:

https://github.com/ramit21/ngrx/blob/813fd92f243543546f31c619ae6d28cad703ac40/src/app/auth/auth.actions.ts#L13

You can group similar actions using a Typescript trick:

https://github.com/ramit21/ngrx/blob/master/src/app/auth/action-types.ts

Next, check the reducers which act on the dispatched action events:

https://github.com/ramit21/ngrx/blob/813fd92f243543546f31c619ae6d28cad703ac40/src/app/auth/reducers/index.ts#L25

Store selectors using Ngrx createSelector:

https://github.com/ramit21/ngrx/blob/813fd92f243543546f31c619ae6d28cad703ac40/src/app/auth/auth.selectors.ts#L9

Ngrx effect is used to capture the user login store update, and update localStorage as well to avoid browser refresh. Effects need to be registered with app.module.ts as well:

https://github.com/ramit21/ngrx/blob/813fd92f243543546f31c619ae6d28cad703ac40/src/app/app.module.ts#L67

https://github.com/ramit21/ngrx/blob/813fd92f243543546f31c619ae6d28cad703ac40/src/app/auth/auth.module.ts#L26

https://github.com/ramit21/ngrx/blob/813fd92f243543546f31c619ae6d28cad703ac40/src/app/auth/auth.effects.ts#L11

Also note '{dispatch: false}' in the effect above that ensures that store update does not trigger any further actions, else it can create a loop of action dispatching.

3. List of reducers is updated in app.module.ts:

https://github.com/ramit21/ngrx/blob/813fd92f243543546f31c619ae6d28cad703ac40/src/app/app.module.ts#L57

4. Note how we redirect the user to login page if an un authenticated user tries to access a page directly:

https://github.com/ramit21/ngrx/blob/813fd92f243543546f31c619ae6d28cad703ac40/src/app/auth/auth.guard.ts#L24

5. 
