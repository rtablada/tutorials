Now that we've bootstrapped our Rolodex app, we can start setting up the application shell.
We'll start by adding some HTML and CSS that will provide the overall structure for the app.

As a reminder, here is the mockup for our app, the highlighted portions we'll be adding in this section of the guide

## Adding Markup

Let's start by creating the entire app in static HTML.
Then we'll begin replacing this static HTML with components and connect to our data source.

Let's add the following markup to `application.hbs`:

```handlebars {data-filename="app/templates/application.hbs"}
<div class="content">
  <nav>
    <input placeholder="search" />
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

  <section>
    <h1>Zoey</h1>

    <ul class="contact-items">
      <li>
        <div class="contact-item-type">
          phone
        </div>
        <div class="contact-item-value">
          555-1232
        </div>
      </li>
      <li>
        <div class="contact-item-type">
          email
        </div>
        <div class="contact-item-value">
          zoey@emberjs.com
        </div>
      </li>
      <li>
        <div class="contact-item-type">
          note
        </div>
        <div class="contact-item-value">
          Met at EmberConf!
        </div>
      </li>
    </ul>

    <button class="edit-button">
      Edit
    </button>
  </section>
</div>
```

And add the following styles to `app/styles/app.css`:

```css {data-filename="app/styles/app.css"}


```

Ember CLI is listening for file changes and will rebuild and reload the application when each file is saved.
We should now see the following in our browser.


Looks just like our mockup!
So far, we have the HTML for our application and we can tweak the HTML and CSS to get the base structure for our application.
Let's look deeper at what is happening with this HTML in our app.

#### What is the Application Template?

The application template, located at `/app/templates/application.hbs`, is the
entry point for every Ember.js application. This file is like the
`index.html` or `main` function for our app - it's the first thing the app
renders, and everything in our app comes from this template.
Other routes and components can be added, but they all eventually exist from this single `application.hbs` template file.

If you're familiar with HTML, you'll notice that the template doesn't include
things like the `<html>` tag, `<body>` tag, script tags, or style tags.
Ember CLI handles all of these details through its build system, providing a
zero-configuration project that allows you to focus on the application.
The `app/index.html` file can be edited to customize the HTML outside of the Ember application.

#### What is an `hbs` File?

The extension for Ember templates is `.hbs` instead of `.html`: this is because Ember uses Handlebars
syntax to describe templates.
Handlebars is a templating language on top of HTML.
_All_ valid HTML _is_ valid Handlebars, it is a superset of the language!
If you know HTML, you're already pretty familiar with Handlebars!
Handlebars adds the ability to add dynamic content using variables, components, and helpers.

Dynamic content in Handlebars templates is generally surrounded by double
curly braces (e.g. `{{foo}}`).
Ember Components can be created using angle brackets with capital case component names (e.g. `<ModalDialog></ModalDialog`).
So far our template doesn't have any dynamic content.
In the next step, we'll extract our first component, the `ContactList`.
