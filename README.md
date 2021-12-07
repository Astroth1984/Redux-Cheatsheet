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
        
        remove: (state) => (state = {}),
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

## Redux Async Actions

In this main case, we fetch data from a back-end server or connecting to an API, so we have to handle our async http calls in redux.
There are three solution to do that : **Redux Thunk** , **The default solution of Redux toolkit** or using **Custom Reducers**.

The following section describes how we can handle _Async Actions_ with **Custom Reducers**.

### src/redux/userRedux.jsx

````javascript
import { createSlice } from '@reduxjs/toolkit';

export const userSlice = createSlice({
     name: "user",
     initialState: {
       userInfo: {
          name: "",
          email: ""
       },
       pending: false,
       error: false
     },
     // Reducers and each countains our actions
     reducers: {
        updateStart: (state) => {
          state.pending = true;
        },
        updateSuccess: (state, action) => {
          state.pending = false;
          state.userInfo = action.payload;
        },
        updateFailure: (state) => {
          state.pending = false;
          state.error = true;
        },
     },
});

// Let's export our actions
export const { updateStart, updateSuccess, updateFailure } = userSlice.actions;

// export reducer to use it in our store
export default userSlice.reducer;
````

### Api Calls
As i prefer to do in a real project, is to create a new file under `src/redux/apiCalls.js` which contains our *GET* *POST* *PUT* *DELETE* http calls.

**apiCalls.js**

````javascript
import axios from "axios";
import { updateStart, updateSuccess, updateFailure } from "./userRedux";


// export reducer to be used in our UpdateComponent
export const updateUser = async (user, dispatch) => {
  
  // first when we click submit button we want to dispatch updateStart() action first
  dispatch(updateStart());
  try {
    const res = await axios.post("http://localhost:8800/api/users/1/update", user);
    //Dispatch Success action and pass in our payload
    dispatch(updateSuccess(res.data));
  } catch (err) {
    dispatch(updateFailure());
  }
};
````

### Update.jsx

In this case we change the approache how we dispatch our actions like so :

````javascript
import { useState } from 'React';
import { useSelector, useDispatch } from 'react-redux';
import { updateUser } from '../../redux/apiCalls';

 
 export default function Update(){
  // ...
  const user = useSelector((state) => state.user.userInfo);
  const dispatch = useDispatch();
  
  const handleUpdate = (e) => {
    e.preventDefault();
    //call the action that we want to dispatch
    // in this case it's the update action
    // inside the dispatched action, we give our payload
    updateUser({name, email}, dispatch);
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

## Redux Async Pending & Error

For further usage, we can disable the _update button_ while fetching data from the server, and make use of our **updateStart()** action. It's just an example, you can be creative and make whatever you like with your custom reducers according to the behavior you want to render.

So in Our case, we will disable the the _update button_ when **updateStart()** action is called. Display an error message when **updateFailure()** is called. Show success message when **updateSuccess()**.

### update.css

````css
.updateButton {
  margin-top: 10px;
  border: none;
  padding: 5px  10px;
  background-color: rgb(97, 161, 161);
  color: white;
  border-radius: 10px;
  cursor: pointer;
  font-weight: 500;
}

.updateButton:disabled{
  cursor: not-allowed;
  background-color: rgb(136, 168, 168);
}

.error{
  color: red;
  font-size: 14px;
  margin-left: 20px;
}

.success{
  color: green;
  font-size: 14px;
  margin-left: 20px;
}
````

In **Update.jsx** component we custom the update button :

````jsx
export default function Update(){
  // ...
  //let s destructure our state
  const {userInfo, pending, error } = useSelector((state) => state.user);
  const dispatch = useDispatch();
  
  const handleUpdate = (e) => {
    e.preventDefault();
    //call the action that we want to dispatch
    // in this case it's the update action
    // inside the dispatched action, we give our payload
    updateUser({name, email}, dispatch);
  };
  
  return (
    <!-- ... -->
    
    <div className='formItem'>
      <label>Username</label>
      <input
        className='formInput'
        type='text'
        placeholder={userInfo.name}
        onChange={(e)=>setName(e.target.value)}
       />
    </div>
  
    <div className='formItem'>
      <label>Email</label>
      <input
        className='formInput'
        type='text'
        placeholder={userInfo.email}
        onChange={(e)=>setEmail(e.target.value)}
      />
    </div>
    <!-- ...  -->
    <button
      disabled={user.pending}
      className="updateButton"
      onClick={handleUpdate}
    >
       Update
    </button>
    
    {user.error && <span className="error">Something went wrong!</span>}
    {user.pending === false && (
        <span className="success">Account has been updated!</span>
     )}
     
    <!-- ... -->
  );
  
 };
````
## Async Redux with Thunk

With **Redux Async Thunk** we should add to our __userRedux.js__ one more function *createAsyncThunk*, and it provides us with some default reducers:

**`pending` : `users/requestStatus/pending`**

**`fulfilled` : `users/requestStatus/fulfilled`**

**`rejected` : `users/requestStatus/rejected`**


## Redux DevTools

For a better understanding, i highly recommand installing the **[Redux DevTools](https://github.com/reduxjs/redux-devtools)** extension on Chrome Browse or any browser of your choice, so that you can track the state of your reducers. It's very usefull and time saver.





