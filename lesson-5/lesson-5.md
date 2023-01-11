# Thinking in React

Successful React development involves being able to 'think in React'. You'll look at breaking down UI's into components and their relationships.

In the early lessons of the course you looked at the relationship between components in React. No matter what the size, structure, and complexity of the React project you’re dealing with, when it comes to components there are broadly two roles a component will have: they will either be a **parent** or a **child**.

## Parent and Child Component Relationships

Most components are either a parent or a child and this isn’t a one to one relationship. Just like real life, parents can have parents, and children can themselves have children. The important thing to remember about this relationship is how you move information between them. It’s a core part of the React approach but one many beginners struggle with.

Here’s your earlier module’s diagram that highlights this information flow.

![](assets/props-and-events.png)

You can see that a parent component passes data or information down to a child via props, while a child component sends data and information back up to a parent via some sort of event. 

For example, a parent component which deals with calling an API to retrieve a list of users might pass a loading flag, `isLoading`, a value for the total number of users found, `totalUserCount`, and a function `nextPage()` to a child component which deals with displaying some paging elements in a nicely formatted way. The child component has a styled set of page elements and a button for the user to click to move onto the next page.

The child component, in this case, is purely presentational. It contains minimal (or zero) logic or state and is more concerned with accepting a range of prop values and displaying them in some formatted way. 

In this scenario, the parent component passes the data, our `totalUserCount`, `nextPage()` and `isLoading`  down into the child component via the props object. In the child component, you access them via a call to props like this, `props.totalUserCount` or `props.isLoading`. 

That’s fine, but what happens when the child component wants to change to the next page of results? 

The logic involved in fetching new results and organizing the returned data is handled by the parent component not the child component because the child component is purely presentational. 

At this point you might be thinking, ‘well couldn’t we just move the button that triggers the next page into the parent component?’. This is an option, but the button makes more sense in the context of the whole paging component. The parent component in this example is possibly doing several other things and contains several other related presentational components such as a data table and a search bar. 

So what’s the solution? Well, leave the button where it is, in the presentational child component, but attach the `props.nextPage()` function you passed down from the parent to the button’s `onClick` event. 

What happens now is that when the user clicks the next page button, the button’s `onClick` event is triggered, this calls whatever function is attached to this event, which in our case is the passed in `nextPage()` function. The `nextPage()` function will be called and since it lives in the parent component, it’ll be handled there. The parent component will likely fetch more results from the API and then pass these new results down to the child competent, which will re-render and complete React’s component lifecycle once more.

This is a fairly simple, yet common, example of data flow around your app and between components. Things can get complicated and a little ugly if you have many layers of children and you need to get data from a parent component higher in the chain to a much lower child component. This practice is called prop drilling and involves each child component in the chain having to grab a prop from its parent and pass it down to the next child. 

In a later module, you'll be looking at more complex data and state management using the `useReducer` Hook and the Redux state management pattern. These concepts can avoid scenarios like prop drilling and reduce the dependency of unrelated components on each other for the sake of passing props down the line. 

### Component Types in the Wild

Just about anything you come across in a React project that returns some JSX can be thought of as a component. However, not all components are created equal. There are several different component types you may come across when building projects as part of your job, contributing to open source projects or reading about in blog articles. 

Look through some of the more common ones to see what they are used for, what they look like, and how you can identify them. 

Keep in mind that because React is both very unopinionated and is based on JavaScript, which is itself a very loosely typed language, there can be a lot of overlap between styles of components. For example, a class-based component can also be a presentational component. The naming convention of a style or type of component can be a mixture of its role, its output or how it’s constructed. Nothing’s set in stone, but it can help to order your project and the building blocks that components represent.

### Class-based Components

A class-based component is really any component based on an ES6 class. They typically look like this, extending React’s Component class:

```
class MyClassComponent extends React.Component {
	state = {
		name: '',
		age: 0,
	};

	constructor(props) {
		super(props);
		
		// handle incoming props
		this.state.name = `${props.firstName} ${props.lastName}`;
	}

	render() {
		return(
			<p>Hi, {this.state.name}, you are {this.state.age} years old!</p>
		);
	}
}
```

You might remember the look of this style of component construction from our earlier lessons where we built out some class-based components and refactored them.

This component will accept some props such as `firstName` and `lastName`, does some string manipulation to turn it into a single name property in state and renders a paragraph element pulling in the name and age values from state. 

Class-based components will often have a file name that ends in `.js` rather than `.jsx`.

### Function-based Components

Function-based components are more simple in their implementation over class-based ones. They look more like a standard JavaScript function and often just return a block of JSX. They also can often be found with a `.jsx` filename. 

