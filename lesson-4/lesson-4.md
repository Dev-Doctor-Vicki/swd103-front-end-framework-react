# Deploy React App

Deploying your React apps is a vital step and this lesson will help you learn how to do that. You cover some hosting history as well as your options for deployment in modern hosting ecosystems.

Once your app is finished and ready, you’re going to want to deploy it to the world. In a large commercial organization, there will likely be multiple steps for this involving different environments, teams, and complex cloud hosting platforms, such as Amazon’s AWS or Microsoft's Azure.

These commercial environments can be both expensive to use and complex to set up and manage. In fact, it’s more than likely that if you end up working in a place using these environments, you’ll have entire teams dedicated to setting up hosting, running integration and deployment pipelines, and managing things from build through to deployment. They’re usually referred to as dev ops teams.

However, for single developers or small teams, static sites, or simple apps, you may just want to deploy your app to the world on your own hosting provider or deployment platform.

That’s what you’re going to do in this lesson. Taking our Furry Friends Gallery II, you will  build an optimized set of deployable code files and have them deployed to a public URL on the excellent Netlify cloud hosting platform.

First, explore some of the different terms, practices, and conventions you’ll come across when exploring deploying your apps to the world.

## Traditional Hosting Environments

In the early days of the web, there were a limited number of ways to host a website. More often than not, it involved one or a handful of linked servers in a physical location serving requests from browsers.

For example, in the early days of Google’s founding (approx. 1997), its first search services were provided by a set of servers slotted into a rack physically based in their company office.

Things moved considerably over the years but websites remained largely split into either simple, frontend sites consisting of `HTML` and `CSS` (maybe with a little JavaScript for basic interactivity) with CGI scripts to process payments or form submissions, to more complex backend sites involving PHP and server-side rendering.

A lot of hosting models were still based on physical hardware server space being sold with some redundancy built-in. They were relatively expensive, challenging to set up and maintain, and had a higher risk of failure.

### Cloud-based Hosting Providers

As new languages emerged to power a new breed of complex websites, hosting evolved into a more complex beast with a variety of options we see today. Depending on your needs, you can opt for virtual servers/machines (AKA VMs), managed hosting environments, shared hosting spaces, containerized deployments (e.g. Kubernetes or Docker) scalable instances, and even serverless hosting for things like Lambdas or serverless functions.

Cloud-based hosting providers offer access to huge computing power that can start as small as you need and seamlessly scale up and down if you have an app that suddenly has an influx of active users.

Still based on physical servers, this computing hardware is housed in massive datacentres located across multiple sites across the globe. Code and apps and websites are rarely ever kept in one physical place, but rather replicated across slices of physical hardware across multiple datacentres using complex management software and techniques.

This offers several benefits:

- Redundancy of your data; if any link in the chain suffers a fault, a duplicate/replicated link will take its place.

- Zero management hassle; all the ins and outs of the complex hosting infrastructure are taken off your hands and the management of the environments is taken care of so you can focus on deploying more features.

- Pay for what you need; depending on the host, you often only pay for what resources you use.

- Instant scaling; app going viral? Not to worry, your hosting can instantly spin up more resources to handle the incoming spike in traffic.

Some of the larger players in this cloud-based hosting space include Amazon AWS, Microsoft Azure, and Google Cloud. You can host just about anything you want on these providers.

The big three above offer a range of flexible, scalable hosting options and services within their platforms, but they can be overkill and work out fairly expensive if you’re trying to deploy a small website or simple app.

You have many more choices for hosting your apps and websites today, and you’ll explore some of those options in a moment.

### Single Page Applications (SPAs)

React and similar frameworks deal in the business of Single Page Applications or SPAs. SPAs are what the name implies: a single page that runs an entire application.

In a traditional website, you’ll have separate HTML pages for each route. So, `index.html` is a separate page, as is `about.html`. In server-side driven websites, such as WordPress, there is still a separate page generated and rendered for each route.

With SPAs, like the apps or sites you created with Create React App, you might remember you only have a single `index.html` page in the `/public directory`. That’s because you start from this entry point and our framework processes a ton of JavaScript behind the scenes to build  sections of HTML, injecting it into the starting page and managing any UI updates and changes as needed (this is what React does).

