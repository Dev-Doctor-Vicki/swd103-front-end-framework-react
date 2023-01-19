# Redux Alternatives

This lesson takes things further by extending the Event Sign-Up App to use the Redux Toolkit for even easier state management.

### Extend the Event SignUp App

At this point you’ve built a fully functional and extensible redux-powered signup app that uses the various redux mechanisms of actions, dispatching, and reducers, to update a centralized store. You’ve done this with the help of the React Redux library which provides a little help in a relatively unopinionated fashion. 

However, I wouldn’t blame you if you followed along during the previous lesson thinking "wow, this is a lot of overly complex and fussy-looking code". The use of Redux isn’t too bad in component files, but I’m sure you’ll agree that in your `eventReducer` file especially, things start to look quite messy and increasingly difficult to read and maintain. Think what this could look like if you added more reducer cases in here to handle different actions!

But don’t worry, there is a much nicer way to bring Redux into your projects in the form of the **Redux Toolkit**, made by the same folks who brought you React Redux. What it offers is a much more opinionated, prescriptive approach to introducing Redux to your applications. 

In this lesson you’re going to swap out the reducer and store creation mechanisms in the React Redux library, for those offered by its much more agreeable sibling, the Redux Toolkit. You’ll need to remove some dependencies, introduce some new ones, and edit some of your files. You’ll notice subtle changes in some of your components, but quite a dramatic change in the main `eventReducer` file where you’ll see the effort of creating actions and the reducer function has become hugely simplified and much more natural-looking.

I’ve used a lot of different redux approaches across a number of commercial projects and I much prefer the one the Redux Toolkit offers.

## Start where you left off with the Events SignUp App.

### Switch up Dependencies

You need to first remove the `redux-thunk` dependency from your existing project.  The Redux Toolkit bundles this commonly-used package in with its offering so you no longer have use for this.

You can either remove the package using the explicit terminal commands, `npm remove redux-thunk`, or you can delete the `node_modules` folder in the project, edit the `package.json` file with the new package names, and run `npm instal`l again to wire everything up.

I’m going to do the former to save me having to install all the packages over again, but either approach nets the same end result.

### Add the new dependency

You only have the one new dependency to add now, so back in the terminal issue the following command:

```
npm add @reduxjs/toolkit
```

The React Redux team offer a number of libraries in their namespace which is prefixed with the ‘`@`‘ symbol, hence this package may look a little different than you’re used to.

### Change Your Files

Not every file is going to change, but a lot of them are. Fortunately, most of the changes are quite small. The major changes will be around configuring your store and in the reducer file where wyou’ll see a rather dramatic reduction in the volume of code.

Let’s get started by editing your existing components.

### Edit EventSignUpList.jsx

Open the `EventSignUpList` component and take out your code scalpel and get cutting.

Head to the bottom of the file and replace all the mapping functions with a more familiar, typical component export line:

**src/components/EventSignUpList.jsx**

```
export default EventSignUpList;
```

You no longer need to explicitly map various elements of state and dispatch to your component, as the Redux Toolkit library offers some handy Hooks you can use instead, wiring up all you need in the background.

Next, back at the top of the file, change the following line:

**src/components/EventSignUpList.jsx**

```
import { connect } from 'react-redux';
```

Remove the `connect` import and swap it for two Hooks, `useSelector` and `useDispatch`:

**src/components/EventSignUpList.jsx**

```
import { useSelector, useDispatch } from 'react-redux';
```

The first, `useSelector` will allow you to select part of the state that you need, using a helper function that you’ll create when it’s time to edit your reducer file. Similarly, `useDispatch` gives access to the `dispatch` function in the redux store to dispatch your actions.

You’ll also need to import a new action, `selectAttendees` from the `eventReducer` file. You haven’t created this just yet, but don’t worry, you’ll get to it soon enough.

In the meantime, to use these new Hooks, edit the `EventSignUp` declaration:

**src/components/EventSignUpList.jsx**

