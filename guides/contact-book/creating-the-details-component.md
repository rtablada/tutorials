Now that we have our HTML and styles all figured out, we'll create our first
component - the `ContactDetails` component. This component will represent the
main detail view on the right hand side side of our application, and should show
our users all of the details of the selected contact. But first, let's talk
about what components are, and how we can use them to build applications.

## What is a Component?

Components are Ember's most fundamental construct. They are reusable,
self-contained elements that have a template and optionally a backing class
with any logic that is part of the component's functionality. You can think of
components as extensions to HTML - they allow us to create our own custom tags
like `<div>`, `<input>`, or `<select>`, with custom layouts, styles, and
behaviors.

You can tell whether or not something is a component by looking at it in your
template. Components are invoked using capital-case notation in angle brackets:

```handlebars
<!-- standard HTML div -->
<div>

  <!-- standard HTML input -->
  <input type="checkbox">

  <!-- component -->
  <CustomInput/>

  <!-- block style component -->
  <CustomButton>
    Save
  </CustomButton>

</div>
```

Components allow us to break down our application into reusable and composeable
parts, so we're not repeating ourselves constantly. They give us a way to
encapsulate behavior and keep our code neat and organized.

## Generating a Component

We'll start by using Ember-CLI to generate a basic component for us. Run the
generate command with `component` to get started:

```sh
ember generate component contact-details
```

or, for short:

```sh
ember g component contact-details
```

Ember-CLI should create three files:

```sh
installing component
  create app/components/contact-details.js
  create app/templates/components/contact-details.hbs
installing component-test
  create tests/integration/components/contact-details-test.js
```

The first two files are the template and the backing class for our component,
and the last file is a basic test file. We'll return to testing later on, for
the moment lets focus on the component.

## Adding a Template

Open up the component's template file at `app/templates/components/contact-list`.
You should see one line only with `{{yield}}` in it. This is how components can
use block form, which is when you pass a template to a component upon invocation
(e.g. `<CustomButton>Foo</CustomButton>`). We'll return to this later, for now
remove the `{{yield}}` helper and copy the markup for the contact details
section from the application template:

```handlebars {data-filename="app/templates/components/contact-details.hbs" data-diff="-1,+2,+3,+4,+5,+6,+7,+8,+9,+10,+11,+12,+13,+14,+15,+16,+17,+18,+19,+20,+21,+22,+23,+24,+25,+26,+27,+28,+29,+30,+31,+32,+33,+34,+35"}
{{yield}}
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
```

And then remove the markup from the application template and replace it with the
`<ContactDetails>` component:

```handlebars {data-filename="app/templates/application.hbs" data-diff="+20,-21,-22,-23,-24,-25,-26,-27,-28,-29,-30,-31,-32,-33,-34,-35,-36,-37,-38,-39,-40,-41,-42,-43,-44,-45,-46,-47,-48,-49,-50,-51,-52,-53,-54"}
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

  <ContactDetails/>
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

Save your changes, and you should see the application rebuild and reload.
Everything should appear the exact same as before, and if you inspect the DOM
you'll find that our HTML has remained unchanged:

This is because Ember is rendering the `ContactDetails` component in the
application template, which puts _its_ template wherever we invoked it. Because
the `ContactDetails` component only has a template right now, we don't see anything
dynamic. It's just the HTML being inserted directly.

Let's change that!

First, lets add some data to our component. We'll add a javascript
object that represents the selected contact. Open up the component file at
`app/components/contact-details.js` and add a contact field to the class:

```js {data-filename="app/components/contact-details.js" data-diff="+4,+5,+6,+7,+8,+9"}
import Component from '@ember/component';

