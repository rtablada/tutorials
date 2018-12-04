Now that we have a way to navigate our contacts, let's extract the detail view
to a component as well. We'll start by pulling out the component from our base
HTML, like the `ContactList` component. Then, we'll create a container component
to connect the `ContactList` to the details section, and allow users to navigate
and control it. Finally, we'll add the ability to edit a contact to the details
component.

## Extracting the Details Component

First, generate the component like before:

```sh
ember g component contact-details
```

Then open up the component's template file at
`app/templates/components/contact-details.hbs` and copy the markup for the
details section from the application template:

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

And replace that section in the application template with the component:

```handlebars {data-filename="app/templates/application.hbs" data-diff="+3,-4,-5,-6,-7,-8,-9,-10,-11,-12,-13,-14,-15,-16,-17,-18,-19,-20,-21,-22,-23,-24,-25,-26,-27,-28,-29,-30,-31,-32,-33,-34,-35,-36,-37,-38,"}
<div class="container">
  <ContactList/>
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

Great, now we have the framework for our details component set up. Next, we need
to make the component's field items dynamic. Like before, we'll add a mock
contact object to the component instance, and we'll use that contact in our
template:

```js {data-filename="app/components/contact-list.js" data-diff="+4,+5,+6,+7,+8,+9"}
import Component from '@ember/component';

export default class ContactList extends Component {
  contact = {
    name: 'Zoey',
    email: 'zoey@emberjs.com',
    phone: '555-1232',
    note: 'Met at EmberConf!',
  };
}
```
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
to the "Edit" button later on, first let's focus on hooking up the
`ContactDetails` component to the `ContactList` we made earlier so that users
can select the contact that they want to view the details of. In order to do
that, we'll create a "container" component that facilitates the communication
between the two "presentational" components we've already made.

## Container and Presentational Components

One of the common patterns used when designing components is to separate
concerns between presentational components (which focus on the UI) and container
components (which focus on the business logic and glue code of the app).
Separating concerns like this makes it easier to reuse the UI components
throughout your application, and consolidates complicated code, making it easier
to understand.

Here's a quick breakdown of the differences between presentational and container
components (lifted with some alterations [Dan Abramov's excellent blogpost](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0)
on the topic).

Presentational components:

* Are concerned with how things look.
* May contain both presentational and container components** inside, and usually
  have some DOM markup and styles of their own.
* Often allow composeability via `yield`s
* Have no dependencies on the rest of the app, such as the Ember Data store or
  other global services.
* Don’t specify how the data is loaded or mutated.
* Receive data and actions exclusively via arguments.
* Rarely have their own state (when they do, it’s UI state rather than data).
* Are written as template-only components unless they need state, computed
  properties, lifecycle hooks, or performance optimizations.
* Examples: Page, Sidebar, Story, UserInfo, List.

Container components:

* Are concerned with how things work.
* May contain both presentational and container components** inside but usually
  don’t have any DOM markup of their own except for some wrapping divs, and
  never have any styles.
* Provide the data and behavior to presentational or other container components.
* Interact with long-lived state, such as models in the Ember Data store or
  other services.
* Are often stateful, as they tend to serve as data sources.
* Examples: UserPage, FollowersSidebar, StoryContainer, FollowedUserList.

Some of these points may not be very clear yet - we haven't talked about
long-lived state or services, but we'll circle back around to that later on in
the tutorial. The main thing to remember here is that you can have components
which only consist of other components and the glue-code that holds them
together, and you can use this to organize your code more neatly. This is a good
pattern to use, but not something that has to be followed all the time.

We'll use this pattern to create a container component that holds the shared
user definitions, and the currently selected user if there is one.

## Creating the Contacts Container

Generate the component using ember-cli like before:

```sh
ember g component contacts-container
```

Then open up the component's template file at
`app/templates/components/contacts-container.hbs` and copy everything from the
application template over:

```handlebars {data-filename="app/templates/components/contacts-container.hbs" data-diff="-1,+2,+3,+4,+5"}
{{yield}}
<div class="container">
  <ContactList/>
  <ContactDetails/>