```
const EventSignUpList = () => {
    const eventAttendees = useSelector(selectAttendees);
    const dispatch = useDispatch();
```

Although you’ve kept the imported actions from your event reducer file, your component will no longer be passed them via props, so you can safely remove them from your component’s declaration. 

You do need to create a couple of variables, however, namely `eventAttendees` and `dispatch`. You can see you’re using the new Hooks to populate the new variables. The `useSelector` Hook is called with the `selectAttendees` function you imported. When you create it later in the lesson, it will allow you to grab a portion of your app’s state and, when used in conjunction with the `useSelector` Hook, provides components with access to that same slice of state. 

The dispatch variable is provided with the redux dispatch function as a result of calling the `useDispatch` Hook. You’ve kept the variable name the same for familiarity’s sake. 

The last thing is to make a subtle change to how you’re dispatching your redux actions. Notice how you haven’t had to edit the `eventAttendees` check? As far as your component’s JSX is concerned, the `useSelector` Hook has provided it with the information it needs so your null-check can remain the same here.

However, you do have to change the click events on your buttons. Scroll down a little and locate the `onClick` events on each button. The actions you’re calling here won’t change. The difference is they are no longer implicitly connected to the redux store and so won’t be automatically dispatched. All you need to do to make that happen now is to wrap the existing calls with the `dispatch()` variable you created earlier.

And that’s it; done. The completed changed component now looks like this:

**src/components/EventSignUpList.jsx**

```
import React from 'react';
import { useSelector, useDispatch } from 'react-redux';

// Actions
import { deleteEventAttendee, toggleEventAttendance, selectAttendees } from '../reducers/eventReducer';

const EventSignUpList = () => {
    const eventAttendees = useSelector(selectAttendees);
    const dispatch = useDispatch();

    return (
        <div className="box">        
            {
                eventAttendees && eventAttendees.length ?
                <>
                    <h2 className="subtitle is-size-5">Hurrah, {eventAttendees.length} {eventAttendees.length > 1 ? "people have" : "person has"} signed up to our event!</h2>
                    <table className="table is-striped is-fullwidth">
                        <thead>
                            <tr>
                                <th>Name</th>
                                <th>Email</th>
                                <th>Number</th>
                                <th>Attending</th>
                                <th>Actions</th>
                            </tr>
                        </thead>
                        <tbody>
                            {
                                eventAttendees.map(item => (
                                    <tr key={item.id}>
                                        <td>{item.name}</td>
                                        <td>{item.email}</td>
                                        <td>{item.number}</td>
                                        <td><span className={`has-text-${item.attending ? 'success' : 'danger'}`}>{item.attending ? "Yes" : "No"}</span></td>
                                        <td>
                                            <div className="buttons">
                                                <button className="button is-info is-small" onClick={() => dispatch(toggleEventAttendance(item.id))}>change attendance</button>
                                                <button className="button is-danger is-small" onClick={() => dispatch(deleteEventAttendee(item.id))}>delete</button>
                                            </div>
                                        </td>
                                    </tr>
                                ))
                            }
                        </tbody>
                    </table>
                </>
                : <h2>Oh dear...looks like no one has signed up yet :(</h2>
            }
        </div>
    );
};

export default EventSignUpList;
```

### Edit EventSignUpForm.jsx

Now that you’ve seen the changes you need to make, you can make those exact same changes in your `EventSignUpForm` file. Open it up and start with the imports section:

**src/components/EventSignUpForm.jsx**

```
import React, { useState } from 'react';
import { useDispatch } from 'react-redux';

// Actions
import { addEventAttendee } from '../reducers/eventReducer';


// Components
import EventDetails from './EventDetails';
```

Again, you’ve removed the `connect` import and replaced it with the `useDispatch` Hook. You’re not going to use any data from state in this component so you won’t be needing the `useSelector` Hook. 

Next, just as before, scroll to the bottom of the file and replace all of the mapping functions with a single export statement:

