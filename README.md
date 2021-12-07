# Redux-Cheatsheet

## Main Use

Redux is nothing but an open-source javascript library that contains the state of the application. The working of Redux is very simple to understand. There are three building parts of the Redux as- Store, Actions, and Reducers.

## Redux For React (installation packages)

To use Redux Library in ReactJs application, we must install the following dependencies:

**- Redux Toolkit:** `npm i @reduxjs/toolkit` or `yarn add @reduxjs/toolkit`.

**- Redux Core:** `npm i redux` or `yarn add redux`.

**-React-Redux:** `npm i react-redux` or `yarn add react-redux`.


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
     // Reducers and each countains our actions
     reducers: {
        update: (state,action) => {
          state.name = action.payload.name;
          state.email = action.payload.email;
        },
        
        remove: (state) => {
          state = null;
        },
     },
});

// Let's export our actions
export const {update, remove} = userSlice.actions;

// export reducer to use it in our store
export default userSlice.reducer;
````

## redux/store.js

The store will containe our user infromation (username, email ...)

````javascript
import { configureStore } from '@reduxjs/toolkit';
import userReducer from './userRedux';

export default configureStore({
  reducer: {
    user: userReducer,
  },
});
````

## State management with Redux

In a real world project, let's imagine we have an `Update.jsx` component, where we update the user value and we want that the updated value used by all the React components who render `user` object.

### Update.jsx

````javascript
 import { useState } from 'React';
 import { useSelector } from 'react-redux';
 
 export default function Update(){
  const [name, setName] = useState('');
  const [email, setEmail] = useState('');
  const user = useSelector((state) => state.user);
  
 }
````

In the return section of `Update.jsx` we set the _userObject_ like follow:

````jsx
  <div className='formItem'>
    <label>Username</label>
    <input
      className='formInput'
      type='text'
      placeholder={user.name}
      onChange={(e)=>setName(e.target.value)}
    />
  </div>
  
  <div className='formItem'>
    <label>Email</label>
    <input
      className='formInput'
      type='text'
      placeholder={user.email}
      onChange={(e)=>setEmail(e.target.value)}
    />
  </div>
````

So what we need to do now is to send the updated values to our __reducer__ and they are going to be basically our __action payload__.
To do that we should **Dispatch our action** when we submit our form, for that we use a React Hook called `useDispatch()`:

````javascript
import { useState } from 'React';
import { useSelector, useDispatch } from 'react-redux';
import { update } from '../../redux/userRedux';

 
 export default function Update(){
  // ...
  const user = useSelector((state) => state.user);
  const dispatch = useDispatch();
  
  const handleUpdate = (e) => {
    e.preventDefault();
    //call the action that we want to dispatch
    // in this case it's the update action
    // inside the dispatched action, we give our payload
    dispatch(update({name, email});
  };
  
  return (
    <!-- ...  -->
    <button
      className="updateButton"
      onClick={handleUpdate}
    >
       Update
    </button>
  );
  
 };
````

- Since we have created our *remove reducer* and export our action, it'll be used like so :

````jsx
import { useSelector, useDispatch } from 'react-redux';
import { update, remove } from '../../redux/userRedux';


export default function Update(){
  // ...
  const user = useSelector((state) => state.user);
  const dispatch = useDispatch();
  
  // ...
  const handleDelete = (e) => {
    e.preventDefault();
    dispatch(remove();
  };
  
  // ...
  
  return (
    <!-- ...  -->
    <button
      className="deleteButton"
      onClick={handleDelete}
    >
       Delete Account
    </button>
    <!-- ... -->
  );
  
 };
````

### index.js

To access our __store__ and __actions__ , we should wrap our entire application and provide our store in any component inside `<App />`

````jsx
import React from "react";
import ReactDOM from "react-dom";
import App from "./App";
import store from "./redux/store";
import { Provider } from "react-redux";

ReactDOM.render(
  <Provider store={store}>
      <App />
  </Provider>,
  document.getElementById("root")
);
````

### Navbar.jsx

Assuming that in the application navbar we show our `user`, the following is the details about how the _Navbar.jsx_ component can call the updated *userObject* in our __store__ using `useSelector() Hook`.

````jsx
import React from "react";
import { useSelector } from "react-redux";

const Navbar = () => {
  const name = useSelector((state) => state.user.name);
  
  return (
    <span className="navbarName">{name}</span>
  )
};
````

