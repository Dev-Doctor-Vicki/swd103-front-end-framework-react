# Final Project: Dinosaur Search App

In this final lesson, you'll be putting your skills together and building a Dinosaur Search App.

Over the past nine lessons, you’ve covered a lot of ground. You’ve learned about React and the part it plays as a modular, component-driven approach to building modern, responsive front ends. 

Not only that but you’ve:

- Hooked it up to an API and made it more data-driven.

- Explored the common Hooks on offer, baked right into React.

- Extended the core React functionality with third-party libraries such as React Router.

You should be feeling pretty pleased with yourself for completing the projects you’ve built so far. Hopefully, by now, your React knowledge has grown to immense proportions and you’re feeling confident in your abilities to recognize the core concepts and build your own applications in this popular, powerful library.

### Introducing the Dinosaur Search App

In this final project, you’ll be pulling together all this React knowledge and building a more comprehensive app, The Dinosaur Search App. Imagine you have a team of palaeontologists who need to be able to look up information on a range of known dinosaurs. You can build an app to help them search for dino info and save a list of their favorites to refer to later. It’ll all be powered by an API that retrieves a list of all required dino data.

Let’s jump right in and take a look at what you’ll be building:

![](assets/dino-search-app.png)

You have a simple navigation up top and start your journey at the main search page. From here, you can either enter a name (or part name) of a dinosaur and search on that, or hit the ‘I’m feeling lucky’ button and be taken to a page of results filled with dinos whose names start with a random letter of the alphabet.

Either option leads to the results page where you see a list of matching dinosaurs. From here you can favorite them directly, which will add them to a list in the global app state, allowing users to visit them in a separate area once they’re logged in. 

If you want to find out more about a particular dinosaur, simply click the button or dino’s name and you can visit a special details page with all the information neatly laid out, including a picture of the beast in question.

Finally, you can simulate a sign-in process which will then allow a logged-in user to view their saved favorites in a convenient list.

## L10FinalProject

### Step 1: Plan out the Build

With larger, more complex apps such as this, even as a minimum viable product, where the functionality might be bare bones to begin with, it can be helpful to start by mapping out two things:

- A user journey or flow. 

- Some basic wireframes. 

The user journey allowsyou to roughly map out which parts of the app connect to each other, and how a user might navigate around an app from a given starting point. With the wireframes, you’re really aiming to loosely pin together your intended UI, placing components into groups, breaking down sections into more modular chunks, and trying to think about what will live on what page or pages — especially important in a system such as React where modularity is key!

I’ve already mocked these two things out for you to give you a flavor of how this could look, so let’s take a quick peek and see how these activities can be very useful tools.

### User jJurney or Navigation Flow

Here’s an example navigation flow (or user journey) for your small dinosaur searching app. 

![](assets/user-flow.png)

You can see that your users start out by hitting the search page, which will act as your home or default landing page. This kicks off the main flow of the app which should work like this:

1. From the starting search page, users trigger a search for a dinosaur. 

2. Next, they’re taken to a results page to see a list of matching dinosaurs returned from the API.

3. From here they can keep searching, or choose to view specific details of a particular dinosaur on the dino details page.

4. If they wish to sign into the app, they can visit the login page from any of the main navigation pages. They can only view the favorites page once they’ve authenticated. So, users must visit the login page and then the favorites. 

Of course, once they’re logged in, you’ll enable the favorites link from the main navigation, so they can get to it any time. For the purposes of this flow diagram, you want to explicitly call out the deliberate need to visit login then favorites. 

This is quite a simple diagram, but then you’re only building a relatively simple app (even though it’ll be more complex than the projects you’ve built so far). You might see lots of different versions of this sort of flow diagram in the wild, from actual flow diagrams with the correct diagram structures and types, to rough, hand-drawn sketches, and everything in between. Sometimes they might be larger in nature, encompassing more of an app all at once, or they might just show the flow for a particular area of functionality. 

Whatever their approach, the aim is to give you an at-a-glance idea of how a user will flow through various distinct parts or pages within your app. They can help immensely during the planning phase of any app you’re going to build. 

### Step 2: Wireframes

You’re not going to dwell on the wireframes too long, as I’m sure you’re keen to get building and put your React muscles to good use. Let’s move through the rough-and-ready wireframes for each of the pages you saw in the previous flow diagram. 

