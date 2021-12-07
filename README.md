# Redux-Cheatsheet

## Main Use

Redux is nothing but an open-source javascript library that contains the state of the application. The working of Redux is very simple to understand. There are three building parts of the Redux as- Store, Actions, and Reducers.

## Redux For React

To use Redux Library in ReactJs application, we must install the following dependencies:
**- Redux Toolkit:** `npm i @reduxjs/toolkit` or `yarn add @reduxjs/toolkit`
**- Redux Core:** `npm i redux` or `yarn add redux`

## Implimentation

In a real application, i prefer having a Redux folder under src `src/redux`, that contains strore, reducers ..., basically Redux folder represents the redux boilerplate for our application.

## redux/userRedux.jsx

You can name the file also as `userSlice.jsx` as mentionned in Redux documentation.

````javascript
import { createSlice } from '@reduxjs/toolkit';

export const userSlice = createSlice({
  
     /*Reducer name*/
     name: "user",
     initialState: {
         name: "",
         email: ""
     },
})

````

## redux/store.js

The store will containe our user infromation (username, email ...)

````javascript
import { configureStore } from '@reduxjs/toolkit';
````