</div>
```

And replace it with the single `ContactsContainer` component:

```handlebars {data-filename="app/templates/application.hbs" data-diff="-1,-2,-3,-4,+5"}
<div class="container">
  <ContactList/>
  <ContactDetails/>
</div>
<ContactContainer/>
```

Now we need to extract the mock data we added to the `ContactList` and
`ContactDetails` components previously to our new container component, and pass
it down as an argument instead. We'll start with the `ContactList` component.

First, copy the `contacts` field from `ContactList` over to `ContactDetails`.
We'll also add some of the missing fields from our details view, such as phone
number and note:

```js {data-filename="app/components/contact-list.js" data-diff="-5,-6,-7,-8,-9,-10,-11,-12,-13,-14,-15,-16,-17,-18,+19,+20"}
import Component from '@ember/component';
import { computed } from '@ember-decorators/object';

export default class ContactList extends Component {
  contacts = [
    {
      name: 'Zoey',
      email: 'zoey@emberjs.com',
    },
    {
      name: 'Tomster',
      email: 'tomster@emberjs.com',
    },
    {
      name: 'Bob',
      email: 'bob@emberjs.com',
    },
  ];
  /****** Arguments ******/
  contacts = null;

  @computed('contacts', 'searchValue')
  get filteredContacts() {
    // Turn the search string into a case-insensitive,
    // regular expression so we can match without
    // worrying about case
    let searchRegex = new RegExp(this.searchValue, 'i');

    return this.contacts.filter(contact => {
      return searchRegex.test(contact.name)
        || searchRegex.test(contact.email);
    });
  }
}
```
```js {data-filename="app/components/contacts-container.js" data-diff="+4,+5,+6,+7,+8,+9,+10,+11,+12,+13,+14,+15,+16,+17,+18,+19,+20,+21,+22,+23"}
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
}
```

Notice that we didn't completely remove the `contacts` field from `ContactList`,
and instead we assign it to `null`. This is not strictly necessary, but it's
good practice to list all arguments that your component expects to receive, both
for performance reasons and to keep the component's code self-documenting so
you always know what arguments can be passed into the component.

Next, we'll pass the `contacts` field into the `ContactList` as an argument:

```handlebars {data-filename="app/templates/components/contacts-container.hbs" data-diff="-2,+3"}
<div class="container">
  <ContactList/>
  <ContactList @contacts={{this.contacts}}/>
  <ContactDetails/>
</div>
```

We pass the contacts in as an argument because we want the contact list to be
able to use them internally, and we don't want it to attempt to apply them as an
HTML attribute. Arguments are assigned to the class as fields, so our current
`ContactList` component code does not need to change at all. If you save, the
app should function the same as before, only now our data lives in the
`ContactsContainer` component.

Now, let's do the same thing for the details component. We'll remove the mock
contact from the details component, and we'll add a class field to the container
to represent the details. This time, however, we'll set that field to `null` as
well, to represent the initial state of the application before the user has
selected a contact to view:

```js {data-filename="app/components/contact-list.js" data-diff="-4,-5,-6,-7,-8,-9,+10,+11"}
import Component from '@ember/component';

export default class ContactList extends Component {
  contact = {
    name: 'Zoey',
    email: 'zoey@emberjs.com',
    phone: '555-1232',
    note: 'Met at EmberConf!',
  };
  /****** Arguments ******/
  contact = null;
}
```
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
```handlebars {data-filename="app/templates/components/contacts-container.hbs" data-diff="-3,+4"}
<div class="container">
  <ContactList @contacts={{this.contacts}}/>
  <ContactDetails/>
  <ContactDetails @contact={{this.selectedContact}}/>