As mentioned above, since the introduction of **Hooks**, function components can now perform the same state interactions and lifecycle logic their class-based counterparts could previously. 

If you were to rewrite the previous class-based example, it would look like this:

```
function MyFunctionalComponent(props) {
	const [name, setName] = useState('');
	const [age, setAge] = useState(0);	

	useEffect(() => {
		setName(`${props.firstName} ${props.lastName}`);
	}, [props.firstName, props.lastName]);

	return(
		<p>Hi, {name}, you are {age} years old!</p>
	);
}
```

It’s important to note that both this example and the previous class-based one are functionally equivalent. They’re both pure functions, that is, given the same input, they will return the same output. It’s one of React’s only hard and fast rules.

### Presentational Components

Presentational components are components that deal almost exclusively in formatting and presentation of information with zero (or bare minimal) logic or stateful processing. 

They represent the base building blocks that your UI is composed of. 

Consider the very first example in this lesson of a paging component which accepts some data from a parent component and just deals with displaying it. This Paging component might look like this:

```
function Paging({ isLoading, totalItemCount, nextPage }) {
	const pageSize = 10;	 
	const pages = [];
  
  for(let i= 0; i <= parseInt(Math.ceil(totalItemCount / pageSize), 10); i++) 	{
    pages.push(<a className="page-item">{i+1}</a>);
  }
  
	return (
		<nav className="paging">
			{ pages }
      	<button onClick={nextPage}>next page</button>
		</nav>
	);
}
```

While containing some logic, it is minimal and purely used to work out how many pages to display. The component is still primarily presentational as it isn’t concerned with external data fetching, complex logic, state updates or lifecycle handling.

Another simple example might be a title component which displays a formatted header and subtitle like this:

```
const TitleDisplay = ({title, subtitle}) => (
	<>
		<h1 className="title is-size-1 has-text-primary">{title}</h1>
		<p className="subtitle is-size-2 has-title">{subtitle}</p>
	</>
);
```

As well as using the alternative construction of the component by using a `const` and an anonymous arrow function, you can see this component is much more of a simple beast. It accepts a `title` value, a `subtitle` value and returns a block of JSX that will format the input values consistently for our imaginary project.

### Container Components

Container components (sometimes known as management or manager components) are almost the opposite of presentational components. They usually contain very little presentational JSX markup, passing that responsibility to child presentational components (or other child management components). 

Instead, they’re concerned with app-wide state updates, fetching data to power child UI blocks, more complex logic and data synchronization tasks. 

This convention of breaking responsibilities into how things look with presentational components and how things work with container components is a great starting point if you’re new to structuring your React projects and want to know how to split out your components and their responsibilities.

### Higher-order Components (HOCs)

HOCs are a more complex offering, but you will come across them in real life projects and it’s worth identifying them and seeing how they tick.

All higher-order components are, like all components, still just JavaScript functions and JavaScript already has this idea of a higher-order function. This is a function that accepts functions as arguments and returns another function. They allow for a greater abstraction over actions not just values. A good example of a JavaScript higher-order function would be the built-in Array functions, `.forEach()` or `.map()`. 

Back to HOCs in React; these are an advanced React technique of creating a function that takes a component and returns a new component.

You’ll be using a number of HOCs in later lessons without even thinking about this concept, such as React Redux for managing a global state. Redux’s `connect()` function is a HOC, but you’ll discover this in an upcoming lesson.

There is a really good example of a higher-order component on the official [React docs](https://reactjs.org/docs/higher-order-components.html) that goes into a lot deeper of an example for a use case. 

However, for the base concept, you’ll discuss a simple example from the [CSS Tricks website](https://css-tricks.com/what-are-higher-order-components-in-react/) to illustrate the point.

```
const withUpperCase = (WrappedComponent) => (props) => {
  return (
	  <WrappedComponent {...props}>
  	{props.children.toUpperCase()}
    </WrappedComponent>
  )
}
```

This `withUpperCase` component receives a wrapped component as an argument, it then returns a new component where you convert the children to an uppercase string using `toUpperCase()`. 

To use this, you could create a component that receives some props and renders its children elements. 

`const Title = props => <h1>{props.children}</h1>`

Next, wrap this `Title` component in the higher-order component you created and save it in a variable. 

`const UpperCaseTitle = withUpperCase(Title);`

Then, render this in another part of your app like so:

```
function MyAwesomeComponent = props => (
	<div>
		<UpperCaseTitle>This is a page title here</UpperCaseTitle>
	</div>
);
```

The `UpperCaseTitle`  component renders the result of the HOC `withUpperCase` that was passed our `Title` component, transforming the string ‘This is a page title here’ to its upper case variation. 
