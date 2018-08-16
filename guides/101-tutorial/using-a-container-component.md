
One of the common patterns used when designing components is to separate
concerns between _presentational components_ (which focus on the UI) and
_container components_ (which focus on the business logic and glue code of the
app). Separating concerns like this makes it easier to reuse the UI components
throughout your application, and consolidates complicated code, making it easier
to understand.

Here's a quick breakdown of the differences between presentational and container
components (lifted with some alterations from [Dan Abramov's excellent blogpost](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0)
on the topic).

Presentational components:

* Are concerned with how things look.
* May contain both presentational and container components inside, and usually
  have some DOM markup and styles of their own.
* Often allow composeability via `yield`s
* Have no dependencies on the rest of the app, such as the Ember Data store or
  other global services.
* Don’t specify how the data is loaded or mutated.
* Receive data and actions exclusively via arguments.
* Rarely have their own state (when they do, it’s UI state rather than data).
* Are written as template-only components unless they need state, computed
  properties, lifecycle hooks, or performance optimizations.
* Examples: `Page`, `Sidebar`, `Story`, `UserInfo`, `List`.

Container components:

* Are concerned with how things work.
* May contain both presentational and container components** inside but usually
  don’t have any DOM markup of their own except for some wrapping divs, and
  never have any styles.
* Provide the data and behavior to presentational or other container components.
* Interact with long-lived state, such as models in the Ember Data store or
  other services.
* Are often stateful, as they tend to serve as data sources.
* Examples: `UserPage`, `FollowersSidebar`, `StoryContainer`, `FollowedUserList`.

Some of these points may not be very clear yet - we haven't talked about
long-lived state or services, but we'll circle back around to that later on in
the tutorial. The main thing to remember here is that you can have components
which only consist of other components and the glue-code that holds them
together, and you can use this to organize your code more neatly. This is a good
pattern to use, but not something that has to be followed all the time.

We'll use this pattern to create a container component that holds user
definitions and the currently selected user, and passes that user into the
`ContactDetails` component.

## Creating the Contacts Container

Generate the component using ember-cli like before:

```sh
ember g component contacts-container
```

Then open up the component's template file at
`app/templates/components/contacts-container.hbs` and copy the contact list and
contact details component from the application template over:

```handlebars {data-filename="app/templates/components/contacts-container.hbs" data-diff="-1,+2,+3,+4,+5,+6,+7,+8,+9,+10,+11,+12,+13,+14,+15,+16,+17,+18,+19,+20"}
{{yield}}
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
```

And replace it with the single `ContactsContainer` component:

```handlebars {data-filename="app/templates/application.hbs" data-diff="-2,-3,-4,-5,-6,-7,-8,-9,-10,-11,-12,-13,-14,-15,-16,-17,-18,-19,-20,+21"}
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
  <ContactsContainer/>
</div>
```

Now we need to add some data to our container. We'll start by adding an array of
all of our users, and we'll define a `selectedUser` field that we'll pass into
our details component:

```js {data-filename="app/components/contacts-container.js" data-diff="+24,+25"}
import Component from '@ember/component';

export default class ContactsContainer extends Component {
  contacts = [
    {
      name: 'Zoey',
      email: 'zoey@emberjs.com',
      phone: '555-1232',
      note: 'Met at EmberConf!',
    },
    {
      name: 'Tomster',
      email: 'tomster@emberjs.com',
      phone: '555-1532',
      note: 'Met at EmberCamp!',
    },
    {
      name: 'Bob',
      email: 'bob@emberjs.com',
      phone: '555-6789',
      note: 'Met at EmberFest!',
    },
  ];

  selectedContact = null;
}
```
```handlebars {data-filename="app/templates/application.hbs" data-diff="-19,+20"}
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
<ContactDetails @contact={{selectedContact}} />
```

Note that we are assigning the value of `selectedContact` here to `null` instead
of creating a new contact object, and our contact fields are all still blank
because of that. We're doing this because the contacts array is supposed to
represent _all_ contacts in our app, so we can't create new contact objects that
aren't part of it.

Later on, when the user clicks on one of the contacts in the list we will assign
the value of that contact to `selectedContact`. But before the user has clicked
anything at all it usually makes sense to show a prompt to let the user know
nothing is selected. Let's add that now, using the `{{if}}` helper.

## Handlebars Helpers

Helpers are another part of the Handlebars templating language. They are like
components, but usually much simpler, and invoked using double-curly-bracket
notation like bindings. Helpers are usually pure functions, and provide much of
the logic that can occur in templates. Some examples include the `{{if}}`,
`{{each}}`, and `{{concat}}` helpers:

```handlebars
{{#if value}}
  The value is truthy
{{else}}
  The value is falsey
{{/if}}

<ul>
  {{#each items as |item|}}
    <li>{{item}}</li>
  {{/each}}
</ul>

<button class="{{concat 'btn-' buttonType}}"></button>
```

Some helpers, such as `{{if}}` and `{{each}}`, can have blocks passed to them.
Others, like `{{concat}}`, only take parameters and return a value. It's also
possible to nest handlebars helpers using parentheses within a helper
invocation:

```hanblebars
<button class="{{if buttonType (concat 'btn-' buttonType)}}"></button>
```

And as you can see from that example, some helpers such as `{{if}}` can also be
used inline as well as in block form.

Now let's add an `{{if}}` to the container component to show a placeholder
message when the selected contact doesn't exist:

```handlebars {data-filename="app/templates/application.hbs" data-diff="+19,+21"}
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

{{#if selectedContact}}
  <ContactDetails @contact={{selectedContact}} />
{{else}}
  Select a Contact
{{/if}}
```

Excellent, now all of our state (the contacts and selected contact) live in our
container component. Next up, we need to build out the `ContactList` component
so that users can search for contacts and select which one to display.