</div>
```

We need to make some changes to our template as well now. First off, arguments
can be referred to directly in templates using the `@` sigil. Since contact is
now an argument, and we use it directly in the template, we can replace existing
references to `this.contact` with `@contact`:

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
        {{@contact.note}}
      </div>
    </li>
  </ul>

  <button class="edit-button">
    Edit
  </button>
</section>
```

This style provides extra clarity around what we're doing with this component.
You can glance at the template and tell that the component takes a `@contact`
argument, which can be very helpful when reading a component for the first time.

Second, we need to account for the fact that `@contact` can be empty now. Let's
add an `{{if}}` statement that shows an empty details panel if the contact
doesn't exist:

```handlebars {data-filename="app/templates/components/contact-details.hbs" data-diff="+2,+35,+36,+37"}
<section>
  {{#if @contact}}
    <h1>{{@contact.name}}</h1>

    <ul class="contact-items">
      <li>
        <div class="contact-item-type">
          phone
        </div>
        <div class="contact-item-value">
          {{@contact.phone}}
        </div>
      </li>
      <li>
        <div class="contact-item-type">
          email
        </div>
        <div class="contact-item-value">
          {{@contact.email}}
        </div>
      </li>
      <li>
        <div class="contact-item-type">
          note
        </div>
        <div class="contact-item-value">
          {{@contact.note}}
        </div>
      </li>
    </ul>

    <button class="edit-button">
      Edit
    </button>
  {{else}}
    <div>Select a Contact</div>
  {{/if}}
</section>
```

Excellent, now all of our state (the contacts) lives in our container component.
Next up, we need to make navigation work so that users can select the contact
to display.

## Adding a Select Action

We've talked about how we can use arguments to pass data to child components.
They can also be used to send callback functions to children, so children can
communicate upwards with their parents when things change or the user interacts
with them. These callback functions are called "actions" in Ember, and we use
the `{{action}}` helper to create them.

Let's use actions to send a newly selected contact whenever the user clicks on
a contact in the `ContactList`. First, we'll add a function to the component
class:

```js {data-filename="app/components/contact-list.js" data-diff="-5,-6,-7,-8,-9,-10,-11,-12,-13,-14,-15,-16,-17,-18"}
import Component from '@ember/component';
import { computed } from '@ember-decorators/object';
import { action, computed } from '@ember-decorators/object';

export default class ContactList extends Component {
  /****** Arguments ******/
  contacts = null;
  onContactSelected = null;

  @computed('contacts', 'searchValue')
  get filteredContacts() {
    // Turn the search string into a case-insensitive,
    // regular expression so we can match without
    // worrying about case
    let searchRegex = new RegExp(this.searchValue, 'i');

    return this.contacts.filter(contact => {
      return searchRegex.test(contact.name)
        || searchRegex.test(contact.email);
    });
  }

  @action
  contactSelected(contact) {
    if (this.onContactSelected) {
      this.onContactSelected(contact);
    }
  }
}
```

Note that the function here is marked with the `@action` decorator. This tells
Ember that we intend to use it as an action in our template. We also added a new
argument, the `onContactSelected` argument which we expect to be a function. We
do a check to make sure it exists, and if it does, we call it with the selected
value.

Now, let's hook up this function in our template. Since this is child-most
component, we won't be passing our action down into another component. Instead,
well use it as the click handler on our links:

```handlebars {data-filename="app/templates/components/contact-list.hbs" data-diff="-4,+5"}
<nav>
  <Input placeholder="search" @value={{searchValue}} />
  <ul>
    {{#each this.filteredContacts as |contact|}}
      <li>
        <a>
        <a onclick={{action this.contactSelected contact}}>
          <h4>{{contact.name}}</h4>
          <p>{{contact.email}}</p>
        </a>
      </li>
    {{/each}}
  </ul>
</nav>
```

As you can see, we pass both the `contactSelected` action and the current
`contact` from the `each` loop into the `{{action}}` helper. This binds the
`contact` value to the function, so when it is called it passes it as the first
parameter. The `onclick` attribute of the link will call the function using
standard browser APIs - nothing special from Ember.