**src/components/EventSignUpForm.jsx**

```
export default EventSignUpForm;
```

Back up at the component declaration line, remove the `addEventAttendee` call in the props destructuring.Aadd a new variable, `dispatch`, that calls the `useDispatch` Hook.

**src/components/EventSignUpForm.jsx**

```
const EventSignUpForm = () => {
    const [signUpComplete, setSignUpComplete] = useState(false);
    const [formFields, setFormFields] = useState(_baseFormFields);
    const dispatch = useDispatch();
```

The only part you need to change in the component’s logic functions is in the `handleFormSubmit` event. 

**src/components/EventSignUpForm.jsx**

```
    const handleFormSubmit = evt => {
        evt.preventDefault();

        const newEventAttendee = {
            ...formFields,
            attending: true,
        };

        dispatch(addEventAttendee(newEventAttendee));
        setSignUpComplete(true);    
```

You’ve explicitly created a `newEventAttendee` variable here that copies across the form field values from state, and adds a new property `attending`, setting it to `true`. You then pass this to the `addEventAttendee` just as you did before, but notice how you’re wrapping this all up in the `dispatch()` function again. 

And that’s it. The JSX remains pristine, and the complete component now looks like this:

**src/components/EventSignUpForm.jsx**

```
import React, { useState } from 'react';
import { useDispatch } from 'react-redux';

// Actions
import { addEventAttendee } from '../reducers/eventReducer';


// Components
import EventDetails from './EventDetails';

const _baseFormFields = {
    name: '',
    email: '',
    number: ''
};

const EventSignUpForm = () => {
    const [signUpComplete, setSignUpComplete] = useState(false);
    const [formFields, setFormFields] = useState(_baseFormFields);
    const dispatch = useDispatch();
    
    const handleOnChange = evt => {
        setFormFields({
            ...formFields,
            [evt.target.id]: evt.target.value, 
        });
    };

    const handleFormSubmit = evt => {
        evt.preventDefault();

        const newEventAttendee = {
            ...formFields,
            attending: true,
        };

        dispatch(addEventAttendee(newEventAttendee));
        setSignUpComplete(true);    
    };

    const resetForm = () => {
        setFormFields({..._baseFormFields});
        setSignUpComplete(false);
    };

    return (
        <>
            <EventDetails />            
            <div className="box">
                {
                    !signUpComplete && (
                        <form onSubmit={handleFormSubmit}>
                            <div className="field is-horizontal">
                                <div className="field-label is-normal">
                                    <label className="label">From</label>
                                </div>
                                <div className="field-body">
                                    <div className="field">
                                        <p className="control is-expanded">
                                            <input className="input" type="text" placeholder="Name" id="name" value={formFields.name} onChange={handleOnChange}  />                                
                                        </p>
                                    </div>
                                    <div className="field">
                                        <p className="control is-expanded">
                                            <input className="input" type="email" placeholder="Email" id="email" value={formFields.email} onChange={handleOnChange} />                                
                                        </p>
                                    </div>
                                </div>
                            </div>

                            <div className="field is-horizontal">
                                <div className="field-label"></div>
                                <div className="field-body">
                                    <div className="field is-expanded">
                                        <div className="field has-addons">
                                            <p className="control">
                                                <a className="button is-static" href="#nogo">
                                                    +44
                                                </a>
                                            </p>
                                            <p className="control is-expanded">
                                                <input className="input" type="tel" placeholder="Your phone number" id="number" value={formFields.number} onChange={handleOnChange} />
                                            </p>
                                        </div>
                                        <p className="help">Do not enter the first zero</p>
                                    </div>
                                </div>
                            </div>

                            <div className="field is-horizontal">
                                <div className="field-label"></div>
                                <div className="field-body">
                                    <div className="field">
                                        <div className="control">
                                            <button className="button is-primary">
                                                Sign me up!
                                            </button>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </form>
                    )
                }                
                {
                    signUpComplete && (
                        <>
                            <p className="is-subtitle is-size-4 has-text-primary">
                                Thanks for signing up! We'll see you soon
                                <button className="button is-link is-pulled-right" onClick={resetForm}>Add another</button>
                            </p>
                        </>
                    )
                }                
            </div>
        </>
    );
};

export default EventSignUpForm;
```