![](assets/wireframes-search.png)

![](assets/wireframes-results.png)

![](assets/wireframes-favourites.png)

![](assets/wireframes-sign-in.png)

![](assets/wireframes%20(1).png)

You can see that each page’s wireframe is much like the flow diagram: quite simple and low-fi. What you’re doing here is getting down on paper (so to speak) the general idea of which parts of the UI live on which pages and roughly how they are intended to look. 

Again, in the wild, you might see lots of different versions of this sort of thing, from hand-drawn sketches, physical paper models and simplistic low-fi versions, through to full-on UI mock-ups made by design teams. 

Just like the user journey diagrams, the idea with wireframes is to give a general starting direction for the feel of the UI; what sort of data will be displayed on the page, in what way, and  with which key interaction points (things like animations, parts of the UI that switch or change, and so on). 

They help so much by providing a reference point that you can keep coming back to whilst building a component or part of an application. 

## Step 3: Build the App

With this being a larger, more complex app than you’ve built thus far in the course, the remaining sections in this lesson will be broken down like this:

1. In section two you’ll get all of the project scaffolding and setup done, including looking at the API you’ll be using and adding in the skeleton React Router structure.

2. In section three you’ll add in some services that will act as kindly data-ferrying middlemen, providing data to your components and updating items in your Redux store. 

3. In section four you’ll flesh out the components and the UI parts of the app.

4. And finally, in section five, you’ll fire everything up and bask in your mighty coding glory as you take the Dinosaur Search App for a test flight.  

Let’s dive into the next section and get cracking. 

## Section 2: Project Setup and Scaffolding

You put the foundations of the Dinosaur Search App in place, such as creating a new React app and exploring the supporting API

You may be unsurprised to learn that dinosaurs are quite old and that there isn’t much data around for a fair few of them. What is a little surprising however, is that there are zero live APIs out in the wild that deal with dinosaur-exclusive data!