Now, let's create another action in the `ContactsContainer` component to pass
to the `ContactList` so it can set the `selectedContact` field.

```js {data-filename="app/components/contacts-container.js" data-diff="+26,+27,+28,+29,+30"}
import Component from '@ember/component';
import { action } from '@ember-decorators/object';

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

  @action
  setSelectedContact(contact) {
    this.set('selectedContact', contact);
  }
}
```

One interesting thing to note here is that we use `this.set` to set the selected
contact. `set` is a special function that notifies Ember when something is
changing, letting it know that it may need to rerender. It also invalidates
computed properties so they know to recalculate. Ember will generally let you
know if you should be using `set` to set a field via a special dev-only
assertion, so you won't accidentally assign a value using standard JavaScript
assignment (e.g. `this.selectedContact = contact`).

Now in our template, we can update the `ContactList` component to take this new
action:

```handlebars {data-filename="app/templates/components/contacts-container.hbs" data-diff="-2,+3,+4,+5,+6"}
<div class="container">
  <ContactList @contacts={{this.contacts}}/>
  <ContactList
    @contacts={{this.contacts}}
    @onContactSelected={{action this.setSelectedContact}}
  />
  <ContactDetails @contact={{this.selectedContact}}/>
</div>
```

You should now be able to click on a contact and see the details panel update!
Note that we didn't pass any additional information to the action helper this
time, we only needed to bind the `setSelectedContact` function. The
`ContactList` component will pass the selected contact to the function when it
is called.

As you can imagine, updating a value like this is a pretty common workflow.
Writing an action that sets a particular value is a fairly large amount of
boilerplate, and Ember provides a special helper that allows us to cut down on
this extra code - the `mut` helper. Let's replace the `setSelectedContact`
action with `mut`:

```js {data-filename="app/components/contacts-container.js" data-diff="-26,-27,-28,-29,-30"}
import Component from '@ember/component';
import { action } from '@ember-decorators/object';

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

  @action
  setSelectedContact(contact) {
    this.set('selectedContact', contact);
  }
}
```
```handlebars {data-filename="app/templates/components/contacts-container.hbs" data-diff="-2,+3,+4,+5,+6"}
<div class="container">
  <ContactList @contacts={{this.contacts}}/>
  <ContactList
    @contacts={{this.contacts}}
    @onContactSelected={{action (mut this.selectedContact)}}
  />
  <ContactDetails @contact={{this.selectedContact}}/>
</div>
```

If you save again, everything should work exactly as before.

You may have noticed that we use are using `mut` in the `action` helper here.
This is possible because helpers are composeable - you can pass the output of
one helper as the input to the next, just like a function call. The only
difference is that when using nested helpers like above, only the first level
is invoked using double curly brackets. All subsequent levels are invoked with
parenthesis instead.

## Adding Active State

In the last section, when we converted our plain HTML into the `ContactList`
component, we also lost the `active` class that was applied to one of the
contacts. Now that we have selection logic all setup, we can add it back! First,
lets add `@selectedContact` as an argument to `ContactList`, so we can tell it
which contact to highlight:

```js {data-filename="app/components/contact-list.js" data-diff="-5,-6,-7,-8,-9,-10,-11,-12,-13,-14,-15,-16,-17,-18"}
export default class ContactList extends Component {
  /****** Arguments ******/
  contacts = null;
  onContactSelected = null;

  @computed('contacts', 'searchValue')
  get filteredContacts() {
    // Turn the search string into a case-insensitive,
    // regular expression so we can match without
    // worrying about case
    let searchRegex = new RegExp(this.searchValue, 'i');

    return this.contacts.filter(contact => {
      return searchRegex.test(contact.name)
        || searchRegex.test(contact.email);
    });
  }

  @action
  contactSelected(contact) {
    if (this.onContactSelected) {
      this.onContactSelected(contact);
    }
  }
}
```