## Edit configureStore.js

Next, let’s make a big change to the `configureStore.js` file. Open it up and change the imports as follows:

**src/config/configureStore.js**

```
import { configureStore } from '@reduxjs/toolkit'

// reducers
import rootReducer from '../reducers/reducers';
```

Notice how you’ve removed `initialState` as you no longer need it. You’ve removed all the other imports from the react-redux library, replacing it with importing the `configureStore` function from the `@reduxjs/toolkit`.

Next, replace the current `configureStore` declaration and export, combining them into a new, single export line:

**src/config/configureStore.js**

```
export default configureStore({
    reducer: rootReducer,
});
```

Instead of rolling your own `configureStore` function, you’ll use the imported one which provides much of the same functionality as your own. It configures some enhancers and middleware on your behalf behind the scenes, including the Thunk middleware for asynchronous operations.

All you need to provide to the new `configureStore` is the `reducer` property, passing it a root reducer. 

The new, edited `configureStore.j`s file now looks like this:

**src/config/configureStore.js**

```
import { configureStore } from '@reduxjs/toolkit'

// reducers
import rootReducer from '../reducers/reducers';

export default configureStore({
    reducer: rootReducer,
});
```

### Edit reducers.js

Speaking of root reducers, you need to find the  `reducers.js` file and make a single, solitary change. Open the file and replace the import library with `@reduxjs/toolkit`.  

**src/reducers/reducers.js**

```
import { combineReducers } from '@reduxjs/toolkit';

// Reducers
import events from './eventReducer';
```

And that’s it. That’s the change. The rest of the file remains the same, but the `combineReducers` function provided by the Redux Toolkit helps to manage nesting of reducers (which we’re not doing) and wires things up. 

### Edit index.js

You’re almost there, but let’s edit the `penultimate` file before getting into the most dramatically changed file, `eventReducer.js`. Open the `index.js` file and make two small changes:

**src/index.js**

```
import React from 'react';
import ReactDOM from 'react-dom';

import App from './App';
import reportWebVitals from './reportWebVitals';

// Redux
import { Provider } from 'react-redux';

import store from './config/configureStore';
```

Previously, you had a two-step process going on: you imported `configureStore` and then called it to create a store variable. Now, however, your store is created as an export in the `configureStore.js` file, so all you need to do is import it, and assign it directly to the `Provider` component’s `store={}` attribute. 

Make the change, save the file, and you’re done here.

### Edit eventReducer.js

Your final set of changes will also be the most dramatic. You’re going to hack away at the `eventReducer.js` file, reducing the line count by almost two-thirds!

Open up the file and let’s change those imports:

**src/reducers/eventReducer.js**

```
import { createSlice } from '@reduxjs/toolkit'
import uuid from 'react-uuid';

// Data
import appState from '../data/initialState';
```

You’re bringing in a new function, `createSlice`, from the Redux Toolkit. The `createSlice` function is a powerful little helper that simplifies the process of creating actions, action types, and reducer functions. It auto-generates the action types and action creators, based on the reducer function names you provide it. 

The only other change you’ve made is to rename the imported `initialState` to `appState`, which is purely for a little clarity. One of the properties on the object you’ll need to pass to the `createSlice` function in a moment is already called `initialState`, so renaming your physical application state data here will just give a little distinction between the two. It’s purely optional though.

Next up, you’ll need to remove both the action types and the actions you created. Don’t be shy, just highlight them all and remove them completely.

Now, rather than try to edit the existing `eventReducer` function, create it again from scratch because the differences are too great to make it worth your while. So, highlight the existing reducer function and remove it all. 

