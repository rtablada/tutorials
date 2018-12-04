Now that we've bootstrapped our Rolodex app, we can start setting up the application shell.
We'll start by adding some HTML and CSS that will provide the overall structure for the app.

As a reminder, here is the mockup for our app, the highlighted portions we'll be adding in this section of the guide

## Adding Markup

Let's start by creating the entire app in static HTML.
Then we'll begin replacing this static HTML with components and connect to our data source.

Let's add the following markup to `application.hbs`:

```handlebars {data-filename="app/templates/application.hbs"}
<div class="container">
  <nav>
    <ul>
      <li>
        <a class="active">
          <h4>Zoey</h4>
          <p>zoey@emberjs.com</p>
        </a>
      </li>
      <li>
        <a>
          <h4>Tomster</h4>
          <p>tomster@emberjs.com</p>
        </a>
      </li>
    </ul>
  </nav>
</div>
```

And add the following styles to `app/styles/app.css`:

```css {data-filename="app/styles/app.css"}


```

Ember CLI is listening for file changes and will rebuild and reload the application when each file is saved.
With the new HTML and CSS saved, we should now see the following in our browser.

![InsertImageHere]()

Looks just like our mockup!
So far, we have some starting HTML for our application.
Let's look deeper at what is happening with this HTML in our app.

#### What is the Application Template?

The application template, located at `/app/templates/application.hbs`,
and this file is the entry point for every Ember.js application.
This file is like the `main` function for our app - it's the first thing the Ember renders, and everything in our app comes from this template.
Other routes and components can be added, but they all eventually live inside the `application.hbs` template file.

Notice that this template does not include an `<html>` tag, `<body>` tag, script tags, or style tags.
Ember CLI handles all of these details through its build system, providing a
zero-configuration project that allows us to focus on our application logic.
Some of these features can be edited in the `app/index.html` file to change the HTML outside of the runing Ember application.

#### What is an `hbs` File?

The extension for Ember templates is `.hbs` instead of `.html`:
this is because Ember uses Handlebars syntax to describe templates.
Handlebars is a templating language built on top of HTML.
Handlebars adds the ability to add dynamic content using variables, components, and helpers to regular HTML.

_All_ valid HTML _is_ valid Handlebars!
So, any existing knowledge of HTML just works in Handlebars!

So far our template doesn't have any dynamic content.
In the next step, we'll extract our first component, the `ContactItem`.
