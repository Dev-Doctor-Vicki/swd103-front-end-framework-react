# Fetching Data with React

This lesson introduces concepts and approaches you can use to deal with fetching data within React, including the JavaScript Fetch API.

## Fetch Data

At some point, you will have to interact with data from an external source. In a frontend application where React is employed, this will most likely be via an `API`. 

You’ll be looking at different ways to retrieve data from an external `API` and handle the response using the [Axios HTTP client package](https://github.com/axios/axios). 

### React’s support for APIs

As previously discussed, React is unopinionated in its implementation. Outside of a few conventions, such as the JSX syntax and Hooks, it pretty much leaves things up to you to develop how you wish. 

However, as you’ve also seen, this unopinionated nature leaves us with some gaps. Unlike its fellow frontend framework Angular, for example, which boasts a suite of built-in tools for talking to external sources like APIs, React leaves us with plain old JavaScript. 

This isn’t a huge issue as connecting to an `API` isn’t particularly difficult, but it does mean you have to make some decisions on how to do this, how to handle errors and creating some sort of library or utility class to wrap this all up in. Or, you could use a third-party package, such as **Axios**, which will be discussed in a moment.

### Data Fetching Options in React

Since you’ll be faced with rolling your own solution to access APIs in React, you’ll likely encounter one of these three most common solutions:

1. Use an XMLHttpRequest to implement an AJAX call.

2. Use JavaScript’s native Fetch API.

3. Add a third-party library, such as Axios (which is arguably the most popular).

Let’s go over each of these three and discuss their merits and drawbacks.

### AJAX Calls with XMLHttpRequest

When it comes to asynchronous data loading network requests, **AJAX** is at the forefront. It stands for "Asynchronous JavaScript and XML", and is a combination of the built-in `XMLHttpRequest` object, which goes off to the server to retrieve the data, and JavaScript and the DOM to use the data.

At its core is the `XMLHttpRequest` object. This is how you would fetch the data. 

An example implementation might look like this:

```
const loadNewQuote = () => {
  const xhttp = new XMLHttpRequest();
  
  xhttp.onreadystatechange = function() {
    if (this.readyState === 4 && this.status === 200) {
      const response = JSON.parse(this.responseText);
     document.querySelector("#output").innerHTML = response.contents.quotes[0].quote;
    }
  };
  xhttp.open("GET", "https://quotes.rest/qod?language=en", true);
  xhttp.send();
};
```

Assuming you have an element on the page called ‘`output`’, this snippet prepares a new `XMLHttpRequest` and opens the connection to a free quote API service, https://quotes.rest/qod. When the state changes and is successful (i.e. a readyState of 4), we parse the `responseText` object which represents the response data as a string.

You won’t see as many direct implementations of the `XMLHttpRequest` object in many codebases today. While perfectly ‘legal’ and legitimate code, it’s considered a little outdated by modern replacements like the Fetch API. 

Although it has support for older browsers (especially Internet Explorer), the `XMLHttpRequest` is a more complicated way to handle async network requests. It doesn’t support `Promise` changing very well and requires several wrapper functions to get the best from it.

## Use the Fetch API

The more recent approach to handle resource fetching, especially to external APIs, is the built-in [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API). 

It offers a more straightforward implementation than the previous `XMLHttpRequest` option; and boasts a more powerful and flexible feature set. Because it employs `Promises` quite heavily, subsequent action calls can be chained or swapped out for the `async/await` syntax. 

The downsides to the Fetch API are:

- Poor support for old browsers (with zero support for any version of Internet Explorer).

- Typical errors are not rejected, which can lead to false positives when expecting to catch errors like 500 or 404. 

- Timeouts and progress reports are not supported, meaning a request will continue as long as the browser chooses.

If you were to recreate the previous example using the Fetch API, the code would look like this:

```
const loadNewQuote = async () => {
  const response = await fetch("https://quotes.rest/qod?language=en");
  const json = await response.json();
  document.querySelector("#output").innerHTML = json.contents.quotes[0].quote;
};
```

As you can see, much more readable and clear and much easier to implement. 

### Leverage third-party Libraries or Packages

Much of modern JavaScript development makes use of the extensive `npm` library of third-party scripts, libraries, and utilities. There are always pros and cons to pulling in another package, and it’s up to you and your development team to weigh those and make the decision. 

However, there are times where employing a well maintained and supported package makes good sense. For situations with complex requirements, such as supporting different environments, browsers, error handling, using callbacks, and everything in between, sometimes it just makes sense to lean on an existing and robust solution. 

This is true when it comes to fetching data from APIs in commercial enterprise codebases. That’s why a package like `Axios` is so popular. `Axios` is a wrapper around the `XMLHttpRequest` object, providing much of the same familiar implementation of the `Fetch API` in the look and feel of how it’s used. 

Some of Axios’ main benefits over the previous two options include:

- Support for both browser and node development.

- Support for older browsers. 

- Interceptors for requests and responses.

- Automatic transformation of request and response data.

- Client-side protection against Cross-Site Request Forgery.

- Support for progress reports. 

As an `npm` package, you add it to your project in the same way as any other package:

`npm add axios`

Assuming you have installed `axios` and imported it into your code, you could achieve the same results as the previous two code examples as follows:

```
const loadNewQuote = async () => {
	const response = await axios.get('https://quotes.rest/qod?language=en');
  document.querySelector("#output").innerHTML = json.contents.quotes[0].quote;
};
```

You can read more on the comparisons between Axios and the Fetch API in this concise article from [Pluralsight](https://www.pluralsight.com/guides/axios-vs-fetch).

In the next section, you will implement Axios to build on your Furry Friends Gallery idea from the last module. 

## Furry Friends Gallery Mark II

Your code-along project for this lesson is to build a new Furry Friends Gallery. This time, you will use a new API and common UI patterns, such as a sidebar filter. Take a look at what you will build.

Your project will be based on the foundation of the Furry Friend’s Gallery you built in the last lesson. 

Here’s what you will build:

![](assets/react-dog-filter.jpeg)

You replace the original `input` box (allowed users to choose the number of pictures to display) with a sidebar containing a breeds filter. When the user selects a breed’s radio button, the dog pictures in the right-hand pane refresh using an API call. 

The list of breeds in the sidebar is also provided from the API. You’ll be able to page this list using previous and next buttons, each page being fetched directly from the API. 

This mechanism of filtering and listing data items, and paging those data items, all while interacting with an API endpoint, is a very common pattern in web development. Although it seems you’re just listing and filtering pictures of cute dogs, I’m sure you can already recognize this familiar layout pattern from just about every online shop or product category page.

You will use the [`Create React App` starter project](https://create-react-app.dev/) again to quickly scaffold your project, as well as the excellent `Axios` HTTP client package to handle API calls and data fetching. 

You’ll touch on the subject of a data handler to act as a middle man between your API and components. For this project, you’re going to use a slightly different API for your dog picture needs: [TheDogAPI - Dogs as a Service](https://thedogapi.com/). 

## Build Furry Friends Gallery Mark II

So here you are, ready to expand your API fetching and handling knowledge using the `Axios` package and grabbing data from TheDogAPI - Dogs as a Service.

You will move to this slightly different API provider for a few reasons:

1. It handles paging via the API.

2. It deals with API keys and sending them via request headers.

3. It offers more flexibility in the data that we can receive.

Overall, however, The Dog API will allow you to simulate a more realistic environment for requesting and receiving data in a realistic project.

Your first move will be to set up a new project using the Create React App helper, so let’s get started.

## :pushpin: L03HandsOnAssignment

### Requirements

1. Read all the guided learning text carefully for understanding.
2. Follow all instructions and coding step-by-step.
3. Create all files and folders for the Greeting App.
4. Complete all coding as demonstrated in all files.
5. Zip `L03ReactHandsOnPProject` Folder
6. Attach the zipped folder below where indicated for submission.

### Project Setup

Open a terminal window and navigate to the parent folder where you want to create the new project. Next, type the create react-app command as follows:

:writing_hand:

1. `cd desktop`

2. `cd FEFReact`

3. `mkdir lesson03-hands-on assignment`

4. `cd lesson03-hands-on-assignment`

5. `npx create react-app furry-friends-two`

The command line will finish installing the dependencies. Wait until you see the ‘success’ message and npm commands to start and build the app.

### Test the New Project

As you did in the last project, it’s always best to spin up the default, unchanged app you just made to make sure you’re starting from a solid, — and more importantly —  working base.

Enter the following commands:

:writing_hand:

1. `cd furry-friends-two`
2. `npm start`

Once the project’s built and launched, you should be able to fire up http://localhost:3000 in a browser and see dark background and spinning React logo you’ll soon come to know and love. 

### Clean Up the Starter Project

Create React App does load a few bare-bones files and styles to give you a jumping-off point. You’ll make a few changes to get everything cleaned up and ready for your new gallery app.

:writing_hand:

- First, open `index.js`, located in `/furry-friend-gallery/src/` and remove the following line:

`import ‘./index.css' ` 

  It should be on line 3 of the `index.js` file. This will just remove a link to the default styles from the project we won’t need. 

- Locate `/src/index.css` and delete the file.

- Find the `/src/App.css` file and open it. Highlight all the contents and delete the existing styles. Save and close the file.

Finally, open the main `App.js` file located at  `/src/App.js`. This currently contains starter JSX which you will replace, as well as a link to a logo file you will remove.

:writing_hand:

- First, locate the following line (around line 2) that imports a `logo.svg` file, and remove it:

`import logo from ‘./logo.svg'`

2. Now, select everything in the return statement (everything between `return (` and `)` and replace it with the following so the new return statement looks like this:

```
return (
  <>
    <h1>welcome to furry friends gallery</h1>
  </>
);
```

Your project still contains default files, components, and assets loaded in by default, but don't worry about them for now as they’re not doing any harm just sitting there, and they’re not currently being loaded anyway.

## Add Project Dependencies

Create and edit the files you need to get your project running, but first, add a couple of dependencies to your project. 

### Bring Axios Onboard

The first dependency to add is the `Axios npm package`. The benefits of Axios has been discussed already, but if you want to access those benefits and everything Axios offers, it’ll need to be part of your app.

Fortunately, it’s very straightforward to add. Back in a terminal window, make sure you’re in the root project location and enter the following command:

:writing_hand:

`npm add axios`

That’s it, quick and simple. Now,  import `Axios` and any of its helper methods using the import statement;

:writing_hand:

**App.js**

` import axios from ‘axios'`

### Add Bulma

You could add Bulma as a dependency just like Axios, but for familiarity’s sake, add it in the same way you did in the last project. 

Open the file `/public/index.html` . If you remember, this is the template `HTML` file the project uses to render the initial output of the app. 

Next, add the following line somewhere between the opening and closing `<head></head>` tags:

:writing_hand:

**index.html**

```
<link
 rel="stylesheet"
 href="https://cdn.jsdelivr.net/npm/bulma@0.9.0/css/bulma.min.css"
/>
```
You can also edit the title of the page between the `<title></title>` tags too if you like.

## Create Your App’s Files

You will be implementing a data handler to act as a middle man between the API and your components. There are a couple more files you will use.:

1. `App.js` — the familiar project starting point where all the magic happens.

2. `DogCardInfo.jsx` — a slightly modified component from the previous project that displays a dog picture and id value.

3. `BreedList.jsx` — a self-contained data-fetching component to handle its own data needs and display a list of breeds to filter our main picture list on.

4. `App.css` — add a few additional styles to make the dog card components look a little nicer.

5. `.env` — a new type of file to hold key variables that might change between environments. Store your API key and other data here.

6. `api.js` — your data handler-like library responsible for interacting with the API and returning it to the calling component.

So start editing your files and putting all the pieces together.

### Obtain an API Key

There are many free APIs, but most usually offer their APIs from behind an authentication key. This helps to limit abuse and helps the API provider keep track of the volume of requests across a given range of accounts. 

[TheDogAPI - Dogs as a Service](https://thedogapi.com/) is no different. Fortunately, it’s really simple to request an API key from them. 

First, head over to https://thedogapi.com and click on the large blue button saying ‘SIGNUP FOR FREE’. 

![](assets/dog-api-homepage.jpeg)

You’ll be taken to the following page where you enter your email address and a brief description of what you’ll be doing with your app.

![](assets/dog-api-signup.jpeg)

After clicking ‘SIGNUP’, you see a thank you screen and a message to check your inbox for the key. Head to your email and you should receive an email containing your shiny new API key like this:

![](assets/dog-api-example-key.png)

With your API, take a quick look at the documentation, which is another important aspect of your role as a frontend developer, especially when working with APIs. 

### Experiment with The Dog API Documentation

In a real-life frontend role, you will come across a situation where you have to connect to APIs to fetch important data for your UI. When facing this, you’ll hopefully have access to quality API documentation that outlines what endpoints are available, as well as information that explains how to call the endpoint — what parameters to supply, what the format of the data returned is, and so on.

With that in mind, take a quick peek at [The Dog API documentation](https://docs.thedogapi.com/). Fire up the URL in your browser of choice and you’ll see a screen similar to this. 

![](assets/dog-api-docs.jpeg)

The top links in the left-hand sidebar all refer to features and functionality of the API, along with details on how to authenticate to the API to use it.

Under the ‘API Reference’ heading, however, is where you find information about specific endpoints the API exposes. Click on ‘breeds’ and then ‘List the Breeds’ to view that page. 

![](assets/dog-api-test-results.jpeg)

The page contains information about authorization, which parameters you can supply to the `/breeds` endpoint (in this case there are three), as well as the response data you’ll receive following a successful call. 

The most useful part of this page is the ‘Send a Test Request’ section. If you hit the orange ‘Send’ button, the API will generate a sample call with real data. 

![](assets/dog-api-test.jpeg)

You use the API URL, https://api.thedogapi.com/v1, in a moment.

The response from the test API call looks like this:

```
[
  {
    "bred_for": "Small rodent hunting, lapdog",
    "breed_group": "Toy",
    "height": {
      "imperial": "9 - 11.5",
      "metric": "23 - 29"
    },
    "id": 1,
    "life_span": "10 - 12 years",
    "name": "Affenpinscher",
    "origin": "Germany, France",
    "temperament": "Stubborn, Curious, Playful, Adventurous, Active, Fun-loving",
    "weight": {
      "imperial": "6 - 13",
      "metric": "3 - 6"
    }
  },
  ...other entries
]
```

This is key as it helps shape the JSX in your components now you know what data is available and the shape of that data. 

## Create .env File

A file without a name but with some variation of `.env` as the extension is an environment file. It contains environment variables, which are pieces of information specific to particular development environments. 

For example, you might have a staging file and a production one, each containing the same variable names but with different values, each specific to their respective environments. 

There are no hard and fast rules to what you can keep in such a file, but generally, you’ll store relatively insensitive information that changes between different environments, such as URLs, names of things, or version numbers.

For your project, however, store the API URL and your API key in a `.env` file of your very own. 

At the root of your project, create a new file and don’t give it a name. Instead, just type the file extension directly, so it should read `.env`. 

:writing_hand:

`touch /src/.env`

Open the file and add the following information:

:writing_hand:

**.env**

```
REACT_APP_DOG_API_URL=https://api.thedogapi.com/v1/
REACT_APP_DOG_API_KEY=[YOUR API KEY]
```

Notice the API  noted before, https://api.thedogapi.com/v1/ . This is the base URL to call the dog API; you will append with specific routes like `/breeds` later on. 

The important point to remember is to replace `[YOUR API KEY]` with the API key The Dog API sent to you via email!

Generally, with `.env` files, you can put whatever variable and value combination you like in there. However, when it comes to Create React App, if you want to use any of the variables in here, you’ll need to prefix them with `REACT_APP_` or they won’t be read. For example, if you want to use a variable called `DINOSAUR`, you must name it `REACT_APP_DINOSAUR`.

Create React App does this to avoid exposing anything it shouldn’t that shares the same name. 

You can read more about [environment variables and Create React App](https://create-react-app.dev/docs/adding-custom-environment-variables/) in the official docs.

## Create DogCardInfo.jsx

The `DogCardInfo.jsx` component is very similar to the last one you created as part of the previous project. You do have a couple of small tweaks because of what data is available from The Dog API though, so let’s get going.

If you don’t have a `/components` folder underneath your `/src` folder, create that now. Next, create a new component file within this new folder and call it `DogCardInfo.jsx`. 

Copy in the following component body:

:writing_hand:

<details>
<summary>DogCardInfo.jsx<.summery>

```
import React from 'react';

export default ({ imgUrl, pictureId }) => (
  <div className='card dog-card'>
    <div className='card-image'>
      <figure className='image' style={{ backgroundImage: `url(${imgUrl})` }}>
        <img src={imgUrl} alt={`A nice dog`} className='is-sr-only' />
      </figure>
    </div>
    <div className='card-content'>
      <div className='content'>
        <strong>picture id:</strong> {pictureId}
      </div>
    </div>
  </div>
);
```
</details>

There’s nothing particularly special or fancy about this `component`.  It’s merely a repeatable presentational component used to show a nicely styled picture of each dog picture returned from the API.

The main difference here is you brought in a `pictureId` value unique to each picture. You use it to display it under the picture of each dog, purely for presentational reasons.

### Edit App.css

With the existing `App.css` file that comes with the Create React App project starter, update the styles so your dog pictures and radio buttons for breeds look much nicer. 

Open the `/src/App.css` file and replace the default starter contents with the following:

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

.breed-list .radio {
  display: block;
  margin: 0 0 0.5em;
  font-size: 1.25em;
}
.breed-list .radio input {
  margin-right: 0.5em;
}
```
</details>

As well as making each dog picture card component have a sensible height, make sure each breed radio button is displayed on its own line and playing with the spacing a little.

## Create api.js

Now for the really interesting part…fetching your data! Start by creating a new folder called `lib` under the `/src` folder. Next, create a new file, `api.js`.

:writing_hand:

1. `mkdir /src/lib`

2. `touch /src/lib/api.js`

Now start coding the API methods.

### Data Handlers

With the `api.js` file, you  introduce the concept of a data handler. Put simply, a data handler is a middle man of sorts that handles the communication between a data source and a data consumer. In your case, the data source is The Dog API and your data consumer is any component that wants information from the API, such as the `BreedList.jsx` component.

You could, of course, just have the code directly in each component, which is fine for small projects. But as soon as you start to repeat this API handling code things start to get messy:

- If you need to change something in the API fetching code, you’ll need to change it in multiple places. 

- Your components grow larger with additional API handling methods.

- Your components start to widen their responsibilities.

- Handling errors becomes more complex and distributed.

However, by abstracting this data fetching and handling facility into a single place, a data handler, you centralize all this logic and responsibility into one place. In reality, you might have a few data handlers that deal with different areas of the API, but the point of a data handler remains the same. 

You gain a lot of benefits:

- Only one place to edit and maintain data handling code.

- Your component code becomes more DRY (Don’t Repeat Yourself) and adheres to the Single Responsibility Principle (SRP) more closely as it should now just be dealing with handling incoming data and presenting it. 

- Errors are localized in one place. 

- You only need to define one interface with the API. 

You’ll dive into data handlers in more detail in an upcoming lesson, but for now, hopefully, you can get a glimpse of how useful they can be to any project that deals with fetching and handling data. 

### Define the callAPI() Function

Back in the `api.js` file, the first thing to do is bring in Axios and your environment variables:

:writing_hand:

**api.js**

```
import axios from 'axios';

const API_URL = process.env.REACT_APP_DOG_API_URL;
const API_KEY = process.env.REACT_APP_DOG_API_KEY;
```

The `process.env`is how you reference any environment variables. 

Next, define the `callAPI()` function which will actually go out and talk to the API:

:writing_hand:

<details>
<summary>api.js</summary>

```
const callAPI = async (url, params = null) => {
  const requestConfig = {
    baseURL: API_URL,
    headers: {
      'x-api-key': API_KEY
    },
    url
  };

  if (params) {
    requestConfig.params = params;
  }
  try {
    return await axios(requestConfig);
  } catch (err) {
    console.log('axios encountered an error', err);
  }
};
```
</details>

The `callAPI()` function accepts a url and a params parameter. The url will be the endpoint of the API we want to call. The `params` will be an object of any `key:value` pairs that need to be passed to the API.

Let’s break things down a little. First up is an object called `requestConfig`.

:writing_hand:

**api.js**

```
  const requestConfig = {
    baseURL: API_URL,
    headers: {
      'x-api-key': API_KEY
    },
    url
  };
```

Axios accepts a configuration object with a list of settings and options you can pass in to tweak your calls. You need to pass in the  `API_URL` value (from your environment variables) to the `baseUrl` property. You also need to supply an `x-api-key` value into the headers object with the `API_KEY` value so any API requests are authenticated and validated by The Dog API. Finally, supply the url parameter passed into the `callAPI()` function.

Axios will combine the `baseUrl` and url `config` values into a final API endpoint.

Next, do a quick check to see if the `params` parameter is not null. If it has a value, then add it to the `requestConfig` object and supply it to Axios.

After that, it’s a simple case of calling Axios using `axios(requestConfig)` passing in the `requestConfig` object. You’re wrapping the call in a try/catch block just in case, and handling the error by logging it out to the console.

In a production app, you’d want to handle the error in more detail and more gracefully than you have here. For example, you’d typically see some kind of log entry made and a message sent to the user to explain what happened and any next steps.

### Define the fetchBreeds() Function

With your generic `callAPI()` function in place, move to separate functions that request specific API endpoints and handle their returned data. The first of those  is `fetchBreeds()`. 

:writing_hand:

<details>
<summary>api.js</summary>

```
export const fetchBreeds = async (page, count = 10) => {
  const breeds = await callAPI('breeds', {
    limit: count,
    page,
  });

  return {
    breeds: breeds.data,
    totalBreeds: breeds.headers['pagination-count'],
  };
};
```
</details>

You’re employing the async/await coupling in all your methods because you’re dealing with asynchronous behaviors. First, call the `callAPI()` function and pass in the ‘`breeds`’ URL, along with a `params` object that populates the limit and page properties. These are used by The Dog API to determine how many results to return per page, as well as which slice of results (i.e. which page) from the entire results available. 

The Dog API documentation explains which parameters are available and what they do. 

Once the data returns, supply it back to the caller of `fetchBreeds` as an object with the breeds value being all the breeds data the API returned, as well as a `totalBreeds` value, which will be a numerical value of the total number of breeds the API has to offer. You’ll be using this later to page the results.

### Define the fetchPictures() Function

The last function to define in the `api.js` file is the `fetchPictures()` function. Here, you’ll be expecting a list of pictures of dogs that belong to a specific breed. 

:writing_hand:

<details>
<summary>api.js</summary>

```
export const fetchPictures = async (breed = '', count = 20) => {
  if (!breed) {
    return [];
  }

  const pictures = await callAPI('images/search', {
    breed_id: breed,
    limit: count
  });

  return pictures.data;
};
```
</details>

You need to know the breed, to return specific pictures from that breed. So, if the breed parameter is empty or not populated, return an empty array. This saves an unnecessary API call, but it also prevents the calling method from receiving a null value. An empty array is much nicer to deal with.

All being well, call the `callAPI()` once more, supply an API URL of `images/search` and another `params` object specifying the breed and number of pictures to return (you’ve defaulted this to 20 in the arguments of the function). 

Finally, return the pictures data the API returns.

### The Complete api.js file

Once everything is added, the complete `api.js` should look like this:

:writing_hand:

<details>
<summary>api.js</summary>

```
import axios from 'axios';

const API_URL = process.env.REACT_APP_DOG_API_URL;
const API_KEY = process.env.REACT_APP_DOG_API_KEY;

const callAPI = async (url, params = null) => {
  const requestConfig = {
    baseURL: API_URL,
    headers: {
      'x-api-key': API_KEY
    },
    url
  };

  if (params) {
    requestConfig.params = params;
  }
  try {
    return await axios(requestConfig);
  } catch (err) {
    console.log('axios encountered an error', err);
  }
};

export const fetchBreeds = async (page, count = 10) => {
  const breeds = await callAPI('breeds', {
    limit: count,
    page,
  });

  return {
    breeds: breeds.data,
    totalBreeds: breeds.headers['pagination-count'],
  };
};

export const fetchPictures = async (breed = '', count = 20) => {
  if (!breed) {
    return [];
  }

  const pictures = await callAPI('images/search', {
    breed_id: breed,
    limit: count
  });

  return pictures.data;
};
```
</details>

## Create BreedList.jsx

The `BreedList` component is going to ask your data handler for a list of dog breeds, display them as radio buttons in pages of a certain size, and handle paging between them. 

Start by creating a new file in the `/components` folder called `BreedList.jsx`.

:writing_hand:

- `touch/src/components/BreedList.jsx`

Open the `/src/components/BreedList.jsx` file and start with your imports section:

:writing_hand:

**BreedList.jsx**

```
import React, {useEffect, useState} from 'react';
import { fetchBreeds } from '../lib/api';
```

React must be in scope, but you’ll be using the `useEffect` and `useState` Hooks in a moment, so bring them along for the ride, too. After that, you need the `fetchBreeds()` function from your `api.js` file to fetch the list of dog breeds.

With the imports done, the best step is to outline the empty or bare-bones component and default export for the file:

:writing_hand:

**BreedList.jsx**

```
export default ({ dispatchBreedChange }) => {

  return (
      <>
        ...todo
      </>
  );
}
```

You’re destructuring the incoming props object to just a single argument, `dispatchBreedChange`. This will be a function passed down from the parent component called when a user selects an individual breed.

Remember, keep things within compartmentalized areas of responsibility. In practice, this means your `BreedList` component will list some breeds and handle paging through them, but it doesn’t know or care about what happens when someone selects a breed (other than keeping track of which selection they made). 

That job will fall to the parent component, which will be `App.js` , but we’ll cover that shortly. For now, all `BreedList` cares about is that once a user selects a breed radio button, it can call the `dispatchBreedChange()` function and carry on its way.

### Outline the Variables

With your skeleton component in place, the first thing is to define some variables:

:writing_hand:

<details>
<summary>BreedList.jsx</summary>

```
  const [value, setValue] = useState('');
  const [breeds, setBreeds] = useState([]);
  const [isLoading, setIsLoading] = useState(false);
  const [currentPage, setCurrentPage] = useState(0);
  const [totalPages, setTotalPages] = useState(0);
```
</details>

Create several `state` values using the `useState` Hook. 

In order, here are the uses of the variables:

- `value` - keep track of the currently selected breed value from the list of radio buttons.

- `breeds` - keep the current list of selectable dog breeds in here and update it each time the API returns.

- `isLoading` - a simple boolean flag set to ‘`true`’ when you’re doing API fetching and ‘`false`’ the rest of the time. You can use this to show/hide parts of the UI depending on if you’re loading or not.

- `currentPage` - someplace to keep track of the current page of dog breeds the user is on. 

- `totalPages` - you need to know the total number of pages to disable paging at certain limits. you’ll track that value in state here and update it each time the API updates the breeds list.

### Handle radio button Selection

Each time a user selects a radio button, the `onChange` event is fired. Capture it using the following function; `handleChange()`:

:writing_hand:

<details>
<summary>BreedList.jsx</summary>

```
  const handleChange = e => {
    setValue(e.target.value);
    
    if(dispatchBreedChange) {
      dispatchBreedChange(e.target.value);
    }
  };
```
</details>

This function should look quite familiar by now. Handling input changes via change event handlers is often done in this fashion.  You update value in state using the Hook `setValue` with the `e.target.value` value provided via the synthetic event. 

The extra bit you do here is inform the parent component that something has changed by calling the `dispatchBreedChange()` function from props after checking that it’s not a null or empty value. 

### Handle Paging Changes

Each time a user changes a page of dog breeds using ‘`next breed`’ or ‘`previous breed`’ buttons, you call this next function, `handlePageClick()` which accepts a `newPageNumber` argument.

:writing_hand:

<details>
<summary>BreedList.jsx</summary>

```
  const handlePageClick = newPageNumber => {
    if(newPageNumber < 0 || newPageNumber >= totalPages) {
      return;
    }

    setCurrentPage(newPageNumber);
  };
```
</details>

The purpose of this function is update the newly chosen page number in state using `setCurrentPage(newPageNumber)`. However, do a quick check before just in case the user has somehow managed to make a potentially faulty choice.

Check the new page number, and if it’s less than `0`, (page 1 of the results is actually page `0` in the array, because arrays are zero-indexed) or greater than or equal to the total number of pages, return from the method without doing anything to prevent errors in the UI from accessing pages that don’t exist.

### Update Breeds with useEffect

The last piece of the puzzle with your functions (before you move onto the JSX) is  handle the fetching and processing of your dog breeds data. This is a perfect job for the `useEffect` Hook. 

Let’s implement it now:

:writing_hand:

<details>
<summary>BreedList.jsx</summary>

```
  useEffect(() => {
    const loadBreeds = async () => {
      setIsLoading(loading => !loading);
      const breedsData = await fetchBreeds(currentPage, 15);
      setBreeds(breedsData.breeds);
      setTotalPages(parseInt(Math.ceil(breedsData.totalBreeds / 15),10));
      setIsLoading(loading => !loading);
    };

    loadBreeds();
  }, [currentPage]);
```
</details>

Notice how you pass in the `currentPage` variable in state to the dependencies array of the `useEffect` Hook. Now, every time `currentPage` changes, this `useEffect` Hook will fire.

Define an asynchronous function `loadBreeds()` which in turn calls the `fetchBreeds()` function from your API data handler. Pass it the current page and an arbitrary page size value of ’15’ — you could make this whatever value you wish and it will increase or decrease the number of breeds returned per page. 

The function follows a pretty simple flow:

1. Set the `isLoading` value so the UI can update.

2. Call out to the API to get a list of breeds.

3. Update state with the breeds once the API returns.

4. Update state with the total number of pages. This calculation divides the number of total breeds returned, by the API by the pages you want (i.e. 15 here). Use the `Math.ceil` function to round this figure up (because if you get something like 14.4 pages, you can’t have 0.4 of a page, but you can have a 15th page) and parse it as an integer with the built-in `parseInt()` function. 

Finally, update the loading value in state back to ‘`false`’ to update the UI again. 

### Fill out the return Statement’s JSX

The last move is to generate some JSX to return to the user. Start by adding in a conditional block of JSX that will show when `isLoading` is set to ‘`true`’:

:writing_hand:

**BreedList.jsx**

```
        {isLoading && (
            <progress className='progress is-medium is-link' max='100'>
              60%
            </progress>
        )}
```

The `<progress>` element is a native HTML element, but you can style it with Bulma to give it a little more pizazz. 

Next, you need an opposite block of JSX for when `isLoading` is ‘`false`’. This will be the default. 

:writing_hand:

<details>
<summary>BreedList.jsx</summary>

```
        {
          !isLoading && (
            <>
              <div className='field breed-list'>
                <div className='control'>
                  {breeds.map(breed => (
                      <label className='radio' key={breed.id}>
                        <input type="radio" name="breed" checked={value === breed.id.toString()} value={breed.id} onChange={handleChange} />
                        {breed.name}
                      </label>
                  ))}
                </div>
              </div>
              <br />
              <nav className="pagination is-rounded" role="navigation" aria-label="pagination">
                <a className="pagination-previous" onClick={() => handlePageClick(currentPage - 1)} disabled={currentPage <= 0}>
                  Previous
                </a>
                <a className="pagination-previous" onClick={() => handlePageClick(currentPage + 1)} disabled={currentPage + 1 >= totalPages}>
                  Next page
                </a>
              </nav>
            </>
          )
        }
```
</details>

Again, you lean on the Bulma framework’s specific way of defining radio buttons, making sure to assign `handleChange` to each radio button’s `onChange` event. 

For the `previous` and `next` paging buttons,  assign `handlePageClick` to their `onClick` events. The only difference is you pass in a value of the `currentPage` one lower or higher depending on if they use clicks ‘`previous`’ or ‘`next`’ respectively. 

### The Complete BreedList Component File

After everything’s been added, the complet `BreedList` component should look like this:

:writing_hand:

<details>
<summary>BreedList.jsx</summary>

```
import React, {useEffect, useState} from 'react';
import { fetchBreeds } from '../lib/api';


export default ({ dispatchBreedChange }) => {
  const [value, setValue] = useState('');
  const [breeds, setBreeds] = useState([]);
  const [isLoading, setIsLoading] = useState(false);
  const [currentPage, setCurrentPage] = useState(0);
  const [totalPages, setTotalPages] = useState(0);

  const handleChange = e => {
    setValue(e.target.value);
    
    if(dispatchBreedChange) {
      dispatchBreedChange(e.target.value);
    }
  };

  const handlePageClick = newPageNumber => {
    if(newPageNumber < 0 || newPageNumber >= totalPages) {
      return;
    }

    setCurrentPage(newPageNumber);
  };

  useEffect(() => {
    const loadBreeds = async () => {
      setIsLoading(loading => !loading);
      const breedsData = await fetchBreeds(currentPage, 15);
      setBreeds(breedsData.breeds);
      setTotalPages(parseInt(Math.ceil(breedsData.totalBreeds / 15),10));
      setIsLoading(loading => !loading);
    };

    loadBreeds();
  }, [currentPage]);

  return (
      <>
        {isLoading && (
            <progress className='progress is-medium is-link' max='100'>
              60%
            </progress>
        )}
        {
          !isLoading && (
            <>
              <div className='field breed-list'>
                <div className='control'>
                  {breeds.map(breed => (
                      <label className='radio' key={breed.id}>
                        <input type="radio" name="breed" checked={value === breed.id.toString()} value={breed.id} onChange={handleChange} />
                        {breed.name}
                      </label>
                  ))}
                </div>
              </div>
              <br />
              <nav className="pagination is-rounded" role="navigation" aria-label="pagination">
                <a className="pagination-previous" onClick={() => handlePageClick(currentPage - 1)} disabled={currentPage <= 0}>
                  Previous
                </a>
                <a className="pagination-previous" onClick={() => handlePageClick(currentPage + 1)} disabled={currentPage + 1 >= totalPages}>
                  Next page
                </a>
              </nav>
            </>
          )
        }
      </>
  );
}
```
</details>

## Edit App.js

While `DogCardInfo` and `BreedList` take care of their respective areas, the `App` component really orchestrates everything and wires up the two.

Map out the `App` component with the imports you need:

:writing_hand:

<details>
<summary>App.js</summary>

```
import React, { useState, useEffect } from 'react';

// data
import { fetchPictures } from './lib/api';

// components
import DogCardInfo from './components/DogCardInfo';
import BreedList from './components/BreedList';

// styles
import './App.css';
```
</details>

There shouldn’t be anything too unfamiliar here. You pull in React into scope, as well as `useState` and `useEffect` Hooks you’ll be using later on. You need `fetchPictures()` function from your API data handler, as well as the two components to display the breeds and dog pictures.

Last, pull in the `App.css` file, or your styles won’t be applied.

Next, define your variables:

:writing_hand:

**App.js**

```
  const [pictures, setPictures] = useState([]);
  const [selectedBreedId, setSelectedBreedId] = useState('');
  const [isLoading, setIsLoading] = useState(false);
```

- `pictures` will be used to keep track of the returned picture array data from the API.

- `selectedBreedId` will hold its namesake, which will be updated from the `BreedList` component when the user selects a breed.

- `isLoading` will act the same way as it does in the `BreedList` component; to update UI changes based on the loading state of the API.

### Fetch Pictures with the useEffect Hook

Just like in the `BreedList` component, the `useEffect` Hook is a great place to take care of fetching data from The Dog API when one or more conditions change. In your case, fetch an updated list of dog pictures from the API when the user selects a new breed — i.e. when the `selectedBreedId` value in state changes.

:writing_hand:

**App.js**

```
  useEffect(() => {
    const loadPictures = async () => {
      if (selectedBreedId !== '') {
        setIsLoading(loading => !loading);
        const fetchedPictures = await fetchPictures(selectedBreedId, 20);
        setPictures(fetchedPictures);
        setIsLoading(loading => !loading);
      }
    };

    loadPictures();
  }, [selectedBreedId]);
```

This has a very familiar look and operation to the `useEffect` defined in the `BreedList` component. Update the UI loading state, fetch pictures from the API, update state with the picture data, and reset the loading state to false.

Notice you pass in `selectedBreedId` to the dependencies array so the `useEffect` Hook fires whenever this value in state changes.

## Complete App by Building out the Return JSX

With all the logic in place, the final thing to do is outline the UI in JSX for the user. Your return statement looks like this:

:writing_hand:

<details>
<summary>App.js</summary>

```
    <div className='container'>
      <header className='section has-text-centered'>
        <h1 className='title is-size-3 has-text-primary'>
          Search for pictures of good doggos
        </h1>
        <p>Filter by breed for more choice!</p>
      </header>
      <hr />
      <div className='columns section is-multiline'>
        <div className='column is-one-quarter'>
          <h2 className='title is-size-4 has-text-info'>Search by breed</h2>
          <BreedList
            dispatchBreedChange={breedId => setSelectedBreedId(breedId)}
          />
        </div>
        <div className='column'>
          <div className='columns is-multiline'>
            {isLoading && (
              <progress className='progress is-medium is-link' max='100'>
                60%
              </progress>
            )}
            {!isLoading &&
              pictures.map(picture => (
                <div className='column is-one-quarter' key={picture.id}>
                  <DogCardInfo imgUrl={picture.url} pictureId={picture.id} />
                </div>
              ))}
          </div>
        </div>
      </div>
    </div>
```
</details>

The upper half of the JSX is a simple `<header>` tag with a title and strapline underneath. Following that, you use Bulma’s `columns` class which allows you to define two columns; one narrower left-hand column for the sidebar, with the larger right-hand panel for the picture gallery.

In the left-hand column, add a title ‘`Search by breed`’ and then add your `BreedList` component. 

Remember the `dispatchBreedChange` function you called in the `BreedList` component when a new breed selection was made? Well, this is where you’re going to pass a function down into the `BreedList` component to handle that.

The prop `dispatchBreedChange={breedId => setSelectedBreedId(breedId)}` passes in an inline arrow function which receives a `breedId` and updates state using the `setSelectedBreedId()` function. You could absolutely use a separate function here and that’d work just fine. However, it seems a little over the top to do that when all you need to do is update a single value in state. 

In the right-hand column, use the same two `isLoading` logic blocks from the `BreedList` component to display an alternate UI, depending on if you’re currently loading data from the API. 

If you’re not loading and you have a list of pictures, then use the array map function to return a new array of JSX elements, each of which includes a Bulma column that wraps a `DogCardInfo` component, passing in image URL and picture id values. 

### The Complete App Component File

With all of the above added in, the complete `App component should look like this:

:writing_hand:

<details>
<summary>App.js</summary>

```
import React, { useState, useEffect } from 'react';

// data
import { fetchPictures } from './lib/api';

// components
import DogCardInfo from './components/DogCardInfo';
import BreedList from './components/BreedList';

// styles
import './App.css';

function App() {
  const [pictures, setPictures] = useState([]);
  const [selectedBreedId, setSelectedBreedId] = useState('');
  const [isLoading, setIsLoading] = useState(false);

  useEffect(() => {
    const loadPictures = async () => {
      if (selectedBreedId !== '') {
        setIsLoading(loading => !loading);
        const fetchedPictures = await fetchPictures(selectedBreedId, 20);
        setPictures(fetchedPictures);
        setIsLoading(loading => !loading);
      }
    };

    loadPictures();
  }, [selectedBreedId]);

  return (
    <div className='container'>
      <header className='section has-text-centered'>
        <h1 className='title is-size-3 has-text-primary'>
          Search for pictures of good doggos
        </h1>
        <p>Filter by breed for more choice!</p>
      </header>
      <hr />
      <div className='columns section is-multiline'>
        <div className='column is-one-quarter'>
          <h2 className='title is-size-4 has-text-info'>Search by breed</h2>
          <BreedList
            dispatchBreedChange={breedId => setSelectedBreedId(breedId)}
          />
        </div>
        <div className='column'>
          <div className='columns is-multiline'>
            {isLoading && (
              <progress className='progress is-medium is-link' max='100'>
                60%
              </progress>
            )}
            {!isLoading &&
              pictures.map(picture => (
                <div className='column is-one-quarter' key={picture.id}>
                  <DogCardInfo imgUrl={picture.url} pictureId={picture.id} />
                </div>
              ))}
          </div>
        </div>
      </div>
    </div>
  );
}

export default App;
```
</details>

Run the Project

Save all of the files and run the project. Open up a terminal and enter the following command:

:writing_hand:

`npm start`

After a moment or two, the project will finish building and (depending on your setup) a browser window should open up and display the development URL, https://localhost:3000. If it doesn’t, then open up your favorite browser window and navigate to that URL yourself.

Take a look at the running app and play with it. You see a list of available breeds in the left-hand sidebar complete with previous and next buttons. Browse through the pages of dog breeds until you find one you like, and make a selection. 

Once you select a breed radio button, you see the right-hand panel update with a filtered list of dog pictures that match your chosen breed. Exciting stuff!

**Congratulations**! You’ve just built a fully working, browsable, gallery app that pages a list of breeds from an API, and then responds to user input, retrieving a filtered list of images from the same API. All the while, you used axios under the hood to provide a great cross-browser data-fetching experience!