With that out of the way, create a new skeleton reducer function and export it:

```
export const eventSlice = createSlice({
    name: 'events',
    initialState: appState.events,
    reducers: {
    },
});
```

You use the `createSlice` method to effectively create a slice of your app’s state, relevant to the area you’re dealing with. It accepts an options object that should contain  `name`, `initialState`, and `reducers` properties. These are described below:

- `name` - give this slice a suitably descriptive name 

- `initialState` - either provide a new portion of state to work with or do like you have here and pass in a section of an existing starter state

- `reducers` - this is an object whose properties will form the name of the reducers you’ll work with to update state - you’re going to edit these in a moment

The `createSlice` function returns a selection of actions and an overall reducer that you’ll export near the end of the file. But first, you need to recreate your three state editing functions, which will replace the previous switch statement you had. 

**src/reducers/eventReducer.js**

```
    reducers: {
        addEventAttendee: (state, action) => {            
            const newId = uuid();
            const newAttendee = action.payload;
            newAttendee.id = newId;            
            state.eventAttendees.push(newAttendee);
            state.loading = false;
        },
```

You’ve added the `addEventAttendee` as a property in the reducer object. It is an arrow function that will be passed `state` and `action` values as arguments. `state` will represent the slice of `state` that you’re working with, and `action` will be an object whose payload property will be whatever you pass to the function. For example, remember in the `EventSignUpList` component where you called `dispatch(toggleEventAttendance(item.id))`? You pass in `item.id`, and it is this value that will be recalled when you access the `action.payload` in your reducer function here.

You’ll see that the body is very similar to that which you had in the `switch` statement. You generate a new ID value and grab the new attendee details from `action.payload`. Then assign the new ID value into the `newAttendee` object. 

However, the big change here is how you’re affecting `state`. Previously, you had to create and return a copy of `state`. This had lots of spread syntax and nesting and it looked cumbersome - it was harder to follow what was going on. 