Typically, you won’t navigate away from this initial starting page, even though the UI may swap and change and replace whole portions of the screen.

Even when you inject some form of routing into an SPA (you’ll be doing this later in the course with React Router), so you can visit what looks like a different page (e.g. visit the `/about` route or `/customers`) you’re still not physically visiting a different web page. There’s no call out to a server that responds to your request by building a page, gathering data and generating an HTML file to return to the browser.

What’s actually happening is React (or a similar framework) is updating the required UI changes and managing the browser history in the background.

### The Jamstack (or JAMStack)

The Jamstack (you may see it written as JAMStack) is a phrase coined by Netlify themselves, who are arguably the original champions of this way of building modern interactive websites.

Jamstack stands for JavaScript APIs and Markup. It’s a particular way of building a website that deals with complex user interfaces and aggregating data from databases, server sources, APIs, or even template files (e.g. markdown or .md files) and squashing all these things together at build time to produce more simple, static sites that hark back to the early days of the web.

The advantage of these statically-generated sites is they can be hosted without any special requirements, are super performant, and can enjoy more security and benefit from heavy caching.

The sites and apps you’re building using Create React App are a form of Jamstack site in that they’re built into a set of `CSS`, `JS`, and `HTML` files.

When talking about the Jamstack in the context of React, you’re more likely to come across specific static site frameworks such as Next.js or Gatsby.

## Hosting Provider Options

In the next section, you will cover what happens when you want to build and deploy a statically generated site or SPA. You need to look for a host that can incorporate both the building and the deploying parts as one seamless process.

Any of these hosts offer a great blend of developer experience, ease of use and setup, fair pricing (usually free for small sites or apps), and modern deployment and hosting tooling.

### Netlify

You’re going to look at Netlify in greater depth in the coming lsections, but Netlify is a great choice and I’d recommend it. Although I’m not personally affiliated with them, I host several personal sites and projects on the Netlify platform and it’s always a great developer experience.

You can deploy from a command line or the simple, user-friendly GUI they offer. You can get started for free with a generous number of limits on various features, and all you need to do is plug your GitHub repository into the system and tell Netlify which build commands to run.

They also have some cool features such as automatic form recognition within your site, build-time plugins, web hooks, and more.

You can use custom domains and they’ll even secure your site for you with a free SSL certificate!

### Vercel

Vercel are the creators of React-based framework `Next.js`. They offer a very comparable set of services to Netlify and also offer a completely free level of hosting for smaller projects.

While they don’t quite have the same breadth of features as Netlify, where Vercel shines is in its handling of any `Next.js` project. Unsurprisingly, since they created `Next.js`, their hosting is geared a little more toward supporting `Next.js` projects and this is really where they perform best.

However, it is an excellent choice for any SPA or site you wish to host. It has custom integrations such as Google Lighthouse, which triggers a performance and optimization report on each build.

The process works in much the same way as Netlify: you hook Vercel into your GitHub repository and the platform will then monitor the code for changes or commits and rebuild and redeploy the site or app as needed.

### Heroku

Heroku is another popular choice for modern web apps and data-driven sites. Starting at free, Heroku offers a range of services to support modern app development.

The Heroku platform is more complex than the previous hosting options, but it does support containerized app deployment and additional services such as data storage providers (Postgres and Redis) and support for a range of backend languages such as Ruby, Node, PHP, and more.

It also has a free option with very minimal pricing to add extra services and features such as extended container hours and SSL certificates.

## Build an app to Deploy

React apps need to be built and packaged for deployment before you can think about sending them off to a hosting platform. This assignment covers how to build your React apps, ready for deployment.

### How to build your Projects

The process of building your applications and websites involves running a separate set of scripts, tools and, processes on your code before you send it to a hosting service.

If you look back at your most recent project from Lesson 3, and open the `package.json` file, you’ll see the following section:

```
"scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject"
  },
```

you’ve been using `npm start` for running your apps locally on some address like http://localhost:3000. But when it's time to build your code ready to deploy, it’s `npm build` you’ll need to run.

