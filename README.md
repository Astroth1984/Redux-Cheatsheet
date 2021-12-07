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
     // Reducers and each countains our actions
     reducers: {
        update: (state,action) => {
          
        }
     }
})

````

## redux/store.js

The store will containe our user infromation (username, email ...)

````javascript
import { configureStore } from '@reduxjs/toolkit';
````

## State management with Redux

In a real world project, let's imagine we have an `Update.jsx` component, where we update the user value and we want that the updated value used by all the React components who render `user` object. So in the *UpdateComponent* :

````javascript
 import { useState } from 'React';
 
 export default function Update(){
  const [name, setName] = useState('');
  const [email, setEmail] = useState('');
  
 }
````

In the return section of `Ùpdate.jsx` we set the _userObject_ like follow:

````jsx
  <div className='formItem'>
    <label>Username</label>
    <input
      className='formInput'
      type='text'
      placeholder='username'
      onChange={(e)=>setName(e.target.value)}
    />
  </div>
  
  <div className='formItem'>
    <label>Email</label>
    <input
      className='formInput'
      type='text'
      placeholder='email'
      onChange={(e)=>setEmail(e.target.value)}
    />
  </div>
````
