# Lesson 2: React Core Concepts

You built and refactored a great React app. Now, it's time to talk about the fundamental principles of React components and core concepts.

In this lesson, you're going to blearn a core concept that lives at the heart of React, and that’s how to pass data around between different components.

I meet a lot of students who are both new to coding or new to React and one of the common sticking points is how to deal with data; passing it to other components, and getting other data back into the original, parent component.

## Component Relationships

Due to the modular nature of React development, you will generally be focused on creating small, isolated parts of functionality within your apps. You then build larger features or areas within the app by combining these modules or components together.

Even if one component is not a logical ***parent** of another, that component may reference another to build some area of functionality. The component doing the referencing can be thought of as the **parent** component, and the component being referenced can be thought of as the **child** component.

As an example; a `header` is not naturally related to a shopping basket, but an e-commerce website might have a count of the current items in the shopping basket in the `header` somewhere. Therefore, you can say the `header` component would be the **parent** component, and the shopping basket would be the **child**.

Of course, **parents** can have **parents** themselves and **children** can have **children**. The important thing to remember is this initial relationship as it dictates how data flows within a React app.

## Data Flow within React

Without getting lost in complex topics such as `Redux` (which you will cover in a later lesson), data generally flows in two directions within React:

- Down via props
- Up via events and event handlers

Looki at this visually:

![](assets/props-events.png)

A **parent** component can pass data down to its **child** components via props.

Similarly, **child** components can feed data back up to **parents** via event handlers (functions passed down to them via props).

### Data Handling in Action

Look at a quick example to illustrate this in action:

<details>
<summary>index.js</summary>

```
import React, { useState } from 'react';

const ParentComponent = props => {
  const [items, setItems] = useState(['apple', 'pear', 'orange', 'bear']);

  const clearBasket = () => {
    setItems([]);
  };

  return <ChildComponent basket={items} clickHandler={clearBasket} />;
};

const ChildComponent = props => {
  return (
    <>
      <p>You have {props.basket.length} items in your basket</p>
      <button onClick={props.clickHandler}>Empty basket</button>
    </>
  );
};
```
</details>

In this simple example, you have two components, `ParentComponent` and `ChildComponent`.

In `ParentComponent`, you have an array of strings set into `state` using the `useState` Hook and assigned into the items variable. You then have a `clearBasket()` function that sets the value of our items array to an empty array.

`ParentComponent` returns an instance of the `ChildComponent` component passing both items and `clearBasket()` into it as props.

In `ChildComponent`, you return a paragraph that tells the user of how many items are in their shopping basket by checking the length of the items array it received via props from `ParentComponent` (although in `ChildComponent` this is received as `basket`).

Now, if you want to clear the basket, you can’t do this in `ChildComponent`. You need a way to tell `ParentComponent` you want to empty your `basket`.

The way we pass this request up to trigger the clearing of the `basket` is with a function passed to `ChildComponen`t via props. The function is `clearBasket()` and you pass it into `ChildComponent` via props under the name `clickHandler`.

In `ChildComponent`, attach the passed-in `clickHandler` function to the `onClick` event of your `button` element.