With Redux Toolkit,  it uses a package called [Immer](https://immerjs.github.io/immer/) in the background to effectively create a draft `state` based on your changes, and then copy this over to the existing `state`. What that means is you can drastically simplify making updates to `state`. Now, you can just write `state` changes directly to the `state` object that each reducer is passed, as you have here with lines like `state.loading = false`;.

Let’s fill out the remaining two reducer functions:

**src/reducers/eventReducer.js**

```
        toggleEventAttendance: (state, action) => {   
            const itemToUpdate = state.eventAttendees.find(item => item.id === action.payload);
            itemToUpdate.attending = !itemToUpdate.attending;
            state.loading = false;
        },
        deleteEventAttendee: (state, action) => {            
            state.loading = false;
            state.eventAttendees = state.eventAttendees.filter(item => item.id !== action.payload);
        },
```

Both `toggleEventAttendanc`e and `deleteEventAttendee` are broadly the same as they were before. With the former, you must find a matching item in `state` whose attending value you can update. With the latter, you filter the existing `eventAttendees`, removing the one with the matching `id` value.

The last thing to do here is to export some important pieces of the puzzle. Previously, when you had explicit action types and action functions, you exported them for use in component files. You still need to do that, but you’ll achieve it in a slightly different way:

**src/reducers/eventReducer.js**

```
export const { addEventAttendee, toggleEventAttendance, deleteEventAttendee } = eventSlice.actions;

export const selectAttendees = state => state.events.eventAttendees;

export default eventSlice.reducer;
```

The `createSlice` function provides a few items on its return, namely some actions and a reducer. 

You can see here that you’ve destructured the actions from `eventSlice.actions`, and they’re named the same as the reducer property names you’ve just been through. After that, at the end of the file, you’re exporting a default of `eventSlice.reducer` which will be consumed in the `reducers.js` file.

The remaining export is `selectAttendees`. You may recall this from whenyou edited the `EventSignUpForm` component. On its own, this arrow function won’t do much, but when it’s combined with the `useSelector` Hook, it will be passed the correct slice of `state` where you can pull whatever you need from it. In your case you’re not doing anything fancy, just an inline return statement that returns us the `eventAttendees` from `state`. You could, however, do something a little more interesting depending on your needs, such as processing some `state` item and then returning that instead.

#### Complete eventReducer.js File

After all of these impressive changes, your complete file now looks like this:

**src/reducers/eventReducer.js**

```
import { createSlice } from '@reduxjs/toolkit'
import uuid from 'react-uuid';

// Data
import appState from '../data/initialState';

export const eventSlice = createSlice({
    name: 'events',
    initialState: appState.events,
    reducers: {
        addEventAttendee: (state, action) => {            
            const newId = uuid();
            const newAttendee = action.payload;
            newAttendee.id = newId;            
            state.eventAttendees.push(newAttendee);
            state.loading = false;
        },
        toggleEventAttendance: (state, action) => {   
            const itemToUpdate = state.eventAttendees.find(item => item.id === action.payload);
            itemToUpdate.attending = !itemToUpdate.attending;
            state.loading = false;
        },
        deleteEventAttendee: (state, action) => {            
            state.loading = false;
            state.eventAttendees = state.eventAttendees.filter(item => item.id !== action.payload);
        },
    },
});

export const { addEventAttendee, toggleEventAttendance, deleteEventAttendee } = eventSlice.actions;

export const selectAttendees = state => state.events.eventAttendees;

export default eventSlice.reducer;
```

### Run the Code

With all of your changes complete, fire up the terminal again and run the familiar `npm start` command. When the app loads in a browser, take a look around, add a couple of attendees to your puppy event, log in and change their status or delete them. 

You should find that nothing has changed and the functionality remains the same. However, the take-away here is that you’ve:

- Achieved another fully-functional redux set-up using Redux Toolkit.

- Reduced hassle and code mess. 

- Kept the same power and flexibility of the traditional redux store set-up.

In the final part of this lesson, you’ll be removing all trace of these external redux libraries in favor of using React’s built-in solution, which is a combination of Context and the `useReducer` Hook.

## useReducer Hook

This final activity eschews all external redux libraries and swaps in the use of React's built-in `useReducer` Hook and Context tools.

In this third and final activity of the lesson, you’re going to go over another redux alternative, this one using no external libraries. Instead, you’ll be using a combination of React’s own `useReducer` Hook and the built-in Context system to pass both `state` and a dispatching function to various components.

You may remember the `useReducer` Hook in your React Hooks Deep Dive lesson, but said you’d be covering it later on. Well that time has come, so let’s get learning!

### Use useReducer and Context

The `useReducer` Hook is provided by React natively without any third-party requirements. It’s quite a simple Hook to use, with its implementation looking like this:

```
const [state, dispatch] = useReducer(reducer, initialArg, init);
```

You call the Hook, passing in a reducer function, initial state value (shown here as initialArg), and an optional third argument, `init`, which would be a function that allows you to lazy load your initial state value. 

What the Hook returns is an array containing the current `state` of the app and a `dispatch` function to trigger `state` updates via actions, just as you have been doing so far.

The `useReducer` Hook focuses on what it does so well, but you still have a few missing pieces of the puzzle. For example, you no longer inherently have any central store mechanism, nor an obvious means to pass access to your app’s `state` or the `dispatch` function to different components. It’s not the end of the world, but it's common practice to create a centralized "store" mechanism just like you’ve seen with the other redux approaches this far, and then pass both the `state` and `dispatch` items across your app via React’s Context system. 

## L09HandsOnProject Update

You have a few changes to make to your project again, starting with removing the React Redux libraries, and working your way through the same files from the last part to switch over to using the `useReducer` Hook.

Before you dive in, I’m just going to give you a heads-up that when you’re done, the resulting code will look a lot more like the first lesson’s code than the much more concise approach offered in the last part using Redux Toolkit.

Personally I feel that the `useReducer` and Context approach is somewhere between the two lessons we’ve covered so far. It is not as concise or smooth as that offered by the Redux Toolkit, but it does offer a much less opinionated approach, and doesn’t depend on any external libraries. 

What’s more, the way you're going to tackle it here is one of a few different ways you could employ the `useReducer` Hook. 

At its simplest, it is a single line function call that is paired with a `switch` statement that updates some object values in a `state` item. 

You’re going to be taking it up a gear in this lesson by creating:

- a central redux store

- a Context provider wrapper 

- and a combined reducer function just like you’ve already seen, to keep things equal between the three lessons. 

### Switch up Dependencies

Just like the last part, start by removing what you don’t need from your project. In this case, you’re going to remove the two packages, `@reduxjs/toolkit` and `react-redux`. 

This time I’m just going to issue the npm command, `npm remove @reduxjs/toolkit react-redux`. 

With that done, start with some of the foundational work and build the changes up from there. 

### Remove configureStore.js

Dead easy one to start with - find the `/config` folder that contains the `configureStore.js` file, and just delete it altogether. You’ll still be creating the idea of a store, but you’ll do the work in your central `reducers.js` file this time. 

This folder and file would be unused dead weight, so get it deleted and let’s move on.

### Edit index.js

You’re beginning your edits with the `index.js` file this time as there’s very little to change and it’s largely all removals.

Open the file and let’s change the imports around redux things:

**src/index.js**

```
import React from 'react';
import ReactDOM from 'react-dom';

import App from './App';
import reportWebVitals from './reportWebVitals';

// Reducer store
import { StoreProvider } from './reducers/reducers';
```

Notice the top imports are the same, but you’ve removed the store you brought in previously from your `configureStore.js` file which no longer exists. 

You’ve also changed the `Provider` import to `StoreProvider`, and changed the import location from `react-redux` to your `reducers.j`s file. You’ll build out this new `StoreProvider` component later, but let’s import it here and use it right now.

With your `StoreProvider` import at the ready, simply use it to directly replace the `<Provider>` component that’s currently wrapping your `<App/>` component.

The changed file should look like this:

**src/index.js**

```
import React from 'react';
import ReactDOM from 'react-dom';

import App from './App';
import reportWebVitals from './reportWebVitals';

// Reducer store
import { StoreProvider } from './reducers/reducers';


ReactDOM.render(
  <React.StrictMode>
    <StoreProvider>
      <App />
    </StoreProvider>
  </React.StrictMode>,
  document.getElementById('root')
);

// If you want to start measuring performance in your app, pass a function
// to log results (for example: reportWebVitals(console.log))
// or send to an analytics endpoint. Learn more: https://bit.ly/CRA-vitals
reportWebVitals();
```

### Edit eventReducer.js

Let’s move onto the `eventReducer.js` file. Open it and familiarize yourself with the current code used alongside the Redux Toolkit. Unfortunately, your new code won’t look quite as neat and tidy as this, but it won’t be as long and unwieldy as in the first lesson. 

Remove everything in this file except for the `uuid` import at the top.

Now, you won’t need to create any action functions this time, but you will be reinstating your action types. Create the simple JavaScript `key:value` object now:

**src/reducers/eventReducer.js**

```
import uuid from 'react-uuid';

// Actions
export const actions = {
    ADD_ATTENDEE: 'add event attendee',
    TOGGLE_ATTENDANCE: 'change attendance',
    DELETE_ATTENDEE: 'delete attendance'
};
```

There you go, pretty much identical to those from the first lesson. Next, create your `eventReducer` variable, complete with `switch` statement to work through the possible action types, and export it from the file as the default export.

**src/reducers/eventReducer.js**

```
// Reducer
const eventReducer = (state, action) => {
    switch (action.type) {
        case actions.ADD_ATTENDEE: {            
            const newAttendee = action.payload;
            newAttendee.id = uuid();
            newAttendee.attending = true;

            return {
                ...state,
                eventAttendees: [
                    ...state.eventAttendees,
                    newAttendee
                ],
                loading: false,                
            };
        }
        case actions.TOGGLE_ATTENDANCE: {            
            const updatedEventAttendees = state.eventAttendees.map(item => {                    
                    if(item.id === action.payload.id) {                        
                        item.attending = action.payload.attending;                        
                    }
                    return item;
                });            

            return {
                ...state,    
                eventAttendees: updatedEventAttendees,
                loading: false,
            }
        }
        case actions.DELETE_ATTENDEE: {            
            const updatedEventAttendees = state.eventAttendees.filter(item => item.id !== action.payload);

            return {
                ...state,
                eventAttendees: updatedEventAttendees,
                loading: false,   
            }
        }
        default:
            return state;
    }
};

export default eventReducer;
```

This should look very familiar as it's virtually identical to the reducer function from earlier. There are a couple of subtle differences here, mainly where you’re referencing the `action.payload` value directly, rather than extracting it via destructuring into separate variables.

That aside, you still have to create a copy of `state` with your updates applied, and return it from your matching cases. Everything else should look about the same though, and it’s a pattern you’ll start to see again and again when you get more adept with redux - this idea of adding items to arrays, changing values in a particular item in an array, and removing items from an existing array. You’ll see heavy and frequent use of both the `map()` and `filter()` functions as they both return a copy of an existing array which is perfect as we don’t want to affect the original `state`.

With all that wrapped up, save and close the completed file which now looks like this:

**src/reducers/eventReducer.js**

```
import uuid from 'react-uuid';

// Actions
export const actions = {
    ADD_ATTENDEE: 'add event attendee',
    TOGGLE_ATTENDANCE: 'change attendance',
    DELETE_ATTENDEE: 'delete attendance'
};

// Reducer
const eventReducer = (state, action) => {
    switch (action.type) {
        case actions.ADD_ATTENDEE: {            
            const newAttendee = action.payload;
            newAttendee.id = uuid();
            newAttendee.attending = true;

            return {
                ...state,
                eventAttendees: [
                    ...state.eventAttendees,
                    newAttendee
                ],
                loading: false,                
            };
        }
        case actions.TOGGLE_ATTENDANCE: {            
            const updatedEventAttendees = state.eventAttendees.map(item => {                    
                    if(item.id === action.payload.id) {                        
                        item.attending = action.payload.attending;                        
                    }
                    return item;
                });            

            return {
                ...state,    
                eventAttendees: updatedEventAttendees,
                loading: false,
            }
        }
        case actions.DELETE_ATTENDEE: {            
            const updatedEventAttendees = state.eventAttendees.filter(item => item.id !== action.payload);

            return {
                ...state,
                eventAttendees: updatedEventAttendees,
                loading: false,   
            }
        }
        default:
            return state;
    }
};

export default eventReducer;
```

## Edit reducers.js

You’re going to move on to arguably the most complex edits now, mainly due to the fact that you’re having to roll your own store and provider offerings. You’ll be introduced to some unexplored concepts here. You didn’t have to do quite as much wiring up in the previous lessons because React Redux helps out a lot and Redux Toolkit offers a big dollop of black magic, doing almost everything for you.

Open up the `reducers.js` file and clear it out completely. Both the previous lessons imported the other reducers into this file and merged them all into a single offering using the `combineReducers` function. You don’t have access to such a function now that we’re on our own, so you’ll have to make one. 

You’re also going to need to create a helper function that will create actions, as well as building a store context and adding both `state` and `dispatch` items to it so that other components can consume them across the app.

Let’s start with some imports. You’re pulling in a lot of things from React, including the new `useReducer` Hook.You’ve got your initial state and your events reducer that you just finished changing.

**src/reducers/reducers.js**

```
import React, { useReducer, useMemo, createContext } from 'react';

// Data
import initialState from '../data/initialState';

// Reducers
import events from './eventReducer';
```



