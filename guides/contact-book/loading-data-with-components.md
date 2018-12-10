So far, we have a `<ContactList>` component that lists contacts for Zoey and Tomster from a hard coded array of data.
In many apps data is loaded from asynchronous sources such as APIs, web sockets, or user actions.
Let's look at how we can make our `<ContactList>` component work with asynchronous data and load from an API using `fetch`.

# Setting Properties on Components

Currently our `<ContactList>` constructor sets the `contacts` property directly in the constructor.
Let's make this asynchronous by using `window.setTimeout` to set the data after a loading period.
In the constructor let's first set `contacts` to an empty array, then using `window.setTimeout`,
we can set the value of `contacts` back to our data array after 2000 milliseconds:

```js {data-filename="app/components/contact-list.js" data-diff="+6,+7,+8,+9,+10,+11,+12,+13,+14,+15,+16,+17,+18,-19,-20,-21,-22,-23,-24,-25,-26,-27,-28"}
export default Component.extend({
  init() {
    this.super(...arguments);

    this.listTitle = 'My Contacts';
    this.contacts = [];
    window.setTimeout(() => {
      this.contacts = [
        {
          name: 'Zoey',
          email: 'zoey@emberjs.com',
        },
        {
          name: 'Tomster',
          email: 'tomster@emberjs.com',
        },
      ];
    }, 2000);
    this.contacts = [
      {
        name: 'Zoey',
        email: 'zoey@emberjs.com',
      },
      {
        name: 'Tomster',
        email: 'tomster@emberjs.com',
      },
    ];
  }
});
```

Now our application is throwing an error that says:

> You must use set() to set the `contacts` property

This is because Ember has setup observers on the `contacts` property in our `<ContactList>` component and now we need to use `this.set` to explicitly change the value.
Using `this.set` allows Ember to track changes to properties in Components.
This means that any parts of our templates that require the `contacts` value will automatically update when we call `this.set` with a new value.
Let's use `this.set` to update the `contacts` value for our list:

```js {data-filename="app/components/contact-list.js" data-diff="+5,+6,-7,-8,+11,+12,+13,+14,+15,+16,+17,+18,+19,+20,-21,-22,-23,-24,-25,-26,-27,-28,-29,-30"}
export default Component.extend({
  init() {
    this.super(...arguments);

    this.set('listTitle', 'My Contacts');
    this.set('contacts', []);
    this.listTitle = 'My Contacts';
    this.contacts = [];

    window.setTimeout(() => {
     this.set('contacts', [
       {
         name: 'Zoey',
          email: 'zoey@emberjs.com',
        },
        {
          name: 'Tomster',
          email: 'tomster@emberjs.com',
        },
      ]);
      this.contacts = [
        {
          name: 'Zoey',
          email: 'zoey@emberjs.com',
        },
        {
          name: 'Tomster',
          email: 'tomster@emberjs.com',
        },
      ];
    }, 2000);
  }
});
```

Now our application is loading the contact data asynchronously, but the contact list is empty when there is no data.
Let's setup a loading state for our component so that we don't have an empty list.

# Adding a Loading State with the `if` Helper

In our component, let's add a new property called `loading` and in the constructor we'll start by setting it to `true` since we're loading data.
Then in the `setTimeout` let's reset `loading` to false before setting the value of our contacts array:

```js {data-filename="app/components/contact-list.js" data-diff="+7,+9"}
export default Component.extend({
  init() {
    this.super(...arguments);

    this.set('listTitle', 'My Contacts');
    this.set('contacts', []);
    this.set('loading', true);
    window.setTimeout(() => {
      this.set('loading', false);
      this.set('contacts', [
        {
          name: 'Zoey',
          email: 'zoey@emberjs.com',
        },
        {
          name: 'Tomster',
          email: 'tomster@emberjs.com',
        },
      ]);
    }, 2000);
  }
});
```

Now we can use the `this.loading` property in our template.
In Handlebars, we can use the `if` helper as a block to render content only when the argument value is truthy.
Let's use this to show an `h4` that says "Loading" when `this.loading` is true:

```handlebars {data-filename="app/templates/components/contact-list.hbs" data-diff="+4,+5,+6"}
<div>
  <h2>{{this.listTitle}}</h2>

  {{#if this.loading}}
    <h4>Loading</h4>
  {{/if}}
  <ul>
    {{#each this.contacts as |contact|}}
      <ContactItem @name={{contact.name}} @email={{contact.email}}/>
    {{/each}}
  </ul>
</div>
```

We should also hide the outer `ul` for our empty list when the component is loading.
We can do this using the `else` helper in our `if` block to only show the `ul` when `this.loading` is falsey:

```handlebars {data-filename="app/templates/components/contact-list.hbs" data-diff="+6,+7,+8,+9,+10,+11"}
<div>
  <h2>{{this.listTitle}}</h2>

  {{#if this.loading}}
    <h4>Loading</h4>
 {{else}}
   <ul>
     {{#each this.contacts as |contact|}}
       <ContactItem @name={{contact.name}} @email={{contact.email}}/>
      {{/each}}
    </ul>
  {{/if}}
</div>
```

Now that we have our loading screen, we can rearrange our code to load in data from an API.

# Loading Data Using `fetch`

The function [`window.fetch`](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) (or just `fetch` for short) provides a powerful and simplified method for working with HTTP requests in JavaScript applications.
We'll use `fetch` to make a GET request to `http://ember-contact-book-api.herokuapp.com/people` and then we'll use the data returned by the server to populate the `contacts` property in our `<ContactList>` component.

In our constructor, let's fetch our contacts and since `fetch` returns a promise, we can call `.then` on the result and pass in a function to be run when the promise resolves.
Here, we'll parse the response as json, and we'll chain another `.then` call to wait for parsing to finish before taking the resulting array of data and setting it to our `contacts` property:

```js {data-filename="app/components/contact-list.js" data-diff="+9,+10,+11,+12,+13,+14,-15,-16,-17,-18,-19,-20,-21,-22,-23,-24,-25,-26,-27"}

export default Component.extend({
  init() {
    this.super(...arguments);

    this.set('listTitle', 'My Contacts');
    this.set('contacts', []);
    this.set('loading', true);

    fetch('http://ember-contact-book-api.herokuapp.com/people')
      .then(res => res.json())
      .then(contacts => {
        this.set('loading', false);
        this.set('contacts', contacts)
      });
    window.setTimeout(() => {
      this.set('loading', false);
      this.set('contacts', [
        {
          name: 'Zoey',
          email: 'zoey@emberjs.com',
        },
        {
          name: 'Tomster',
          email: 'tomster@emberjs.com',
        },
      ]);
    }, 2000);
  }
});
```

Now we are loading data from the API and directly displaying the data in our `<ContactList>` component!