But fear not, because I have got you covered. In the accompanying course material there is [starter project code](https://github.com/Dev-Doctor-Vicki/api-server-starter) for this project that includes a `client` and a `server` folder. You’ll be building everything you have planned in the `client` folder, but the `server` folder houses a fully-functional Node-based API that will provide you with all you need to serve your app. 

- If you’re interested in learning more about this Node server starter-kit, then here is a [helpful article](https://robkendal.co.uk/blog/how-to-build-a-restful-node-js-api-server-using-json-files) on this very subject.

You’re going to quickly spin up the API `server` and take a brief look at the queries you can make, and the sort of data you’ll get back, as well as how to start it.  

### Open and Run the Starter Project

Here’s where you can grab the project files for this course, if you haven’t already done so. You’ll need them for this last project as they include the API server, which you won't be building from scratch!

Click below to download the Starter Code.

[Starter Code](assets/start)


Once you’ve downloaded the project files, I’d recommend creating a new folder on your local machine to house them. For example I created the following empty folder on my local machine, called /code/demos/dino-search-app. 

Copy the contents of `/lesson-10/start/` into your new local folder. When you’re finished you should have a folder structure that looks like this:

```
/dino-search-app

--/client

--/server
```

Open the project in VS Code (or your favorite editor) and let’s take a look at the API files.

### Queries and Routing

In the `/data` folder you have `dinos.json` file. If you take a look at this file, you’ll find a huge list of more than 50 objects that each represent a collection of information points for a particular dinosaur. Each has a unique id value, name, location, image filename, and some other relevant info.

Open the `/server.js` file and you’ll see the bare minimum required to get an ExpressJS-powered Node server up and running. This file creates the server, `app` and plugs in the `/routes/routes.js` file, which serves various API end points. 

The other thing to note here is that you explicitly define a static `/images` route that will allow you to serve images directly from the API as image data, rather than a set of JSON results, as with the dinosaur data. In the frontend client app we can call this endpoint `/images/image_name.jpg` and simulate grabbing an image from a CDN, rather than having to house the dinosaur images locally in your frontend project where they don’t really belong.

If you look in the `/routes` folder, you have a main `routes.js` file that collects the other two files in this folder (`dinos.js` and `login.js` ), and plugs them into the Express JS server you just outlined.

### login.js

The `login.js` file serves a single route, `/api/login`, and is fairly dumb in its operation. If there is both a `username` and a `password` value supplied, it will generate a dummy JWT token and return it to the caller. If neither is supplied, then it returns a `401` error to indicate the user is unauthorized. Of course, this is not in the least bit secure or hooked to up an actual authentication system, but it will serve the purpose of simulating one for your frontend client app, which doesn’t need to care about the backend implementation anyway, just what’s returned from the API.

### dinos.js

The `dinos.js` file is where the API peovides for all the dinosaur-related endpoints. It handles:

- `/api/dinos` for fetching all dinosaur information.

- `/api/dinos/search/:term` for fetching a filtered list of dino information based on whether the `:term` parameter is a single character or a name of some sort.

- `/api/dinos/:id` for grabbing a single dinosaur object.

You don’t need to go into go into great detail about the inner workings of the API setup here (you can refer to [the article](https://robkendal.co.uk/blog/how-to-build-a-restful-node-js-api-server-using-json-files) on this exact thing if you’re curious, but the basic operation is that each API endpoint uses the Node `fs` function to read the `dinos.json` file. Depending on the endpoint called, the resulting JSON data is filtered and formatted and then returned to the caller as JSON data.

### Runn the API

Before you plough on with the front-facing client app, quickly spin up the API server and check that it’s working OK. 

In your terminal, navigate to the root of the `/server` folder. If this is the first time you’re running it, you’ll need to install the dependencies with the following command:

`npm install`

Once that’s finished, run the start command:

`npm start`

Nothing exciting will happen at this point, but you should see a message in the terminal saying ‘listening on port 4000…’ which means the API server is running.

If you open a browser and navigate to http://localhost:4000, you will see a white screen with the message ‘welcome to the development dinosaur api-server’ displayed. If you navigate to the main dinosaur route, http://localhost:4000/api/dinos, you’ll see a more interesting result — the list of all dinosaur information objects returned to the browser as JSON data.

## Project SetUp

Now that you have a good idea of what’s what with the API server part of the project, you need to focus on building the frontend client app in your beloved React. Once again you’ll be calling on the Create React App project to get off to a best-practice flying start. 

### Install a New Create React App Project

Since you’ve already have a starter project and a folder, `/client`, where you want your frontend files to live, using the default Create React App project command can be a little more cumbersome, but let’s walk through it and see if we make it out the other side unscathed. 

Open a terminal window to the dinosaur project folder and leave it at the project root (the one at the same level as the `/client` and `/server` folders). Now run the familiar command:

```
npm create react-app dino-search-app
```

Once it’s finished, open the folder on your machine and cut-and-paste all of the contents into the `/client` folder. Once this is done, delete the dino-search-app folder, as you don’t need it.

### Project Clean Up

Now that you have a shiny new Create React App instance installed and ready to go, let’s move through the same parts as in previous lessons, and clean up the default files a little. 

- Open `index.js`, and remove the reference to `import ‘./index.css'`. 

- Delete the `/src/index.css` and `/src/App.css` files.

- Open the main `App.js` file and empty it so you can add your demo-specific code later on.

### Add Dependencies

You’ll need to add a few dependencies to your app:

- `axios` - your old friend axios will help talk to your dino API and fetch the important fossil data for.

- `bulma` - no surprises here, Bulma will add nice styles and look and feel to the UI.

- `react-router-dom` - again you’ll be leaning on this package to route your users between pages and areas of the app.

- `@fortawesome/fontawesome`-free - this is a new one, but the popular font-icon library provides support to Bulma, allowing you to display some nice icons along with the dinosaur information.

- `node-sass` - you’ve used node-sass previously to compile your `.scss` files into the browser-useable `.css` files.

Let’s work through them, adding them all to the project via the familiar `npm add [dependency name]` syntax you know and love. I’m adding them one by one here for clarity and to ensure there’s no mistakes that could be caused by misspellings or typos. 

- `npm add axios`
- `npm add bulma`
- `npm add react-router-dom`
- `npm add @fontawesome/fontawesome-free`
- `npm add node-sass`

### Wire up the Proxy

This is going to be an unfamiliar part of the process that you’ve not covered yet. However, the Create React App scripts offer a special property, `proxy` that you can add to your `package.json` file. 

Open the `package.json file and add the following line at the end of the file:

```
"proxy": "http://localhost:4000"
```

Essentially the proxy property here allows you to proxy (i.e. an intermediary for requests) requests between front and back-end apps using the same port. It’s something that can happen more frequently in local development scenarios. 

Your front-end app and the API you’re calling aren’t running on the same port, but this does have an added benefit of not having to deal with irksome [CORs errors](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) and blockages, especially when you don’t have any security concerns at the moment since you’re running everything on your local, sealed off machine. Also, it means you can avoid writing longer API URLs because the value you add here is automatically prepended to requests that aren’t text/HTML in nature — e.g. API calls. 

You can read more about the proxy function in the [Create React App documentation](https://create-react-app.dev/docs/proxying-api-requests-in-development/).

### Add the Dino Logo

You will use a little cartoon dinosaur mascot as your logo in a few places throughout your app, namely this little guy:

![](assets/cartoon-dino.png)

You can grab him from the finished course files or download him from this lesson text. Simply right-click and save him somewhere on your desktop. 

Make sure to name him `cartoon-dino.png` and copy him into the `/client/public/` folder in your project. 

## Files and Foolders Creation

With all your dependencies added and your proxy set up, create all the files and folders you need for the project and build them out.

```
root/
├─ server/
├─ client/
│  ├─ services/
│  │  ├─ api.service.js
│  │  ├─ auth.service.js
│  │  ├─ dino.service.js
│  ├─ redux/
│  │  ├─ authReducer.js
│  │  ├─ dinoReducer.js
│  │  ├─ initialState.js
│  │  ├─ reducers.js
│  ├─ components/
│  │  ├─ DinoBrowse.jsx
│  │  ├─ DinoCard.jsx
│  │  ├─ DinoDetails.jsx
│  │  ├─ DinoSearch.jsx
│  │  ├─ Favourites.jsx
│  │  ├─ Layout.jsx
│  │  ├─ LoginForm.jsx
│  │  ├─ Navigation.jsx
│  ├─ assets/
│  │  ├─ styles.scss
│  ├─ index.js
│  ├─ App.js
```

### index.js

Included out of the box; you only need to bring in a Redux provider component here and wrap it around your app’s starting point.

### App.js

Another default component; but you’ll be building this out as a centralized routing station of sorts to handle navigation route matching to the correct components via the React Router library.

### services/api.service.js

The first of your service-layer handlers that you’re introducing to act as a clean middleman that will handle API requests for other parts of the app, returning necessary data.

### services/auth.service.js

The second data-handling service helper; the auth service will handle any user sign-in requests, calling the API and any Redux interactions you need as part of it. It’ll also offer two custom Hooks you’ll explore in more detail as you build out the code.

### services/dino.service.js

The final service helper will handle requests to the API for dinosaur-related data and update any relevant Redux calls.

### redux/authReducer.js

Just as in the previous lesson on Redux, the `authReducer`  will deal with updates to the auth-specific slice of global app state.

### redux/dinoReducer.js

Another reducer that will deal with dinosaur information updates to global state.

### redux/initialState.js

Just like you had in the Redux lesson, `initialState` offers a good blank slate starting point for your app’s initial global state.

### redux/reducers.js

This will be almost identical to the same file from the previous lesson. Its function is to pull together the separate reducers and combine them all, wrapping everything up in an exported `Provider` component.

### assets/styles.scss

Nothing flashy here; just a `.scss` file that pulls in the FontAwesome and Bulma styles and adds a couple of helpful styles for some of the components you’ll be building later on.

### components/DinoBrowse.jsx

When built, this component will render a list of dinosaur information card elements and allow the user to search by name or by alphabetical letter category.

### components/DinoCard.jsx

A semi-presentational component that will display relevant dinosaur information in a tidy and clear manner and allow users to favorite a dinosaur.

### components/DinoDetails.jsx

This will be a much larger component that handles retrieving data for a specific individual dinosaur and then displaying it, again allowing the user to favorite (or unfavorite) the dino in question should they choose.

### components/DinoSearch.jsx

A relatively simple component with some scant logic that displays a search box and a couple of buttons to the user to allow them to search for dinosaurs by name. This will also represent the home page for your app.

### components/Favorites.jsx

The `Favorites` component will display a list of dinosaurs in a similar fashion to the `DinoBrowse` component. The difference here is that it will retrieve the list of dinos to view from the favorited information held in our global Redux store.

### components/Layout.jsx

With this component you’re effectively adding a styled structure around the other child components. It’ll contain app-wide UI that includes things like a header and navigation, as well as some global spacing elements. It’s also purely presentational.

### components/LoginForm.jsx

Perhaps unsurprisingly, this is what you’ll show users when they wish to sign into the app. It’ll house a simple username/password form and pass these details on to the API via the service handlers.

### components/Navigation.jsx

Another semi-presentational component that will provide an app-wide navigation element containing various React Router Link components that ferry your user around the child pages and areas within your app.

Now you’re clear on what you’re building, and created the bare bones of the files, let’s work through them and flesh them out!

## Styles.scss

Open the `/assets/styles.scss` and copy in the following styles:

<details>
<summary>src/assets/styles.scss</Summary>

```
// IMPORTS
@import "../../node_modules/@fortawesome/fontawesome-free/css/all.min.css";
@import "../../node_modules/bulma/css/bulma.min.css";

// Overrides
body {
    min-height: 100vh;
}

.field-label {
    flex-grow: 0.5;
}

.dino-card {
    figure {
        background-repeat: no-repeat;
        background-position: center center;
        background-size: cover;

        img {
            visibility: hidden;
            height: 0;
            width: 0;
        }
    }
}

// Dino card
.dino-card {
    .dino-card__favourite {
        z-index: 2;

        &:hover {
            text-decoration: none;
        }        
    }
    
    &.is-favourite {
        border: 1px solid #01ca592e;
    }
}

// Dino details
.dino-info {
    .dino-info__favourite {
        &:hover {
            text-decoration: none;
        }        
    }
}
```
</details>

The import statement at the top of the file brings the Bulma CSS framework styles into the project. The rest of the file should be fairly clear; you don’t have any tricky styles or crazy CSS wizardry here. The remaining styles are helpful to handle the oddly-shaped images you have for your dinosaurs, setting background sizes and such. You also remove some unsightly underlines around the favorite icons you’ll add in later on.

### index.js

There’s barely any work to do in the `index.js` file. You just need to bring in the `StoreProvider` you’ll create shortly and wrap it around the `App` component. 

Open the file and import the `StoreProvider` at the top of the file. 

<details>
<summary>src/index.js</summary>

```
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';
import reportWebVitals from './reportWebVitals';


// Reducer store
import { StoreProvider } from './redux/reducers';


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

</details>

Next, just wrap this component around the `App` component as you did in the last lesson, and save the file. 

That’s us all done here for now. Let’s move on to the last edit in this part. 

## App.js and Routing with React Router

Your `App` component is a routing office of sorts. It’s not going to house any logic, but it will contain the main navigation routing paths and their corresponding components that handle them. 

### Add the Imports

Open the empty `App.js` file and let’s bring in the imports:

<details>
<summary>src/App.js</summary>

```
import React from 'react';
import { BrowserRouter as Router, Switch, Route, Link } from 'react-router-dom';

// Styles
import './assets/styles.scss';

// Components
import Layout from './components/Layout';
import LoginForm from './components/LoginForm';
import DinoSearch from './components/DinoSearch';
import DinoBrowse from './components/DinoBrowse';
import DinoDetails from './components/DinoDetails';
import Favorites from './components/Favouites';

```
</details>

You’re bringing in many tools from the react-router-dom toolbox here to help with routing, just as you did in the React Router lesson. Next you have your styles file to help applying Bulma to your app. Finally, you’re bringing in all the components you’ll need to handle the various routing scenarios as per your user flow diagram.

Don’t worry that you haven’t built these files out yet. You’ll be doing that as we move along. You can bring them onboard here because you won’t need to edit the `App` component any further, and at the moment, none of the components will need any additional logic to be carried out here either.

### Add the Component Body

Now, let’s add in the body of the component and set up the route handling:

<details>
<summary>src/App.js</summary>

```
const App = () => {

  return (
    <Router>
      <Layout>
        <Switch>
          <Route path="/login" component={LoginForm} />
          <Route path="/favourites" component={Favourites} />
          <Route path="/search" component={DinoSearch} />
          <Route path="/browse/:name" component={DinoBrowse} /> 
          <Route path="/browse" component={DinoBrowse} />          
          <Route path="/dinos/:id" component={DinoDetails} />
          <Route path="/" exact component={DinoSearch} />
          <Route>
            <h1 className="title is-size-2">404 - Page not found</h1>
            <p>Apologies, you've hit a route we hadn't planned for.</p>
            <p>Go back to the <Link to="/">search page</Link> to start again</p>
          </Route>
        </Switch>      
      </Layout>
    </Router>
  )
};

export default App;
```

</details>

As a general rule, you should wrap your app in the `<Router />` component (or each major part if the app is large enough to justify this approach), which is what you’re doing here. By doing this, you allow all the child parts and components of your app access to the React Router system. 

For example, this is useful for how you’re using the `<Layout />` component here, which is added just under the `<Router />` component. When you build it out later on, it will contain a navigation element that uses some other Hooks and features of the React Router system. To make use of these, you need to provide access to React Router’s main component higher up in the component tree, just like you’re doing here.

After this, you’ll recognize the `Switch` component that you’ve used. The `Switch` component is designed to match and render routes exclusively. In practice, it means you have more precise control over what is rendered for each route. You have very distinct route-handling needs here for each separate page in your dino-searching app. `Switch` helps make better matches against each navigation route a user visits. 

You can read more about `Switch` on the [React Router docs](https://reactrouter.com/web/api/Switch).

Inside of the `Switch` component is the meat and potatoes of your route-matching system. You can see that you have a number of mostly self-explanatory routes that will render the components you imported earlier, depending on the route they chose. 

You can recognize the route parameter format from the earlier lesson on routing here, too. Routes like `/browse/:name` will match URLs such as `/browse/an-interesting-name` or `/browse/123`. Notice too, how you are listing route paths in order of most-specific to least-specific. The `Switch` component works in this way too, so you’re mirroring that. You’re not making much use of this system here by supplying different components for the same route, but which features a parameter, but you may want to in other apps, so it’s useful to see an ideal approach to handling that scenario.

**NOTICE!**

Notice that in this app you’ve decided to just hard-code the route paths as strings. In the lesson on routing, you opted to pull your routes from a `routes.js` file and dynamically loop through them. To keep the overhead a little lower in this more complex dino app, you’ve chosen the direct paths-as-a-string approach, but you just as easily use whatever method you like here. As long as each `Route` component has a path attribute to match against a URL and a `component` attribute to know what to render when the path matches, the routing system is happy. 

So, each `Route` component here matches a path and renders the correct component to handle it. You have one final, non-explicit `Route`, which will be rendered if nothing else matches. For example, if a user navigates to `/apples`, you don’t have a matching route for that, and this catch-all block of JSX will be rendered. It’s just a simple `h1` element explaining the classic ‘404 - Page not found’ message and giving users a way out, back to the safety of the home page.

#### Completed component

With everything finished in the App component, it should look like this:

<details>
<summary>src/App.js</summary>

```
import React from 'react';
import { BrowserRouter as Router, Switch, Route, Link } from 'react-router-dom';

// Styles
import './assets/styles.scss';

// Components
import Layout from './components/Layout';
import LoginForm from './components/LoginForm';
import DinoSearch from './components/DinoSearch';
import DinoBrowse from './components/DinoBrowse';
import DinoDetails from './components/DinoDetails';
import Favourites from './components/Favourites';

const App = () => {

  return (
    <Router>
      <Layout>
        <Switch>
          <Route path="/login" component={LoginForm} />
          <Route path="/favourites" component={Favourites} />
          <Route path="/search" component={DinoSearch} />
          <Route path="/browse/:name" component={DinoBrowse} /> 
          <Route path="/browse" component={DinoBrowse} />          
          <Route path="/dinos/:id" component={DinoDetails} />
          <Route path="/" exact component={DinoSearch} />
          <Route>
            <h1 className="title is-size-2">404 - Page not found</h1>
            <p>Apologies, you've hit a route we hadn't planned for.</p>
            <p>Go back to the <Link to="/">search page</Link> to start again</p>
          </Route>
        </Switch>      
      </Layout>
    </Router>
  )
};

export default App;
```

</details>

### Next Steps

That’s all there is to the `App` component. It’s a navigation route-handling train station, passing the user the correct component for their selected route. In the next part, you’ll work through the services and Redux files to put the data-handling underpinnings in place that will support your UI components.