Give it a try! Open the terminal and type `npm build`  and hit enter.

You’ll see some brief messages in the console and then….nothing?

Take another look at your project directory. You should see a new folder, `/build`. Look in there and you’ll see a few new files and folders, especially in the `/js` folder where there are a number of `.js` files — this is your app!

This `/build` folder is the one you’ll want to deploy in the coming lessons as it holds the minified, optimized code that our hosting provider will be able to serve to your visitors.

## Continuous Deployment and Continuous Integration

Continuous deployment and integration is often shortened to CI/CD. Continuous deployment is a broad term that encapsulates the processes that happen when new code is committed to a codebase.

Broadly speaking, it describes an often automated environment that flows like this:

1. Code is checked into one or more versioned branches being watched by the CI/CD environment for changes.

2. The change is noticed by the CI/CD environment.

3. Automated tests are usually run next (unit tests or integration tests).

4. On a successful test run, a new build is started.

5. Whatever build commands you have are run.

6. On a successful build, the built, processed, packaged code is collected and deployed to one or more hosting environments.

In the next section, you'll sett up a simple CI/CD environment on Netlify to host your Furry Friends Gallery. In reality, CI/CD environments can be very complex beasts and are often managed by dedicated teams broadly grouped under the name DevOps.

It’s not imperative that you have a detailed knowledge of how to build and deploy your apps and sites at this level, but even having a basic grasp of the concepts and some experience with the processes involved will make it easier for you to communicate with dev ops teams in your workplace.

Now that you’ve got an idea of the concepts involved in building and deploying your apps and websites, start deploying your very own project.

## Set Up Deployment on Netlify

You'll walk through the deployment process to host your Furry Friends Gallery Mark II app on the Netlify hosting platform.

From the previous lessons you’ve discovered that you need a suitable hosting platform to serve your built and processed apps, websites, and SPAs.

Because the code you’re likely to produce with React is dependent on some kind of build process to get from development code to production-ready output, it’s useful to choose a hosting provider that can handle this building process for you. You could, of course, just run build commands manually and then deal with the output yourself, but this is a tedious, unnecessary, and very error prone-process.

In modern development environments, it’s much more common to deal with some sort of predictable build and deployment pipeline where tests are run, code is built, bundled and packaged for deployment, and physically deployed somewhere for public consumption.

You've been briefly introduced to Netlify as an option for hosting your React apps and sites, but you’ll look in more depth here. Sign up for a new account, attach Netlify to our GitHub repositories, set up a build and deployment project for the Furry Friends Gallery Mark II, and finally, deploy your app to the world.

### Step One - Create a New Account on Netlify

This is the easy part. Head to [Netlify](https://www.netlify.com/) and you’ll be greeted with the company’s landing page. Look for the ‘Sign up ->’ button on the top right-hand side of the screen and click it.

You’ll be taken to the signup screen, which will look like this:

[](lesson-4.md)

Sign up with your GitHub account. You’ll see  Netlify offers a range of options, including a simple email/password combination.

You need to be logged in with GitHub, as it is arguably the most common. However, even if you choose another provider, the meat and potatoes of the lesson here will work in the same way. The main differences will be around signin, choosing your repository and any special GitHub commands.

### Step Two - Create a New Project for Furry Friends Gallery

If you choose to sign up using GitHub or one of the other code versioning platforms, you’ll be whisked off to their specific signin screens to authenticate and then authorize Netlify to be able to read your repository information.

Once authorized, you’ll be taken to the Netlify dashboard. Here’s mine:

![](assets/netlify-dashboard.png)

On the dashboard, you have easy access to high-level account settings, such as domains, builds, billing, and any team members you have.

You see on my dashboard a list of my Netlify-hosted projects.

If I were to click on one of these, I’ll be taken to a sub-dashboard with specific information related to this site, such as deploys, previews, and settings. We’ll look at this screen in more depth shortly, but for now, we’re going to add a new project!

Hit the green ‘Add New Site’ button at the top right of the dashboard and you’ll be taken through the new site wizard.

### Connect to a Git provider

The first step in the wizard is to choose the source for the website or app.