export default class ContactDetails extends Component {
  contact = {
    name: 'Zoey',
    email: 'zoey@emberjs.com',
    phone: '555-1232',
    note: 'Met at EmberConf!',
  };
}
```

Now, back in the template, we can replace the static text values we had as
placeholders with _bindings_ to the actual values on our contact object.

### What's a Binding?

Bindings are a way for us to directly reference a value in our Javascript code
from a component's template. Binding a value in the template tells Ember to
lookup the value when rendering the component, and to rerender if the value
changes later on.

Handlebars allows us to bind values using double-curly-bracket syntax. For
instance, we can reference the `contact` object on the component as
`{{this.contact}}`. However, if we were to add this to our template, it would
end up rendering `[Object object]` as the text, because it calls `toString` on
the object.

Bindings can be applied to any value that is in the scope of the template,
similarly to standard Javascript scoping. By default, the component instance is
available in scope as `this`, so we can update our template to bind the various
values available on our `contact` object by binding their paths:

```handlebars {data-filename="app/templates/components/contact-details.hbs" data-diff="-2,+3,-11,+12,-20,+21,-29,+30"}
<section>
  <h1>Zoey</h1>
  <h1>{{this.contact.name}}</h1>

  <ul class="contact-items">
    <li>
      <div class="contact-item-type">
        phone
      </div>
      <div class="contact-item-value">
        555-1232
        {{this.contact.phone}}
      </div>
    </li>
    <li>
      <div class="contact-item-type">
        email
      </div>
      <div class="contact-item-value">
        zoey@emberjs.com
        {{this.contact.email}}
      </div>
    </li>
    <li>
      <div class="contact-item-type">
        note
      </div>
      <div class="contact-item-value">
        Met at EmberConf!
        {{this.contact.note}}
      </div>
    </li>
  </ul>

  <button class="edit-button">
    Edit
  </button>
</section>
```

If you save and refresh, everything should appear to be the same as before,
except now our component is rendering values from its instance. We'll come back
to the "Edit" button later on in the tutorial, first we need to make our details
component reusable.

Currently, there's no way for us to select a different contact and show it to
the user. The details component will always render the details for Zoey, but we
want to render the details for whatever contact has been actively selected. To
do that, we'll first make our `<ContactDetails>` component receive the `contact`
value as an _argument_, so it can be passed the value from an external context.
We'll then add a _container component_, whose sole purpose will be to manage the
state of and communication between our `<ContactDetails>` component, and the
contact list component we'll add later on.

## Component Arguments and Attributes

Components can receive two different kinds of values when they are invoked,
arguments and attributes:

* **Arguments** are values that are passed to the component instance. These can
be any kind of value - strings, numbers, booleans, objects, etc. They are passed
in and the component can use them directly and bind them in its template. They
are prefixed with `@` when passed into the component, and can be accessed using
`@` in the component's template.

* **Attributes** are standard HTML attributes, and they are rendered directly
in the component via the `...attributes` syntax. We'll learn more about this
later, but in general attributes are meant to work just like standard HTML
attributes such as `id`, `class`, `role`, aria attributes, data attributes, and
more.

You can see the two in action with the standard `<Input>` component that ships
with Ember:

```
<Input placeholder="Name" @value={{name}} class="name-field {{fieldClass}}" />
```

As you can see, values like `placeholder` and `class` are attributes because
they are not prefixed with `@`. These values are rendered directly on the
`input` element that is created by this component, but `@value` is not -
instead, it is managed by the component internally. The `Input` component
provides extra functionality with this argument, like two-way data binding which
causes whatever value we bind to it to update as the user types in the input (in
this case, the `{{name}}` value).

Let's convert the `contact` field on our details component to be an argument.
First, we'll change all references in the template to point to an argument named
`@contact`:

```handlebars {data-filename="app/templates/components/contact-details.hbs" data-diff="-2,+3,-11,+12,-20,+21,-29,+30"}
<section>
  <h1>{{this.contact.name}}</h1>
  <h1>{{@contact.name}}</h1>

  <ul class="contact-items">
    <li>
      <div class="contact-item-type">
        phone
      </div>
      <div class="contact-item-value">
        {{this.contact.phone}}
        {{@contact.phone}}
      </div>
    </li>
    <li>
      <div class="contact-item-type">
        email
      </div>
      <div class="contact-item-value">
        {{this.contact.email}}
        {{@contact.email}}
      </div>
    </li>
    <li>
      <div class="contact-item-type">
        note
      </div>
      <div class="contact-item-value">
        {{this.contact.note}}
        {{@contact.note}}
      </div>
    </li>
  </ul>

  <button class="edit-button">
    Edit
  </button>
</section>
```

Then we'll remove the `contact` object from our component, since it's no longer
necessary:

```js {data-filename="app/components/contact-details.js" data-diff="-4,-5,-6,-7,-8,-9"}
import Component from '@ember/component';

export default class ContactDetails extends Component {
  contact = {
    name: 'Zoey',
    email: 'zoey@emberjs.com',
    phone: '555-1232',
    note: 'Met at EmberConf!',
  };
}
```

That's all there is to it! However, if you save and refresh, you'll see that
all of the places where the contact details were are now blank:

[image]

This is because we aren't passing a contact into the details component yet. In
order to do that, we now need to create our container component.
