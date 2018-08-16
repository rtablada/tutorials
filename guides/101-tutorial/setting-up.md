This 101 tutorial covers the fundamentals of Ember.js, building up a contact
manager app from scratch! We'll be covering all of the core concepts and tools
necessary for you to start using Ember to build ambitious web apps, including:

* The CLI
* Components
* Services
* Routes
* Styles
* Tests

This tutorial is meant to build an Ember.js app progressively, starting
small and adding concepts over time. If you're new to Single Page App
development, this is the best guide to start with. If you've used Ember before and want a refresher,
check out the [Super Rentals Tutorial](../../tutorial/ember-cli) for something more fast paced.

## What You'll Build

Here's a mockup of the design we'll be shooting for as we build the Rolodex
application:

This app is meant to be allow users to manage and search through their contact
information quickly and easily. We'll want this app be able to:

* Show a list of contacts
* Search through contacts
* Show a detail view of the selected contact
* Add, edit, and delete contacts

We'll build out each of the these features step-by-step using Ember's core
concepts. If you get stuck at any point or would like to jump ahead to the
finished product, checkout [the github repo](https://github.com/ember-learn/ember-rolodex) for the source code for the project you will be building in this guide.
Each step of this guide is a single commit on the master branch of the repo,
allowing you to follow along or to debug errors or typos.

## Prerequisites

This guide is written with the assumption that you know some basic HTML, CSS,
and JavaScript. If you haven't ever used any of these before, you should
probably start with the following guides: (@todo: Add recommendations here)

You'll need to have Git, Node.js, and NPM installed on your computer for this
tutorial. Checkout the [Installing Ember](../../getting-started) guide for more
details.

You'll also need a modern web browser. We'll be making use of some of the latest
web APIs throughout this tutorial, like `window.fetch`. The latest version of Chrome,
Firefox, Microsoft Edge, or Safari should work just fine.

## Bootstrapping a New App

If you haven't already, install the latest version of Ember-CLI globally:

```sh
npm install -g ember-cli@latest
```

Ember-CLI is the Ember Command Line Interface. It's a tool that provides a ton of helpful 
features, like generators and blueprints for new applications. It also wraps the Ember
build system and development server which builds and packages your app for distribution.
While it is possible to set everything up on your own by including scripts or using other
build tools, its not recommended.

Once the CLI is installed, you can generate a new Ember application by running
`ember new` and providing an application name:

```sh
ember new ember-rolodex
```

A new project we be generated inside your current directory. Let's check it out:

```sh
cd ember-rolodex
```

## Directory Structure

The `new` command generates a project structure with the following files and
directories:

```text
├─ /app
├─ /config
├─ /node_modules
├─ /public
├─ /tests
├─ /vendor

<other files>

ember-cli-build.js
package.json
README.md
testem.js
```

Let's take a look at the folders and files Ember CLI generates.

**app**: This is where your app code is going to live. You'll see folders and
files here for things we'll be covering in this guide like:

```text
app
├─ /templates
├─ /components
├─ /services
├─ /routes
├─ /styles
```

as well as some files such as `index.html` and `app.js` which are necessary for
our app. There should also be some folders for other things that we _won't_ be
covering just yet:

```text
├─ /controllers
├─ /models
├─ /helpers
```

Ember CLI assumes that you're going to need these things eventually which is why
it adds these folders up front. If you feel like there's a lot going on in here,
it's because there is, and you can hide or remove the folders that we won't be
going over to clean things up and focus in for the time being. They're just
folders, and you can always add them back!

**config**: The config directory is the place for various types of
configurations, such as environment configs (development, testing, production,
etc) and deployment configs. We won't be diving into this area during the course
of this tutorial.

**node_modules**: This directory is created by `npm` and contains all of the dependencies
used to build serve and create your Ember application.
This includes Ember, Ember CLI, addons and more!


**package.json**: The `package.json` file is what defines the dependencies that `npm` installs in `node_modules`.
Packages here are usually defined in `dev-dependencies` since they are used locally to create your final Ember application

**public**: This directory contains assets such as images and fonts.

**vendor**: This directory is where third party dependencies (such as JavaScript
or CSS) that are not managed by NPM can be placed without mixing them with your `app` code.

**tests**: Automated tests for our app go in the `tests` folder, this includes unit tests, component test, acceptance tests, and more!

**testem.js**: The `testem.js` file is used to configure Ember CLI's test runner including what browsers are used for testing and more.

**ember-cli-build.js**: This file describes how Ember CLI should build our app.
We'll be using the default configuration for this tutorial, but if you ever need
to customize your apps build process, this will be one of the places to start.

## The Development Server

Once we have a new project in place, we can confirm everything is working by
starting the Ember development server:

```sh
ember serve
# or
ember s
# or
npm start
```

If we navigate to [`http://localhost:4200`](http://localhost:4200), we'll see the default welcome screen.

![default welcome screen](/images/ember-cli/default-welcome-page.png)

Let's look at where this UI is coming from.

Let's look at the contents of `app/templates/application.hbs`, here there is a some Handlebars code that should look like this;

```handlebars {data-filename="app/templates/application.hbs"}
{{!-- The following component displays Ember's default welcome message. --}}
{{welcome-page}}
{{!-- Feel free to remove this! --}}

{{outlet}}
```

The welcome page we saw when we loaded our app in the browser is being created by the statement `{{welcome-page}}`.
This Handlebars statement renders a `welcome-page` component provided by the `ember-welcome-page` package.
We'll return to components and addons later in this guide, for now we can remove this component and the comments that surround it.

There's also a Handlbars statement `{{outlet}}` at the bottom of this template file.
This outlet statement is used by the router to work with subroutes in our application.
We'll be returning to routes and `outlet`s later in this guide as well, for now let's remove the `{{outlet}}` statement.

Now we should have a completely blank canvas to build our application on!
Let's move to the next step and learn about Ember templates!
