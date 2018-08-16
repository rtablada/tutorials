Now that we have the details and container components setup, let's make a list
component that our users can use to search for contacts and select one to view.
We'll begin by extracting the component from our HTML like we did before, and
then we'll hook it up to receive our `contacts` array as an argument and
communicate the user's selection to our details component.

## Extracting the ContactList Component

First, generate the component like before:

```sh
ember g component contact-list
```

Then open up the component's template file at
`app/templates/components/contact-list.hbs` and copy the markup for the
details section from the `ContactsContainer` template:

```handlebars {data-filename="app/templates/components/contact-list.hbs" data-diff="-1,+2,+3,+4,+5,+6,+7,+8,+9,+10,+11,+12,+13,+14,+15,+16,+17,+18"}
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
```

And replace that section in the `ContactsContainer` template with the component:

```handlebars {data-filename="app/templates/components/contacts-container.hbs" data-diff="-1,-2,-3,-4,-5,-6,-7,-8,-9,-10,-11,-12,-13,-14,-15,-16,-17,+18"}
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
<ContactList/>

{{#if selectedContact}}
  <ContactDetails @contact={{selectedContact}} />
{{else}}
  Select a Contact
{{/if}}
```

Great, now we have the framework for our list component set up. Next, we need
to make the component's contact list items dynamic. We'll start by passing in
our `contacts` array as an argument to the list, and we'll loop over the
contacts argument with the `each` helper:

```handlebars {data-filename="app/templates/components/contacts-container.hbs" data-diff="-1,+2"}
<ContactList>
<ContactList @contacts={{contacts}} />

{{#if selectedContact}}
  <ContactDetails @contact={{selectedContact}} />
{{else}}
  Select a Contact
{{/if}}
```
```handlebars {data-filename="app/templates/components/contact-list.hbs" data-diff="+4,+5,+6,+7,+8,+9,+10,+11,-12,-13,-14,-15,-16,-17,-18,-19,-20,-21,-22,-23"}
<nav>
  <input placeholder="search" />
  <ul>
    {{#each @contacts as |contact|}}
      <li>
        <a>
          <h4>{{contact.name}}</h4>
          <p>{{contact.email}}</p>
        </a>
      </li>
    {{/each}}
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
```

Save and refresh, and you should see the same list as before, rendered using our
`contacts` array from the `ContactsContainer` component. The only difference is
that we're missing the `active` class from one of our items. We'll add that
back in a moment, first let's go over the `{{each}}` helper.

As you can see, the `{{each}}` helper takes an array of items and a template
block, and repeats the block for each item in the array. The `as |contact|` part
of the helper is known as a block parameter, which is the way that we receive
the individual items to use in the template. Components and helpers can _yield_
block parameters, similar to calling a callback function with parameters in
Javascript. This is done with the `{{yield}}` helper which we've seen in the
generated templates for our components before we started editing them.

Alright, now we're rendering our contacts list! Next, let's add some
functionality to that search bar so we can filter through contacts.

## Adding Search Functionality

First, we need to hookup the current `input` to send its value to the component
whenever its updated. Ember ships with a built in `Input` component, which
handles the details of this data binding for us:

```handlebars {data-filename="app/templates/components/contact-list.hbs" data-diff="-2,+3"}
<nav>
  <input placeholder="search" />
  <Input placeholder="search" @value={{searchValue}} />
  <ul>
    {{#each @contacts as |contact|}}
      <li>
        <a>
          <h4>{{contact.name}}</h4>
          <p>{{contact.email}}</p>
        </a>
      </li>
    {{/each}}
  </ul>
</nav>
```

Notice how we passed `placeholder` as an attribute, but `@value` as an argument,
because as we mentioned earlier the `Input` component binds the value itself.
This means that when you update the value of the `input` tag, it is communicated
upwards by the `Input` component and modified in the surrounding context (in
this case, the `ContactList` component). It's absolutely possible to have a
component uni-directional bindings in its arguments, where updating a value in a
child component will _not_  automatically update the parent component, but most
often for HTML inputs bi-directional binding is the behavior we want, so `Input`
implements it this way to save some boilerplate. We'll discuss uni-directional
data flow (also known as Data Down, Actions Up) later on.

Now that we have the search value in our component, we need to use it to
actually filter the list. To do that, we'll use a computed property:

```js {data-filename="app/components/contact-list.js" data-diff="+2,+5,+6,+7,+8,+9,+10,+11,+12,+13,+14,+15,+16,+17,+18,+19,+20,+21,+22"}
import Component from '@ember/component';
import { computed } from '@ember-decorators/object';

export default class ContactList extends Component {
  /****** Arguments ******/
  contacts = null;

  /****** Properties ******/
  searchValue = '';

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

As you can see, we've added `filteredContacts` as a standard class getter
function which returns the `contacts` array filtered by the `searchValue`. We
also added class fields for `contacts` and `searchValue`, but those are only
there for us to remember that the values exist on the class, they don't change
any of the functionality of those fields.

The only unusual thing we've added here is the `@computed` decorator. This
decorator memoizes the getter - it caches its value until one of the dependent
keys passed to it (in this case, `'contacts'` and `'searchValue'`) changes. This
way we don't do more work than we need to. It also allows Ember to know when a
value has updated and when it needs to rerender, otherwise it wouldn't know to
rerender the `each` loop when `searchValue` changes.

Let's update the template to use our new `filteredContacts` property:

```handlebars {data-filename="app/templates/components/contact-list.hbs" data-diff="-4,+5"}
<nav>
  <Input placeholder="search" @value={{searchValue}} />
  <ul>
    {{#each @contacts as |contact|}}
    {{#each this.filteredContacts as |contact|}}
      <li>
        <a>
          <h4>{{contact.name}}</h4>
          <p>{{contact.email}}</p>
        </a>
      </li>
    {{/each}}
  </ul>
</nav>
```

Now, whenever we type a value into the search bar it should update the list to
only show the contacts that match the value! Now we have most of the
functionality we wanted for our list, all that's left is adding navigation so
users can select the contact they want to view.

## Adding a Select Action

We've talked about how we can use arguments to pass data to child components.
They can also be used to send callback functions to children, so children can
communicate upwards with their parents when things change or the user interacts
with them. These callback functions are called "actions" in Ember, and we use
the `{{action}}` helper to create them.

Let's use actions to send a newly selected contact whenever the user clicks on
a contact in the `ContactList`. First, we'll add a function to the component
class:

```js {data-filename="app/components/contact-list.js" data-diff="-2,+3,+8,+25,+26,+27,+28,+29,+30,+31"}
import Component from '@ember/component';
import { computed } from '@ember-decorators/object';
import { action, computed } from '@ember-decorators/object';

export default class ContactList extends Component {
  /****** Arguments ******/
  contacts = null;
  onContactSelected = null;

  /****** Properties ******/
  searchValue = '';

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

```handlebars {data-filename="app/templates/components/contact-list.hbs" data-diff="-6,+7"}
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

```js {data-filename="app/components/contacts-container.js" data-diff="+27,+28,+29,+30,+31"}
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
assertion, so you won't accidentally assign a value using standard Javascript
assignment (e.g. `this.selectedContact = contact`).

Now in our template, we can update the `ContactList` component to take this new
action:

```handlebars {data-filename="app/templates/components/contacts-container.hbs" data-diff="-1,+2,+3,+4,+5"}
<ContactList @contacts={{this.contacts}}/>
<ContactList
  @contacts={{this.contacts}}
  @onContactSelected={{action this.setSelectedContact}}
/>

{{#if selectedContact}}
  <ContactDetails @contact={{selectedContact}} />
{{else}}
  Select a Contact
{{/if}}
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

```js {data-filename="app/components/contacts-container.js" data-diff="-27,-28,-29,-30,-31"}
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
```handlebars {data-filename="app/templates/components/contacts-container.hbs" data-diff="+3,-4"}
<ContactList
  @contacts={{this.contacts}}
  @onContactSelected={{action (mut this.selectedContact)}}
  @onContactSelected={{action this.setSelectedContact}}
/>

{{#if selectedContact}}
  <ContactDetails @contact={{selectedContact}} />
{{else}}
  Select a Contact
{{/if}}
```

If you save again, everything should work exactly as before. We're almost done,
we just need to add back the active state to our contact list items.

## Adding Active State

When we converted our plain HTML into the `ContactList` component, we also lost
the `active` class that was applied to one of the contacts. Now that we have
selection logic all setup, we can add it back! First, lets add
`@selectedContact` as an argument to `ContactList`, so we can tell it which
contact to highlight:

```handlebars {data-filename="app/templates/components/contacts-container.hbs" data-diff="+3"}
<ContactList
  @contacts={{this.contacts}}
  @selectedContact={{selectedContact}}
  @onContactSelected={{action (mut this.selectedContact)}}
/>

{{#if selectedContact}}
  <ContactDetails @contact={{selectedContact}} />
{{else}}
  Select a Contact
{{/if}}
```
```js {data-filename="app/components/contacts-list.js" data-diff="+8"}
import Component from '@ember/component';
import { computed } from '@ember-decorators/object';
import { action, computed } from '@ember-decorators/object';

export default class ContactList extends Component {
  /****** Arguments ******/
  contacts = null;
  selectedContact = null;
  onContactSelected = null;

  /****** Properties ******/
  searchValue = '';

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

Then, we'll add a class to the `contact` that is equal to `selectedContact`. In
order to do that, we'll need to check if they are equal _in_ our Handlebars
template. Handlebars does not provide operators like `===` by default, but
helpers can be made for them. We'll use a community package that adds several
helpers for basic comparisions,
[ember-truth-helpers](https://github.com/jmurphyau/ember-truth-helpers). It
specifically adds the `{{eq}}` helper, which is exactly what we're after.

In the command line, use ember-cli to add the package to the app with
`ember install`:

```sh
ember install ember-truth-helpers
```

This installs the package and saves it to `package.json`, along with running any
additional setup code that the package needs.

Now, back in the `ContactList` template, we can add the class by combining the
`{{if}}` and `{{eq}}` helpers:

```handlebars {data-filename="app/templates/components/contact-list.hbs" data-diff="-2,+3"}
<nav>
  <Input placeholder="search" @value={{searchValue}} />
  <ul>
    {{#each @contacts as |contact|}}
      <li>
        <a class="{{if (eq contact this.selectedContact) 'active'}}">
          <h4>{{contact.name}}</h4>
          <p>{{contact.email}}</p>
        </a>
      </li>
    {{/each}}
  </ul>
</nav>
```

Notice that we are using the _inline_ form of the if statement, and embedding it
directly in the `class` string. This allows us to very tersely add conditional
classes, and add them as if they were any other value in the template. We could
also add static classes alongside the `if` statement this way.

Alright, now we have a fully functional contact list! Next, we'll update our
list to fetch data from a remote server, and then we'll add that edit
functionality to the details panel, allowing us to update data.