When the user clicks the `button`, the `onClick` event is triggered, which calls the `clickHandler` function from props. The `clickHandler` function (which is really `clearBasket())` is invoked in `ParentComponent` and the `basket` is cleared.

## Data Syncing

Fortunately, React takes care of the syncing of the dynamic data passed between components via props on your behalf; re-rendering the UI as needed.

In your case, when a user clicks the `button` in `ChildComponent`, a function in `ParentComponent` is triggered which clears the items array. The new items array (which is empty) is then passed down into `ChildComponent` again, which re-renders the output, this time displaying the new message, ‘`You have 0 items in your basket`’.

React handles this automatically which is pretty handy and one of the powerful features of the React library.

## React Hooks

This section introduces you to React Hooks; what they are, how to use them, and why they're an important part of modern React development.

Hooks are not the newest addition to the React library. They were introduced in version 16.8 (the current version at the time of writing is 18). However, they have taken a while to be more widely adopted by the community as the standard practice when developing React components.

In React, Hooks allow developers to tap into some of the more powerful features of React, such as `state` and the **component lifecycle**, without writing some of the more cumbersome syntax required in more "traditional" class-based components.

### Why Hooks

Hooks offer several big advantages :

1. They allow us to write smaller, more manageable components.
2. They allow us to reuse stateful logic between components (we’ll discuss this next).
3. They reduce the complexity of dealing with the React component lifecycle.
4. They eliminate the difficulties learning and dealing with JavaScript classes can bring, especially around how this works and binding event handlers.

Let's cover these advantages with  examples.

### Reuse Stateful Logic

‘stateful logic’ refers to a component’s `state`, the data stored by the component, and any associated logic to alter that `state`.

Before Hooks, `state` was quite rigidly bound to a particular component and common or repeated logic wasn’t easily removed into a separate space to be reused across other components.

Hooks solves this problem by allowing you to grab this stateful logic and create your own ‘hook’ to reuse wherever you wish, without changing your component hierarchy.

Let’s look at a quick example.

You want to know the status of a user compared with the amount of authorized roles they had within a system:

<details>

<summary>UserStatus.jsx</summary>

```
const UserStatus = props => {
  const [roles, setRoles] = useState([]);
  useEffect(() => {
    const handleRoleChange = user => {
      setRoles(user.roles);
    };
    UserService.fetchUserRoles(props.userId, handleRoleChange);
    return () => {
      UserService.destroy(handleRoleChange);
    };
  });

  return roles.length > 0 ? 'enabled' : 'disabled';
};
```
</details>

In another component, you want to see a list of that user’s roles, perhaps for an account page. That could look like this:

<details>
<summary>RolesList.jsx</summary>

```
const RolesList = props => {
  const [roles, setRoles] = useState([]);
  useEffect(() => {
    const handleRoleChange = user => {
      setRoles(user.roles);
    };
    UserService.fetchUserRoles(props.userId, handleRoleChange);
    return () => {
      UserService.destroy(handleRoleChange);
    };
  });

  return (
    <>
      {roles.map(role => {
        <li style={{ color: role.isAdmin ? 'red' : 'green' }} key={role.id}>
          {role.name}
        </li>;
      })}
    </>
  );
};
```
</details>

You have much shared logic here. You can use a custom Hook to abstract this into a shared `state` logic that deals with the `roles` list.

<details>
<summary>useRolesForUser.jsx</summary>

```
const useRolesForUser = userId => {
  const [roles, setRoles] = useState([]);
  useEffect(() => {
    const handleRoleChange = user => {
      setRoles(user.roles);
    };
    UserService.fetchUserRoles(props.userId, handleRoleChange);
    return () => {
      UserService.destroy(handleRoleChange);
    };
  });

  return roles;
};
```
</details>

You have your custom Hook. Now you can update your examples above to use it!

<details>
<summary>UserStatus.jsx</summary>

```
const UserStatus = props => {
  const roles = useRolesForUser(props.userId);
  return roles.length > 0 ? 'enabled' : 'disabled';
};
```
</details>

<details>
<summary>RolesList.jsx</summary>

```
const RolesList = props => {
  const roles = useRolesForUser(props.userId);
  return (
    <>
      {roles.map(role => {
        <li style={{ color: role.isAdmin ? 'red' : 'green' }} key={role.id}>
          {role.name}
        </li>;
      })}
    </>
  );
};
```
</details>

### Reduce Complexity

Hooks can reduce the complexity of your components when compared to the previous class-based syntax you may have seen in the past.

<details>
<summary>MyClassComponent.jsx</summary>

```
import React from 'react';

// data handler
import DataHandler from './data-handler';

// components
import UserCard from './UserCard';

class MyClassComponent extends React.Component {
  constructor(props) {
    super(props);

    this.state = {
      users: []
    };
  }

  componentDidMount() {
    if (!DataHandler.initialised) {
      DataHandler.init(this.props.usersToLoad);
    }
    DataHandler.loadUsers().then(data => {
      this.setState({
        users: data
      });
    });
  }

  componentDidUpdate(prevProps) {
    if (prevProps.usersToLoad === this.props.usersToLoad) {
      return;
    }

    DataHandler.loadUsers().then(data => {
      this.setState({
        users: data
      });
    });
  }

  componentWillUnmount() {
    // clear any subscriptions and teardown data handler
    DataHandler.exit();
  }

  render() {
    return (
      <>
        <h2>Loading {this.props.usersToLoad} users</h2>
        {this.state.users.map(user => (
          <UserCard {...user} />
        ))}
      </>
    );
  }
}
export default MyClassComponent;
```
</details>

While not a very complex component in terms of what it does, it’s already becoming quite long and will prove more difficult to manage over time if it grows. You have data loading happening in several places across different parts of this `component`’s lifecycle, as well as having extra `state` management work in a constructor function, not to mention the liberal use of this to reference `state` and props values.

Refactor it to use Hooks (similar to how you did it in the previous module):


<details>
<summary>MyClassComponent.jsx</summary>

```
import React, { useState, useEffect } from 'react';

// data handler
import DataHandler from './data-handler';

// components
import UserCard from './UserCard';

const MyHooksComponent = ({ usersToLoad }) => {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    if (!DataHandler.initialised) {
      DataHandler.init(usersToLoad);
    }

    DataHandler.loadUsers().then(data => setUsers(data));

    return () => {
      DataHandler.exit();
    };
  }, [usersToLoad]);

  return (
    <>
      <h2>Loading {usersToLoad} users</h2>
      {users.map(user => (
        <UserCard {...user} />
      ))}
    </>
  );
};
export default MyHooksComponent;
```
</details>

Look at how much smaller and more readable you made your component! You also reduced much of the complexity and overhead involved in managing several lifecycle methods.

#### Are Hooks Mandatory

That’s one of the positives of Hooks: they’re 100% completely opt-in.

You can also use them alongside class-based components. You might encounter a mixture of class-based components and Hooks-based ones in a real world project. This is because Hooks, much like React itself, offers a very adoptive experience. You can use them as much as you need, and gradually introduce them into your projects.

That said, I would recommend you get familiar with using them and employ them as your exclusive tool for building components in new projects. They are widely adopted by the React community today and there are countless articles and helpful advice out there on how to build your own Hooks and use Hooks in general.

You’ll be exclusively using Hooks to power your components throughout this course, so you’ll have plenty of experience with them as you make progress through the lessons.

For additional information, read this [documentation on Hooks(https://reactjs.org/docs/hooks-intro.html). 

## Common Hooks 

In this section you take a look at the most common Hooks you'll come across and learn how to use them in your apps.

## :pushpin: L02 Hands On Project: Common Hooks

Welcome to the Common Hooks hands-on practice assignment.

Arguably the most common Hooks you’ll encounter are:

- `useState` - used to interact with a component’s `state`.

- and `useEffect` -  best thought of as replacing the React lifecycle events such as `componentDidMount` and `componentDidUpdate`.

Before you move on to your big project in this lesson, take some time to familiarize yourself with the use of these two most common built-in Hooks.

## Requirements

1. Read all the guided learning text carefully for understanding.
2. Follow all instructions and coding step-by-step.
3. Create all files and folders for the Greeting App.
4. Complete all coding as demonstrated in all files.
5. Zip `L02ReactHandsOnPProject1` Folder
6. Attach the zipped folder below where indicated for submission.

## Project Setup

You get an introduction to 'Create React App' in the next section as a great way to build new React projects. For now, keep things simple and familiar by using 'Parcel JS' to set up your project to bundle and run your React code for these examples.

### Configure Parcel JS

1. Create a new folder in your `FEFReact`.

:writing_hand: 

- `mkdir FEFReact/L02HandsOnProject1`

 2. and open it in VS Code. Navigate to the terminal and let’s initialize our new project:

:writing_hand: 

 - `cd L02HandsOnPractice1`

 - `npm init -y`

3. Install the packages you need:

:writing_hand: 

 - `npm add react react-dom`

4. Finally, open your `package.json` file and add the following config so you can call `npm start` to run the project:

:writing_hand: 

<details>
<summary>package.json</summary>

```
"scripts": {
    "start": "parcel index.html"
  },
```
</details>

### Create the Files

Your project is set up and ready to run. Add the necessary files to your project before you code them out.

:writing_hand:

- `App.js`
- `index.js`
- `index.html`
- `styles.css`
- `UseEffectExample.jsx`
- `UseStateExample.jsx`

`UseStateExample.jsx` and `UseEffectExample.jsx` are where you do the main body of your work, so get your other files set up before you explore your example components.

This will be the entry point for Parcel to start loading your JavaScript from. Import your main `App` component here and call React DOM’s `render()` method to mount it into the `HTML` element in your `index.html` file.

:writing_hand:

<details>
<summary>index.js</summary>

```
import React from 'react';
import ReactDOM from 'react-dom/client';

import App from './App';

ReactDOM.createRoot (document.getElementById('root')).render(<App />);
```
</details>

### App.js

In your `App` component, the main starting point for your React app, use a Hook, the `useState` Hook.

Look at the file in full:

:writing_hand:

<details>
<summary>App.js</summary>

```
import React, { useState } from 'react';

import './styles.css';

// components
import UseStateExample from './UseStateExample';
import UseEffectExample from './UseEffectExample';

export default props => {
  const [example, setExample] = useState('UseStateExample');

  return (
    <>
      <h1>Choose an example</h1>
      <button onClick={() => setExample('UseStateExample')}>
        useState Example
      </button>
      <button onClick={() => setExample('UseEffectExample')}>
        useEffect Example
      </button>
      <div className='container'>
        {example === 'UseStateExample' ? (
          <UseStateExample />
        ) : (
          <UseEffectExample />
        )}
      </div>
    </>
  );
};
```
</details>

You imported your two example components, `UseStateExample` and `UseEffectExample` and use the example value in `state` to determine which one to show.

If example is set to the string ‘`UseStateExample`' then you show the `UseStateExample.jsx `component, otherwise, show the `UseEffectExample.jsx` component.

You also have two buttons that both call the `setExample()` method to update the example `state` value, which will, in turn, change which child component is displayed.

### styles.css

Many of the styles here are the same basic styles from your first example built in Lesson 1, the Greeting App. You add a couple extra layout selectors at the bottom to add some flex box support and additional styling to some form elements, such as a `select` and `label`.

:writing_hand:

<details>
<summary>styles.css</summary>

```
body {
  font-size: 16px;
  line-height: 1.4;
  font-family: 'Lucida Sans', 'Lucida Sans Regular', 'Lucida Grande',
    'Lucida Sans Unicode', Geneva, Verdana, sans-serif;
  margin: 5em;
}

p {
  margin-bottom: 1.6em;
}

input,
select {
  padding: 0.5em 1em;
  line-height: 1;
  font-size: 18px;
  border: 1px solid #ececec;
}

button {
  border: 1px solid #ececec;
  background: #ececec;
  padding: 0.5em 1em;
  cursor: pointer;
  font-size: 18px;
  margin-right: 15px;
}

button:hover {
  background: #c9c6c6;
}

label {
  display: block;
  margin-bottom: 0.5em;
}

label + input,
label + select {
  margin-bottom: 1em;
}

form button {
  display: block;
}

.container,
.dogs-container {
  padding: 0 2em 2em;
  border: 1px solid #ececec;
  border-radius: 4px;
  margin: 1em auto;
}

.form-message {
  background-color: #ececec;
  border-radius: 4px;
  margin: 1em auto;
  padding: 2em;
  font-size: 18px;
}

.dogs-container {
  display: flex;
  align-items: center;
  justify-content: space-around;
}

.dogs-container img {
  max-width: 250px;
  max-height: 200px;
}
```
</details>

### index.html

Your `index.html` file simply provides a basic entry point for Parcel to follow and find your JavaScript starting point.

It looks like this:

:writing_hand:

<details>
<summary>index.html</summary>

```
<html>
  <head>
    <title>Hooks example demo</title>
  </head>
  <body>
    <div id="root"></div>
    <script src="index.js"></script>
  </body>
</html>
```
</details>

With the basic files set up and ready, take a closer look at the `useState` and `useEffect` Hooks.

## The useState Hook

`useState` is a direct replacement for the class-based `state` you see used in this manner:

```
// updating state
this.setState({
  someProperty: 'some value'
});

// using state
<p>The value is {this.state.someProperty}</p>;
```

As you already saw in the previous lesson, the `useState` Hook has a very simple syntax for setting and retrieving values from.

```
// initial declaration
const [nameOfValue, setNameOfValue] = useState('initial value here');

// updating state
setNameOfValue('a new value');

// using state
<p>The value in state is {nameOfValue}</p>;
```

You’re not limited to what you can store inside this `state` Hook, just like you’re not limited with `state` used in a class component. Any valid JavaScript primitive value, array, object, and even function can be passed into and stored in `state`.

The convention is to have distinct declarations for multiple values, unless they’re logically able to be kept together, such as with form values.

```
// multiple values
const [itemCount, setItemCount] = useState(0);
const [data, setData] = useState([]);
const [balance, setBalance] = useState(null);

// grouped values
const [formValues, setFormValues] = useState({
  name: '',
  email: '',
  message: ''
});
```

In the first group, you see  singular values such as an integer, '0', an empty array, and a null value. These are all distinct pieces of `state` and declared in their own variable pairs, each using a separate call to `useState`. However, for the `formValues` variable, you're calling `useState` and passing in an object with multiple properties, one for `name`, `email`, and `message`, that each represents a form field value.

You could have separate these values out into their own `useState` call like this:

```
const [name, setName] = useState('');
const [email, setEmail] = useState('');
const [message, setMessage] = useState('');
```

This is fine and you see this approach quite frequently. As with many things in development, there are personal preferences and trade-offs on how you tackle a problem and implement a solution. For me, it makes sense to group closely related slices of data, such a collection of form field values, into a single place (i.e. the `formValues` variable above), but it does add a little more overhead in dealing with updates to any particular form field's value. Conversely, if you have a really large form with many field values, breaking each one into a separate call to `useState` could potentially create much more code for you to manage and a very large component.

With the explanation out of the way, build a little example app to demonstrate how to use `useState` and the values it can be used with.

First: open the `UseStateExample.jsx` file, where you’ll import React and create the empty component’s skeleton:

:writing_hand:

<details>
<summary>UseStateExample.jsx</summary>

```
import React, { useState } from 'react';

const UseStateExample = () => {
  return <></>;
};

export default UseStateExample;
```
</details>

Look at how to effectively use the `useState` Hook. You’re going to build an `HTML` form that will update values in your component’s `state`, and display a message to your user once they’ve submitted the form.

To do that, you need a few things:

- An object in `state` to track `state` values

- A value in `state` to determine if you should show the message (i.e. has the form been submitted?)

- Methods to handle changes in the form elements and form submission

- An HTML form

Your component’s looking a little bare at the moment, so let’s code it out.

### Add in State

The first thing is add your `state` values and items, right at the top of the component.

:writing_hand:

<details>
<summary>UseStateExample.jsx</summary>

```
  const [showMessage, setShowMessage] = useState(false);
  const [formValues, setFormValues] = useState({
    name: '',
    age: '',
    fruit: ''
  });
```
</details>

You really can store just about anything you want in `state`. Your first `state` variable, `showMessage` is going to hold a boolean value, while `formValues` holds an object with some default key-value pairs where you track the input from your user.

#### Tip!

You don’t have to add the keys in like done here, but it’s good practice. It let's you get an idea of what data your form will capture and what the expected type of that data will be, without having to run anything.

### Event Handling Methods

 Map out your `state` updating methods and event handlers: one for a change of any form field values, and one to handle the form submission.

:writing_hand:

<details>
<summary>UseStateExample.jsx</summary>

 ```
   const handleChange = evt => {
    const updatedFormValues = {
      ...formValues,
      [evt.target.id]: evt.target.value
    };

    setFormValues(updatedFormValues);
  };

  const handleSubmit = evt => {
    evt.preventDefault();
    setShowMessage(true);
  };
```
</details>


### UseStateExample.jsx

  In the `handleChange` event, you’re still using the `evt.target.value` to retrieve the current value from the underlying `HTML` element (e.g. a text input field), but you’re creating a new `updatedFormValues` object first. You're using the spread syntax (the three dots you can see before `formValues`) which will essentially copy everything from the current `formValues` object in `state` into your `updatedFormValues` object.

:writing_hand:

<details>
<summary>UseStateExample.jsx</summary>

  ```
    const handleChange = evt => {
    const updatedFormValues = {
      ...formValues,
      [evt.target.id]: evt.target.value
    };
```
</details>

Immediately after this, use the bracketed notation [evt.target.id] to reference a property dynamically on the `updatedFormValues` object, specifically referencing using the form field's `id`. If it exists on the object then the value we assign to it (e.g. `evt.target.value`) will be updated. If it doesn't exist, then a new property will be created and assigned the value of `evt.target.value`.	

Finally, update your form field values object in `state` by calling `setFormValues()`.

Another thing to point out is your use of `evt.preventDefault()` in the `handleSubmit` method. The event React passes us is synthetic, the form submission caused by the button you add at the moment is very real. By calling the `preventDefault()` method when the form submits, you stop the form from triggering a complete page reload. If that happens, you lose your `state` values and your app will produce unintended results.

**UseStateExample.jsx**

Other than that, the `handleSubmit` method is simple. You change the value of `showMessage` in `state` to ‘`true`’ which will be used later in your returned JSX.

### HTML (JSX)

The final piece of the puzzle is to add JSX in the return method so you can render something useful to the user.

First, a little message introducing the app to the user:

:writing_hand:

<details>
<summary>UseStateExample.jsx</summary>

```
  return (
    <>
      <p>Complete the form below and see a nice message about yourself!</p>
```
</details>

Next, add `HTML` form:

:writing_hand:

<details>
<summary>UseStateExample.jsx</summary>

```
      <form onSubmit={handleSubmit}>
        <label htmlFor='name'>What should we call you?</label>
        <input id='name' value={formValues['name']} onChange={handleChange} />

        <label htmlFor='age'>How old are you?</label>
        <input id='age' value={formValues['age']} onChange={handleChange} />

        <label htmlFor='fruit'>What's your favorite fruit?</label>
        <select id='fruit' value={formValues['fruit']} onChange={handleChange}>
          <option>Bananas</option>
          <option>Apples</option>
          <option>Oranges</option>
          <option>Strawberries</option>
          <option>Pears</option>
          <option>Dragon fruit</option>
        </select>

        <button>Submit</button>
      </form>
```
</details>

There are a couple of things to highlight:

- Notice on your labels you use `htmlFor=` instead of the HTML `for=`. This is because you’re still in JavaScript land and `for` is a protected keyword. You can use it, but React will complain, giving warnings in the browser and any tools like ESLint will also notify you of this. Stick to `htmlFor=` when using this attribute on labels in React.
- Each of your HTML elements (inputs and select) are setting the `value=` attribute using a named key from your `formValues` state object. They also each reference the same change event handler, `handleChange` you defined previously.
- Finally, there’s no event handler attached to the button within your form. This is because this button will trigger a form submission event by default when it’s clicked. So all you need to do is attach your `handleSubmit` event handler to the form itself.

Once you collect your user data and submit it, you need to display a message.


:writing_hand:

<details>
<summary>UseStateExample.jsx</summary>

```
      {showMessage && (
        <div className='form-message'>
          <p>
            Hi <strong>{formValues['name']}</strong>, what a lovely name. And I
            can't believe you're only <strong>{formValues['age']}</strong> years
            old, that's no age!
          </p>
          <p>
            Your favorite fruit can't be <strong>{formValues['fruit']}</strong>
            , mine too!
          </p>
        </div>
      )}
```
</details>

You have a couple of simple paragraphs where you reference the values from `state`. You also wrap this entire block in a simplified logic expression:

:writing_hand:

**UseStateExample.jsx**

```
{ showMessage && (...rest of expression)}
```

JavaScript evaluates the left side of the expression, `showMessage` and if that’s ‘`false`’, doesn’t look at the right side of the expression. However, once `showMessage` is ‘`true`’ it will evaluate the right-hand side, which will essentially render your JSX block.

### The Complete UseStateExample.jsx file

The finished file looks like this:

:writing_hand:

<details>
<summary>UseStateExample.jsx</summary>

```
import React, { useState } from 'react';

const UseStateExample = () => {
  const [showMessage, setShowMessage] = useState(false);
  const [formValues, setFormValues] = useState({
    name: '',
    age: '',
    fruit: ''
  });

  const handleChange = evt => {
    const updatedFormValues = {
      ...formValues,
      [evt.target.id]: evt.target.value
    };

    setFormValues(updatedFormValues);
  };

  const handleSubmit = evt => {
    evt.preventDefault();
    setShowMessage(true);
  };

  return (
    <>
      <p>Complete the form below and see a nice message about yourself!</p>
      <form onSubmit={handleSubmit}>
        <label htmlFor='name'>What should we call you?</label>
        <input id='name' value={formValues['name']} onChange={handleChange} />

        <label htmlFor='age'>How old are you?</label>
        <input id='age' value={formValues['age']} onChange={handleChange} />

        <label htmlFor='fruit'>What's your favorite fruit?</label>
        <select id='fruit' value={formValues['fruit']} onChange={handleChange}>
          <option>Bananas</option>
          <option>Apples</option>
          <option>Oranges</option>
          <option>Strawberries</option>
          <option>Pears</option>
          <option>Dragon fruit</option>
        </select>

        <button>Submit</button>
      </form>
      {showMessage && (
        <div className='form-message'>
          <p>
            Hi <strong>{formValues['name']}</strong>, what a lovely name. And I
            can't believe you're only <strong>{formValues['age']}</strong> years
            old, that's no age!
          </p>
          <p>
            Your favorite fruit can't be <strong>{formValues['fruit']}</strong>
            , mine too!
          </p>
        </div>
      )}
    </>
  );
};

export default UseStateExample;
```
</details>

## The useEffect Hook

The `useEffect` Hook is a bit more complex to understand than its `useState` cousin.

React has several lifecycle methods triggered during specific parts of a component's journey from being mounted, to when it updates, and finally, when it unmounts.

Before Hooks, developers would have to tie updates into these lifecycle methods; tasks such as fetching data, handling updates to props’ values, and cleaning up data handling or subscriptions to APIs. As you saw in lesson 1, this leads to messy and very complex components.

`useEffect` gives you a combined method that runs on each render of a component. It can be used multiple times to separate any distinct logic, and it can also be passed an array of dependencies React will monitor for changes. Should any of those dependencies change, only then would that particular `useEffect` call be executed.

Additionally, some work requires a clean up phase (e.g. subscriptions to external data sources). For these tasks, return a function as part of your `useEffect` call to carry out any clean up code you need. Think of this as replacing the `componentWillUnmount` lifecycle event.

Look at the syntax closer to get an idea of how it works:

<details>
<summary>MyComponent.jsx</Summary>

```
const MyComponent = props => {
  // a simple useEffect declaration
  useEffect(() => {
    // log some data on each render
    cleverDataLoggingService.log('ooo what an exciting log entry');
  });

  // cleaning up on unmount
  useEffect(() => {
    // on mount and rerender
    // subscribe to an external API
    productAPI.subcribeToProductList(props.customer.id);

    return () => {
      // on render and unmount
      productAPI.unsubscribeFromProductList(props.customer.id);
    };
  });

  // using dependencies
  useEffect(() => {
    // this will only run when 'props.customer.id' changes
    console.log(`logged in customer is: ${props.customer.id}`);
  }, [props.customer.id]);
};
```
</details>

Now that you know a little more about the `useEffect` Hook, build your component.

- Open the file `UseEffectExample.jsx` and get the basic component scaffolding in as well as your React imports:

:writing_hand:

<details>
<summary>UseEffectExample.jsx</summary>

```
import React, { useState, useEffect } from 'react';

const UseEffectExample = () => {
  return <></>;
};

export default UseEffectExample;
```
</details>      
                             s
In this example, you  build a simple gallery that loads everybody’s favorite: dog pictures! For this, you will use the fun [Dog CEO API](https://dog.ceo/dog-api/).

![](assets/dog-api-screenshot.png)

For the component, you need to accomplish a few things:

- Set your API URL for loading in the pictures

- Add two variables in `state`; one for holding the image URLs of your dogs, and a loading flag you can use to show a ‘…loading’ message

- Create a function to actually load your dog pictures

- Two `useEffect` calls; one for an initial load and one for subsequent loads when the user presses a button

- A button for the user to trigger a new set of dog pictures

- The gallery itself

Now let's get building!

### Create the State Variables

The first task is define some variables, namely your fixed API URL and flexible state variables:

:writing_hand:

<details>
<summary>UseEffectExampl.jsx</summary>

```
const UseEffectExample = () => {
  const apiBaseUrl = 'https://dog.ceo/api/breeds/image/random/';
  const [dogImageUrls, setDogImageUrls] = useState([]);
  const [loadPictures, setLoadPictures] = useState(false);
```
</details>

The Dog CEO API address will return a single image of a dog (that’s no good!), so append a random number onto this later on so we get a nice amount of dog images back.

With state variables, `dogImageUrls` is set to an empty array to begin with, but it will hold a list of picture URLs once the API has been called. With `loadPictures`, set it to ‘`false`’ for now, but you can use this to toggle whether the app is in the middle of loading data or not.

## Create the Image Fetching Function

Call the API to get the pictures in the first place. You define that next:

:writing_hand:

<details>
<summary>UseEffectExample.jsx</summary>

```
  const loadDogPictures = () => {
    const numToLoad = Math.random() * 10;
    fetch(`${apiBaseUrl}${numToLoad}`)
      .then(response => response.json())
      .then(data => setDogImageUrls(data.message));
  };
  ```
</details>

  Use the `Math.random()` function to generate a random number between 1-10. Next, call the Dog CEO API, appending this random number on the end to return that number of doggo pics.

  Use the native browser `fetch()` method here. In a realistic setting you may need to consider browser support and might need to look at using a polyfill or external utility like the Axios project.

  The process is straightforward: `fetch()` calls the API, massage the raw response into `JSON`, then consume that `JSON` as the object data, extracting a message object (as defined by the Dog CEO API docs), which just happens to be an array of image URLs. Set this array in `state` using `setDogImageUrls()` method returned from the `useState` Hook.


### Create the useEffect Calls

 Use the `useEffect` Hook in two separate calls here: one that will be used on the initial mount of the component, and the other will trigger a new load of pictures when the state value `loadPictures` changes.

:writing_hand:

<details>
<summary>UseEffectExample.jsx</summary>
 ```
   useEffect(() => {
    loadDogPictures();
  }, []);
```
</details>

You use a bit of a React trickiness here. By passing to this effect an empty dependency array, you effectively tell React to execute the effect, but only once, on the first mount.

You do that, because you want to have some dog pictures available when the component first mounts, but not every time anything happens, such as the component updates.

You do, however, want to trigger a new fetch when the user clicks the ‘`load more pictures!`’ button. When they do that, the value in `state` for `loadPictures` will change. You can have React watch this value and execute our second effect only when it changes. 

:writing_hand:

<details>
<summary>UseEffectExample.jsx</summary>
```
  useEffect(() => {
    if (loadPictures) {
      loadDogPictures();
      setLoadPictures(false);
    }
  }, [loadPictures]);
  ```
</details>

You supply `loadPictures` as a dependency on the `useEffect` Hook. When it changes, React will execute whatever you supply to the function. In this case, check to make sure it’s set to ‘`true`’, calling the API again before setting the value back to ‘`false``’, which will enable the button to work again.

You may be asking ‘won’t this just trigger this effect to be called again, even when `loadPictures` is set back to ‘`false`’?!’. The answer is ‘yes’, but by checking if it’s set to ‘`true`’ at the start of the function, you avoid some horrible infinite loop scenario.

### Add the Button and Gallery JSX

With all your logic in place, process the images and display an attractive gallery of sweet dogs to your users.
 
:writing_hand:

<details>
<summary>UseEffectExample.jsx</summary>

```
  return (
    <>
      <h2>Random dog pictures</h2>
      <button onClick={() => setLoadPictures(true)}>
        Load some more pictures!
      </button>
      <p>
        {dogImageUrls.length <= 0
          ? '...loading'
          : `We've got ${dogImageUrls.length} pictures for you:`}
      </p>
      <div className='dogs-container'>
        {dogImageUrls.map(imgUrl => (
          <img
            key={imgUrl}
            src={imgUrl}
            alt='a great picture of a random dog'
          />
        ))}
      </div>
    </>
  );
  ```
  </details>

Starting with a title, add a button that simply sets the `state` to ‘`true`’. As you discovered when you mapped out the `useEffect` calls, because React is watching this value as a dependency, it will automatically trigger a fetch of some new dog pictures.

Under this, display a message to the user based on the value of `loadPictures`: ‘…loading’ if set to ’`true`’; or the number dog pictures the user can be expected to see if ‘`false`’.

Now the part you have been waiting for; the pictures themselves!

Use the `.map()` function built into JavaScript,  for arrays to step through your array of dog pictures outputting an image tag.

#### Note!

You added an extra attribute you might not be familiar with right away. The `key=` attribute is another React-specific attribute needed when rendering lists of things. It helps React keep track of changes within the list, such as if any have been added, removed, etc. Supply a unique identifier to each item in a list to give the elements a stable identity.

The nice thing about the `.map()` function is if we have an empty array (like when we initialize it to `[]` at the beginning of the component), nothing is output to the user.

The last thing to do is fire up your app using `npm start` in the terminal, and enjoy some wholesome dog pictures: what the Internet was made for.

### The Complete UseEffectExample.jsx Component

Here's what the complete file will look like:

:writing_hand:

<details>
<summary>UseEffectExample.jsx</summary>

```
import React, { useState, useEffect } from 'react';

const UseEffectExample = () => {
  const apiBaseUrl = 'https://dog.ceo/api/breeds/image/random/';
  const [dogImageUrls, setDogImageUrls] = useState([]);
  const [loadPictures, setLoadPictures] = useState(false);

  const loadDogPictures = () => {
    const numToLoad = Math.random() * 10;
    fetch(`${apiBaseUrl}${numToLoad}`)
      .then(response => response.json())
      .then(data => setDogImageUrls(data.message));
  };

  useEffect(() => {
    if (loadPictures) {
      loadDogPictures();
      setLoadPictures(false);
    }
  }, [loadPictures]);

  useEffect(() => {
    loadDogPictures();
  }, []);

  return (
    <>
      <h2>Random dog pictures</h2>
      <button onClick={() => setLoadPictures(true)}>
        Load some more pictures!
      </button>
      <p>
        {dogImageUrls.length <= 0
          ? '...loading'
          : `We've got ${dogImageUrls.length} pictures for you:`}
      </p>
      <div className='dogs-container'>
        {dogImageUrls.map(imgUrl => (
          <img
            key={imgUrl}
            src={imgUrl}
            alt='a great picture of a random dog'
          />
        ))}
      </div>
    </>
  );
};

export default UseEffectExample;
```
</details>

#### Submission

1. Zip Project Folder
2. Upload zipped folder

## Furry Friend Gallery App

Next you build on the previous Hooks above, by creating a dog picture gallery. First though, take a look at what you're going to build.

You will build on the previous example, a dog picture gallery, but this time with a few enhancements.

Take a look at the app in action.

![](assets/furry-friends-gallery.png)

You have a much improved UI thanks to the Bulma CSS framework. You accept a number of dogs to search for from the user, display the dog pictures in a flexbox grid, and keep track of how many dog pictures have been searched for to date.

This will also be your first journey into development using the **Create React App** starter project, provided by Facebook itself.

You’re not building anything super complex just yet. There are a few moving parts to consider and some judicious use of both the `useState` and `useEffect` Hooks.


## :pushpin: L02HandsOnProject2: Build the Furry Friends Gallery

This code-along assignment walks through using **Create React Ap**p to build a fully-functioning picture gallery; the Furry Friend Gallery.

### Requirements

1. Read all the guided learning text carefully for understanding.
2. Follow all instructions and coding step-by-step.
3. Create all files and folders for the Greeting App.
4. Complete all coding as demonstrated in all files.
5. Zip `L02ReactHandsOnPProject2` Folder
6. Attach the zipped folder below where indicated for submission.

### Introduction

As you experienced in the quick code along of the Furry Friend Gallery, you will build an app that talks to your familiar Dog CEO API, loads in a few starter pictures before turning control over to the user, allowing them to choose several images they’d like to fetch.

Start by creating a new React project using [Create React App](https://create-react-app.dev/).

### Create React App (CRA) Starter Project

Until now, you used [Parcel JS](https://parceljs.org/) to set up and configure your React apps. It makes sense for smaller projects or apps and there’s certainly no problem with continuing to choose Parcel for more complex needs.

However, out in the real world commercial environments, you often come across React projects with much more complexity around their configuration; from employing static code analysis using a tool like [ESLint](https://eslint.org/) or [Babel JS](https://babeljs.io/) to allow you to use the latest JavaScript features cross-browser, and code bundling and optimization with help from [Webpack](https://webpack.js.org/).

**Create React App** is an official React starter project, developed and maintained by the Facebook Open Source team and it offers all of the above (and more) in one, convenient package with just one dependency.

### CRA Advantages

Behind the scenes, the **Create React App** maintains an up-to-date and working configuration that employs modern coding best practices for setting up and working with a React project. It abstracts all of the complex setup required for Webpack to bundle your project’s files, as well as giving you a list of great code linting rules to ensure you’re adhering to generally accepted ‘good code’ standards.

The best part; it can be used to spin up a new project and get coding in under five minutes from just a single terminal command.

### CRA Disadvantages

The biggest drawback to using **Create React App** is also one of its biggest advantages: the abstraction.

Many developers and projects demand more flexibility and control from their configuration. By using **Create React App**, you’re effectively handing over control of these parts of your app to the React team and their own development opinions.

It’s not necessarily a bad thing. There are workarounds to add in your own configurations without using the built in ‘eject’ command (CRA offers this as a last resort, should you wish to unbundle all the configurations and remove the dependency on the CRA itself), but they’re not always ideal.

For more information about this and a great guide on how to set up your own React project without **Create React App**, there is a [great article on Dev.to from Nikhil Kumaran](https://dev.to/nikhilkumaran/don-t-use-create-react-app-how-you-can-set-up-your-own-reactjs-boilerplate-43l0) you can review.

## Furry Friends Gallery Setup

With that out of the way, get started with your new gallery app by navigating to your `FEFReact` folder on your local machine in the terminal.

:writing_hand:

1. `cd desktop`

2. `cd FEFReact`

3. `mkdir lesson02-assignment-1`

4. `cd lesson02-assignment-1`


From here, use the following command to create a brand new React project using the **Create React App** starter.

:writing_hand:

`npm create react-app furry-friends-gallery`

Your terminal or command window will start processing and pulling in all the resources it needs to create a new project. This takes a while, so just wait until it finishes.

When it’s done, you have a success message in the terminal window featuring a list of commands.

![](assets/cra-terminal.png)

### Test the New Project

Before you do anything else, it’s a good idea to jump straight into the new project, launch it, and make sure it’s all working.

So, follow the advice in your terminal output and enter the following commands:

#### KNOWLEDGE CHECK

| What is the correct command to create a new React project? |
|------------------------------------------------------------|
| npm create-react-app                                       |
| npx create-react-app myReactApp                            |
| npx create-react-app                                       |
| npm create-react-app myReactApp                            |

<details>
<summary>Solution</summary>

`npx create-react-app myReactApp`

</details>

| What does myReactApp refer to in the following command? npx create-react-app myReactApp |
|-----------------------------------------------------------------------------------------|
| The directory to create the new app in                                                  |
| A reference to an existing app                                                          |
| The type of app to create                                                               |
| The name you want to use for the new app                                                |

<details>

<summary>Solution</summary>

 The name you want to use for the new app   
 
                                          |

</details>

:writing_hand:

1. `cd furry-friends-gallery`
2. `npm start`

After a few moments, you should see a webpage open up with a spinning React logo and a simple message:

![](assets/app-start.png)

**Boom!!** Everything’s looking great and your new project is almost ready to go!

#### KNOWLEDGE CHECK

| What command is used to start the React local development server? |
|-------------------------------------------------------------------|
| npm build                                                         |
| npm run dev                                                       |
| npm serve                                                         |
| npm start                                                         |

<details>
<summary>Solution</summary>

`npm start`

</details>


### Clean up the Default Files

*Create React App* does load in a few bare-bones files and styles to give you a jumping-off point. However, you need to make a few changes to get everything cleaned up and ready for your new gallery app.

First; open `index.js`, located in `/furry-friend-gallery/src/` and remove the following line:

:writing_hand:

`import ‘./index.css'`

It should be on line 3 of the `index.js` file. This will just remove a link to the default styles from the project you won’t need.

#### Note!

If you use a later version of React for your build-along projects, remove the `<React.StrictMode>` tags from your `index.js` file. Strict mode was a troubleshooting tool that helped identify some common problems in development apps. However, it can also cause some unintended behaviour while you're getting familiar with some of the trickier Hooks, such as `useMemo`. You can read more about [strict](https://reactjs.org/docs/strict-mode.html) mode on the official React docs, but for now turn it off for this app, and I recommend doing the following for all of the upcoming projects too. 

To remove strict mode from your app, still in the `index.js` file, locate the code around line 9-10 that looks like this `<React.StrictMode>` and remove it. Also, find its matching closing tag, `</React.StrictMode>` a few lines further down and remove that too.

Once you remove strict mode and the default `index.css` file, your `index.js` file should look like this:

:writing_hand:

<details>
<summary>index.js</summary>

```
import React from 'react';
import ReactDOM from 'react-dom/client';
import App from './App';
import reportWebVitals from './reportWebVitals';

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(  
    <App />
);

// If you want to start measuring performance in your app, pass a function
// to log results (for example: reportWebVitals(console.log))
// or send to an analytics endpoint. Learn more: https://bit.ly/CRA-vitals
reportWebVitals();
```
</details>

Locate `/src/index.css` and delete the file.

Next, find the `/src/App.css` file and open it. 
- :writing_hand: Highlight all the contents and delete the existing styles. Save and close the file.

Finally, open the main `App.js` file located at `/src/App.js`. This currently contains starter JSX which you’re going to replace, as well as a link to a logo file you want to remove.

First, locate the following line (around line 2) that imports a `logo.svg` file, and remove it:

:writing_hand:

`import logo from ‘./logo.svg'`

Now, select everything in the return statement (everything between `return (` and `)` and replace it with the following so the new return statement looks like this:

:writing_hand:

```
return (
  <>
    <h1>welcome to furry friends gallery</h1>
  </>
);
```

Your project still contains a few default files, components, and assets loaded in by default, but don't worry about them for now as they’re not doing any harm just sitting there and they’re not currently being loaded anyway.

### Bulma CSS Framework

CSS frameworks, much like JavaScript frameworks and libraries such as React itself, allow you to employ some well-used and well-tested conventions to help build user interfaces more quickly.

CSS frameworks specifically, give you access to a design system, providing many individual elements, like buttons and heroes, that often combine into larger components, each maintaining the correct look and feel. They also offer layout systems, such as grids or column-based layouts.

You’ll be using one frequently throughout this course, the [Bulma CSS](https://bulma.io/documentation/components/card/) framework. It’s quite lightweight, simple to implement, and is based on modern CSS using Flexbox for much of its layout options.

It can be loaded via an `npm` package or simply dropped into an `HTML` file as an external stylesheet resource (which you’ll be doing here). There is even a React-based component library you can add to your project.

Take a look at the [Bulma doc](https://bulma.io/documentation/s) to familiarize yourself with the elements and components. For now,you’ll be adding to your app and using the CSS classes it gives to build your components.

## Create your Furry Friends Files

Your project will consist of just three files:

1. `App.js` — this is where the main action takes place

2. `DogCardInfo.jsx` — a reusable list item component to display each picture

3. `App.css` - you’ll need to add a couple of styles here to tweak the look and feel of your gallery list items

You also need to make a single line change in your `index.html` file that loads the Bulma CSS framework for you.

Let’s start there.

### Edit index.html

Navigate to `/public/index.html` and open the file. This is the main starting template `HTML` file the project uses to render the initial output of the app. It’s well commented and you can easily see what does what.

To load in Bulma so you can take advantage of the styles, add the following line somewhere between the opening and closing `<head></head>` tags:

:writing_hand:

```
<link
  rel="stylesheet"
  href="https://cdn.jsdelivr.net/npm/bulma@0.9.0/css/bulma.min.css"
/>
```

You can also edit the title of the page between the `<title></title>` tags if you wish.

### Create DogInfoCard.jsx

`DogInfoCard` will be a presentational component. It deals with the presentational aspects. but usually no logic (or very little). A presentational component will accept one or more values as props or direct arguments and simply return a block of JSX to render out.

Create a new file in the `/src` directory called `DogInfoCard.jsx` and drop in the following contents:

:writing_hand:

`touch /src/DogInfoCard.jsx`

<details>
<summary>ogInfoCard.jsx</summary>

```
import React from 'react';

export default ({ imgUrl, breed }) => (
  <div className='card dog-card'>
    <div className='card-image'>
      <figure className='image' style={{ backgroundImage: `url(${imgUrl})` }}>
        <img src={imgUrl} alt={`A ${breed} dog`} className='is-sr-only' />
      </figure>
    </div>
    <div className='card-content'>
      <div className='content'>
        <strong>breed:</strong> {breed}
      </div>
    </div>
  </div>
);
```
</details>

Use the destructuring syntax again to break the `imgUrl` and breed values out from the props object supplied to this component.

The markup here is based on Bulma’s card component. The subtle difference is you’ve employed a little image display trick on the figure element.

Because the dog images are returned from the API in different sizes and aspect ratios, you can’t just drop them directly in using an `<img />` tag. You could, but they’d look wonky and misshapen.

Apply the image to the figure element as a background, which (via some styling you’ll take care of next) allows more control of how things look when the images are weird sizes and shapes.

However, to keep things nice and semantic and look after those users working with screen-readers, use an `<img />` tag with the correct `src` and `alt` attributes, but apply a handy helper class, ‘`is-sr-only`’ which means the image won’t be visible on screen.

Render the breed name into the ‘`card-content`’ `div` as a nice addition so people can see what breed they’re looking at.

### Edit App.css

You have your list item display component read. Add a handful of styles into the `/src/App.css` file, which is loaded in at the top of our `App.js` file.

:writing_hand:

<details>
<summary>App.css</summary>

```
.dog-card .card-image {
  height: 15em;
}

.dog-card .image {
  height: 100%;
  background-repeat: no-repeat;
  background-position: top center;
  background-size: cover;
}
```
</details>

Nothing complicated, but you add a height to the main image container from the `DogCardInfo.jsx` component, otherwise it would collapse because its child, the image element, is hidden.

You also apply some background styles to the child figure element so the background is sized properly and doesn’t repeat.

### Edit App.js

Start by importing what you need. In your case the React defaults, your `DogCardInfo` component, and your main styles, `App.css`.

:writing_hand:

**App.js**

```
import React, { useState, useEffect } from 'react';

// components
import DogCardInfo from './DogCardInfo';

// styles
import './App.css';

```

### The loadDogPictures() Function

Next, outside of the main `App` component, define a new asynchronous function, `loadDogPictures()`, which will do exactly that; load a set of pictures from the API.

:writing_hand:

**App.js**

```
const loadDogPictures = async (dogsToLoad = 8) => {
  // TODO: load in some cute dogs
};
```

Here, define the outline of the function, adding the `async` keyword before your single argument, `dogsToLoad`. Set as a default to ‘`8`’. So, specify a number of dogs' pictures to fetch from the API or leave it blank and you’ll get eight back.

Next, add your variables:

:writing_hand:

**App.js**

```
  const apiBaseUrl = 'https://dog.ceo/api/breeds/image/random/';
  const response = await fetch(`${apiBaseUrl}${dogsToLoad}`);
  const data = await response?.json();
```

You have the static API URL, which is the base URL Dog CEO provides for a single random image. You can append this with a number (to a maximum of fifty) to return that many images.

Next, use the `await` keyword in front of the `fetch()` command which will pause the execution of things until the `fetch` method finishes up and returns. Notice how you use the template literals version of a String here to combine the base API URL and the number of pictures to fetch.

Perform another `await` as you call to the `json()` method on the response from the API call. This will get your dog-related data in a nice, malleable JSON format, ready for doing with as you please.

What happens next is some manipulation of the data returned from the API. In its raw state, the API returns something like this:

```
{
 "messages": [
    "https://images.dog.ceo/breeds/terrier-cairns/n02983056_1127.jpg"
    "https://images.dog.ceo/breeds/bullterrier-staffordshire/n02093256_1727.jpg"
    "https://images.dog.ceo/breeds/rottweiler/v983763017594_9910.jpg"
 ]
 //...other info
}
```

Loop through the image URLs in the messages array, capture the URL, generate an `id` value, and work out the breed.

Start by using `map()` to look at each image URL in turn.


:writing_hand:

<details>
<summary>App.js</summary>

```
  const dogData = data.message.map(item => {
    // item is an image url in the format:
    // "https://images.dog.ceo/breeds/bullterrier-staffordshire/n02093256_1727.jpg",
    let breed = item.replace('https://', '').split('/')[2];

    // bread clean up (i.e. starts in the format 'subtype-maintype')
    if (breed && breed !== '') {
      breed = breed.split('-').reverse().join(' ');
    }

    return {
      id: `dog_pic_${data.message.indexOf(item)}`,
      imgUrl: item,
      breed
    };
  });
```
</details>

Work out the breed by manipulating the URL string, removing the protocol, splitting the rest of the URL on the forward-slash character, and pull the breed from the item in the third position in this array.

Next, split the breed string by the dash character and reverse it. For some reason, that’s how the Dog CEO people log their breed information, in reverse name order.

Finally, wrap this up by returning an object to this iteration of the map we populate with a simply generated `id` from the index of the image URL in the original list, the image URL, and the breed name.

Once you have all that, return the array of dog item objects.

The full function looks like this:

:writing_hand:

<details>
<summary>App.js</summary>


```
const loadDogPictures = async (dogsToLoad = 8) => {
  const apiBaseUrl = 'https://dog.ceo/api/breeds/image/random/';
  const response = await fetch(`${apiBaseUrl}${dogsToLoad}`);
  const data = await response?.json();
  const dogData = data.message.map(item => {
    // item is an image url in the format:
    // "https://images.dog.ceo/breeds/bullterrier-staffordshire/n02093256_1727.jpg",
    let breed = item.replace('https://', '').split('/')[2];

    // bread clean up (i.e. starts in the format 'subtype-maintype')
    if (breed && breed !== '') {
      breed = breed.split('-').reverse().join(' ');
    }

    return {
      id: `dog_pic_${data.message.indexOf(item)}`,
      imgUrl: item,
      breed
    };
  });

  return dogData;
};
```
</details>

## App.js variables

As with all good components, start with the variables you will use. Define these at the top of the component to be used as you go along.

:writing_hand:

**App.js**

```
  const [dogPictures, setDogPictures] = useState([]);
  const [isLoading, setIsLoading] = useState(false);
  const [numOfDogs, setNumOfDogs] = useState('');
  const [totalDogsSearched, setTotalDogsSearched] = useState(0);
```

You have `dogPictures` which you create as an empty array to hold loaded dog picture objects, `isLoading` to determine if the component is in the process of fetching the dog photos, `numOfDogs` to track the user input on how many pictures they’d like to load, and `totalDogsSearched` which is a running total of how many pictures have been fetched in total.

### handleSubmit() function

You need a way to handle your form submissions and that’s where the `handleSubmit()` function comes in.

:writing_hand:

**App.js**

```
  const handleSubmit = async e => {
    e.preventDefault();
    setIsLoading(true);
    setDogPictures(await loadDogPictures(numOfDogs));
    setIsLoading(false);
  };
```

First, prevent the form from causing a page reload (as that will just kill off your app’s functionality) by calling `e.preventDefault()`; then toggle the `isLoading` state value to ‘`true`’ (which will be used later to set visibility on some of your UI elements); Once you’re loading, trigger a `fetch()` from the API via the `loadDogPictures()` function.

Last but not least, toggle `isLoading` back to ‘`false`’ once you have your pictures.

Notice the use of JavaScript’s built-in async/await pairing in use in this function.

## useEffect Hooks

This is where things get a little more interesting. You use two separate calls to the `useEffect` Hook to handle two side-effects.

Let’s deal with the first:

:writing_hand:

**App.js**

```
  useEffect(() => {
    setTotalDogsSearched(totalDogs => totalDogs + dogPictures.length);
  }, [dogPictures]);
```

This Hook call is just one line, but notice you passed in `dogPictures` into the dependency array. What you’re saying here is ‘when `dogPictures` changes, execute whatever code is inside of the `useEffect` call’.

Inside the body of the function, set the `totalDogsSearched` value in state to the current value plus the length of the new array of pictures you’ll have when the value of `dogPictures` changes.

You might be wondering about the unusual way you called the `setTotalDogsSearched()` function to update the `totalDogsSearched` value. Previously, you just passed in the new value you wish state to have, and that’s the way you usually do things.

However, if you did that here, although it would technically work, you’d be breaking one of the rules of Hooks and you’d most likely get an ESLint error that looks like this:

```
React Hook useEffect has a missing dependency: ‘totalDogsSearched’. Either include it or remove the dependency array. You can also do a functional update ‘setTotalDogsSearched(t => …)’ if you only need ‘totalDogsSearched’ in the ‘setTotalDogsSearched’ call. (react-hooks/exhaustive-deps)
```

Because you’re referencing a value from your component (in this case, the state value `totalDogsSearched`) but not including it in the list of dependencies passed to `useEffect` you get this error. It’s a potential problem because you run the risk of using stale data. Each time the component updates, changes, or rerenders the value of `totalDogsSearched` could be different. You’re referencing that value and so you can see how it could cause problems.

However, if you include it in the dependencies array, you most likely run into an infinite loop because this effect will then be called when dogPictures or `totalDogsSearched` changes. But of course, you are changing `totalDogsSearched` so you can update your UI. Every time it changes, you call the `setTotalDogsSearched()` function to update it, then the effect runs again, and so on until our app stops working.

The basic rule of thumb is; if you’re going to reference a function or a value from state or props inside of `useEffect`, then you must include it in the dependency array.

There is much more information available on this subject if you head over to the official [React documentation](https://reactjs.org/docs/hooks-faq.html#what-can-i-do-if-my-effect-dependencies-change-too-often).

However, you fix this by using the functional form of the state update method. So, instead of passing a direct value into the `setTotalDogsSearched()` function to update the value in state, you pass it a function, which will receive the current value from state as an argument.

This fixes the potential ‘stale data’ error you might run into and doesn’t run foul of the Hooks rules.

Define one last use of your `useEffect` Hook you’ll call with an empty dependency array, so it’s only triggered on the first component mount. In here, make sure you kick off an initial load of some dog pictures to populate our app with.

:writing_hand:

<details>
<summary>App.js</summery>
```
  useEffect(() => {
    (async () => {
      setIsLoading(loading => !loading);
      const dogPictureData = await loadDogPictures();
      setDogPictures(dogDataArray => [...dogPictureData]);
      setIsLoading(loading => !loading);
    })();
  }, []);
```
</details>

Once that’s defined you see you’re using the same form of functional update for setting various state values here. You also wrap all of these updates in a self-executing anonymous function (otherwise known as an immediately invoked function expression) that employs the async/await syntax.

You could have defined a regular function in here too and then immediately called it, and that would work just fine. I’ve gone with personal preference here and opted for a simpler syntax

## JSX markup

What would a component be without returning some markup to render your UI?!

Define a simple return statement with a container div element:

:writing_hand:

**App.js**

```
return <div className='container'></div>;
```

Here, you need two sections: one for the search form and another to render the dog pictures.

First, define the `header` and search form.

:writing_hand:

<details>
<summary>App.js</summary>

```
      <header className='columns section has-text-centered'>
        <div className='column is-6 is-offset-3'>
          <h1 className='title is-size-3'>
            Search for pictures of good doggos
          </h1>
          <form className='form' onSubmit={handleSubmit}>
            <div className='field has-addons has-addons-centered'>
              <div className='control is-expanded'>
                <input
                  type='text'
                  className='input is-medium'
                  placeholder='how many dogs should we look for (max 50)?'
                  value={numOfDogs}
                  onChange={e => setNumOfDogs(e.target.value)}
                />
              </div>
              <div className='control'>
                <button className='button is-primary is-medium'>
                  <span className='icon is-small'>
                    <i className='fas fa-search'></i>
                  </span>
                  <span>search</span>
                </button>
              </div>
            </div>
          </form>
        </div>
      </header>
```
</details>

Notice the few extra divs and class names from Bulma. You attached the `handleSubmit` function to the form and wired up the input element’s value to the `numOfDogs` value in state, as well as adding an inline state update in the `onChange` event to update that value whenever a user enters some text.

The button element in this section just serves to trigger the submission of the form.

The picture display section looks like this:


:writing_hand:

<details>
<summary>App.js</summary>

```
      <hr />
      <div className='has-text-centered'>
        <h3 className='subtitle is-size-4'>
          All time puppers found = {totalDogsSearched}
        </h3>
      </div>
      <div className='columns section is-multiline'>
        {isLoading && (
          <progress className='progress is-medium is-link' max='100'>
            60%
          </progress>
        )}
        {!isLoading &&
          dogPictures.map(dogPicture => (
            <div className='column is-one-quarter' key={dogPicture.id}>
              <DogCardInfo {...dogPicture} />
            </div>
          ))}
      </div>
```
</details>

You have a heading level three element where you display the total number of pictures searched for using the `totalDogsSearched` value in state.

Next, show an animated progress bar if the `isLoading` value is set to ‘`true`’.

Similarly, if `isLoading` is ‘`false`’, map over the `dogPictures` array in state, returning a new `DogCardInfo` component for each item in the array. This component is wrapped in a `div` with a ‘`column`’ class that will automatically render them side-by-side in the UI.

### The complete App.js file

Here’s the complete **App.js**

:writing_hand:

<details>
<summary>App.js</summary>

```
import React, { useState, useEffect } from 'react';

// components
import DogCardInfo from './DogCardInfo';

// styles
import './App.css';

const loadDogPictures = async (dogsToLoad = 8) => {
  const apiBaseUrl = 'https://dog.ceo/api/breeds/image/random/';
  const response = await fetch(`${apiBaseUrl}${dogsToLoad}`);
  const data = await response?.json();
  const dogData = data.message.map(item => {
    // item is an image url in the format:
    // "https://images.dog.ceo/breeds/bullterrier-staffordshire/n02093256_1727.jpg",
    let breed = item.replace('https://', '').split('/')[2];

    // bread clean up (i.e. starts in the format 'subtype-maintype')
    if (breed && breed !== '') {
      breed = breed.split('-').reverse().join(' ');
    }

    return {
      id: `dog_pic_${data.message.indexOf(item)}`,
      imgUrl: item,
      breed
    };
  });

  return dogData;
};

function App() {
  const [dogPictures, setDogPictures] = useState([]);
  const [isLoading, setIsLoading] = useState(false);
  const [numOfDogs, setNumOfDogs] = useState('');
  const [totalDogsSearched, setTotalDogsSearched] = useState(0);

  const handleSubmit = async e => {
    e.preventDefault();
    setIsLoading(true);
    setDogPictures(await loadDogPictures(numOfDogs));
    setIsLoading(false);
  };

  useEffect(() => {
    setTotalDogsSearched(totalDogs => totalDogs + dogPictures.length);
  }, [dogPictures]);

  useEffect(() => {
    (async () => {
      setIsLoading(loading => !loading);
      const dogPictureData = await loadDogPictures();
      setDogPictures(dogDataArray => [...dogPictureData]);
      setIsLoading(loading => !loading);
    })();
  }, []);

  return (
    <div className='container'>
      <header className='columns section has-text-centered'>
        <div className='column is-6 is-offset-3'>
          <h1 className='title is-size-3'>
            Search for pictures of good doggos
          </h1>
          <form className='form' onSubmit={handleSubmit}>
            <div className='field has-addons has-addons-centered'>
              <div className='control is-expanded'>
                <input
                  type='text'
                  className='input is-medium'
                  placeholder='how many dogs should we look for (max 50)?'
                  value={numOfDogs}
                  onChange={e => setNumOfDogs(e.target.value)}
                />
              </div>
              <div className='control'>
                <button className='button is-primary is-medium'>
                  <span className='icon is-small'>
                    <i className='fas fa-search'></i>
                  </span>
                  <span>search</span>
                </button>
              </div>
            </div>
          </form>
        </div>
      </header>
      <hr />
      <div className='has-text-centered'>
        <h3 className='subtitle is-size-4'>
          All time puppers found = {totalDogsSearched}
        </h3>
      </div>
      <div className='columns section is-multiline'>
        {isLoading && (
          <progress className='progress is-medium is-link' max='100'>
            60%
          </progress>
        )}
        {!isLoading &&
          dogPictures.map(dogPicture => (
            <div className='column is-one-quarter' key={dogPicture.id}>
              <DogCardInfo {...dogPicture} />
            </div>
          ))}
      </div>
    </div>
  );
}

export default App;
```
</details>

### View Your Hard Work

With everything coded up and set to go, that just leaves you to complete a final check to make sure everything’s working as it should.

:writing_hand:

`npm start`


#### Knowledge Check

| What is the default local host port that a React development server uses? |
|---------------------------------------------------------------------------|
| 5000                                                                      |
| 8080                                                                      |
| 3500                                                                      |
| 3000                                                                      |

<details>
<summary>Solution</summary>

3000

</details>

If everything’s working as planned, you should have a great looking site that shows us nothing but some well-behaved dogs doing what they do best.

#### Submission

1. Zip the Project Folder
2. Upload the zipped folder

## Vocabulary

| TERM             | DESCRIPTION                                                                                                                                                                                                                                                                                                                |
|------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ParentComponent  | The component doing the referencing can be thought of as the parent component, and the component being referenced can be thought of as the child component.                                                                                                                                                                |
| Hooks            | n React, Hooks allow developers to tap into some of the more powerful features of React, such as state and the component lifecycle, without writing some of the more cumbersome syntax required in more "traditional" class-based components.                                                                              |
| stateful logic   | Refers to a component’s state, the data stored by the component, and any associated logic to alter that state.                                                                                                                                                                                                             |
| useState         | A direct replacement for the class-based state you see used in this manner: // updating state<br>this.setState({<br>  someProperty: 'some value'<br>});<br><br>// using state<br><p>The value is {this.state.someProperty}</p>;                                                                                            |
| useEffect        | gives you a combined method that runs on each render of a component. It can be used multiple times to separate any distinct logic, and it can also be passed an array of dependencies React will monitor for changes. Should any of those dependencies change, only then would that particular useEffect call be executed. |
| key=             | This attribute is another React-specific attribute needed when rendering lists of things. It helps React keep track of changes within the list, such as if any have been added, removed, etc.                                                                                                                              |
| Create React App | An official React starter project, developed and maintained by the Facebook Open Source team and it offers all of the above (and more) in one, convenient package with just one dependency.                                                                                                                                |