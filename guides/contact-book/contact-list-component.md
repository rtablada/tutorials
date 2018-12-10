Now that we have a `ContactItem` component to display data for one user in our contact book, we need a way to load in data for our contact list instead of hard-coding it into component attributes.
To do this, let's create a new `ContactList` component that will be responsible for loading contact data and displaying multiple `ContactItem` components.
We'll start by creating a new file in `app/templates/components/contact-list.hbs` and here we'll past in the existing Handlebars for our list of contacts:

```handlebars {data-filename="app/templates/components/contact-list.hbs"}
<ul>
  <ContactItem @name="Zoey" @email="zoey@emberjs.com"/>

  <ContactItem
    @name="Tomster"
    @email="tomster@emberjs.com"
  />
</ul>
```

Then we can update our `application.hbs` template to use this new `ContactList` component:

```handlebars {data-filename="app/templates/application.hbs" data-diff="+3,-4,-5,-6,-7,-8,-9,-10,-11"}
<div class="container">
  <nav>
    <ContactList />
    <ul>
      <ContactItem @name="Zoey" @email="zoey@emberjs.com"/>

      <ContactItem
        @name="Tomster"
        @email="tomster@emberjs.com"
      />
    </ul>
  </nav>
</div>
```

Now that we have moved our contact logic to the new `ContactList` component, let's look at how we can interact between our component templates and JavaScript.

# Component Classes

Ember components can be created using just templates, but there are times where we need to use JavaScript to interact with data and respond to user interactions.
For this, we can create a Component class to match our component template.
To get started, let's create a new file in `app/components/contact-list.js`.
Here we will import from `@ember/component` and export a new class called `ContactList` that extends from `Component`:

```js {data-filename="app/components/contact-list.js"}
import Component from '@ember/component';

export default Component.extend({

});
```

Now we have the basic class for our `ContactList` component and we can start looking at how this JavaScript class interacts with our component template.
First, let's create a constructor so that we can set a property called `listTitle` to the string `My Contacts` in our `ContactList` class.
In this constructor we need to call `this.super(...arguments)` before we use `this` so that Ember can setup the basic structure for our component.

```js {data-filename="app/components/contact-list.js" data-diff="+4,+5,+6,+7,+8"}
import Component from '@ember/component';

export default Component.extend({
  init() {
    this.super(...arguments);

    this.listTitle = 'My Contacts';
  }
});
```

Now to use the value of `listTitle`, let's go to our `contact-list.hbs` template file.
In component templates to refer to the component instance we can use the `this` variable like we would in JavaScript.
So to print our `listTitle` value let's create a new `h2`:

```handlebars {data-filename="app/templates/components/contact-list.hbs" data-diff="+1,+2,+11"}
<div>
  <h2>{{this.listTitle}}</h2>
  <ul>
    <ContactItem @name="Zoey" @email="zoey@emberjs.com"/>

    <ContactItem
      @name="Tomster"
      @email="tomster@emberjs.com"
    />
  </ul>
</div>
```

Notice, instance properties are referred to using `this.propertyName` while component attributes always are referred to using `@attributeName`.

Now we have a list of `ContactItem`s and we can display data from our component JavaScript class.
Next, let's look at using an array of data and displaying these values.

# Working With Arrays in Ember Components and templates

In our component class, let's create a new array of objects and set this to a property called `contacts`, this will store the array of all users in our list.

```js {data-filename="app/components/contact-list.js" data-diff="+8 ,+9 ,+10 ,+11 ,+12 ,+13 ,+14 ,+15 ,+16 ,+17"}
import Component from '@ember/component';

export default Component.extend({
  init() {
    this.super(...arguments);

    this.listTitle = 'My Contacts';
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

Now that we have this data set to a property on our component, let's look at how we can work with this data in our template.
First, let's try to display the value of `{{this.contacts}}` in the `ul` of our `contact-list.hbs` template:

```handlebars {data-filename="app/templates/components/contact-list.hbs" data-diff="+4"}
<div>
  <h2>{{this.listTitle}}</h2>
  <ul>
    {{this.contacts}}
    <ContactItem @name="Zoey" @email="zoey@emberjs.com"/>

    <ContactItem
      @name="Tomster"
      @email="tomster@emberjs.com"
    />
  </ul>
</div>
```

Now our application is displaying `[object Object],[object Object]` in the `ul` because Ember is calling `toString` on our array of data.
To work with this array, we'll need to use the Handlebars `each` helper.

The `each` helper takes in a single argument (the array that we want to loop through), then calls a "block" or closure function for each item in the array of data.
When using a helper that calls a block we need to use a `#` before the helper name and then add a closing `/` tag for the block.
In this each loop, let's create the `ContactItem` for Zoey:

```handlebars {data-filename="app/templates/components/contact-list.hbs" data-diff="+4,+5,+6,-7,-8"}
<div>
  <h2>{{this.listTitle}}</h2>
  <ul>
    {{#each this.contacts}}
      <ContactItem @name="Zoey" @email="zoey@emberjs.com"/>
    {{/each}}
    {{this.contacts}}
    <ContactItem @name="Zoey" @email="zoey@emberjs.com"/>

    <ContactItem
      @name="Tomster"
      @email="tomster@emberjs.com"
    />
  </ul>
</div>
```

Now we have two copies of the Zoey contact, but this isn't what we really wanted.
Instead we want to show a `ContactItem` for every object in our `this.contacts` array.
To do this, we'll need the value of each item as we loop through the array using the `each` helper.
In Handlebars block helpers can yield variables back to our template similar to arguments in JavaScript closure functions.
To use these values we need to add `as ||` to our `each` loop.
Let's call this yielded variable `contact` since it will contain a single contact from our array:

```handlebars {data-filename="app/templates/components/contact-list.hbs" data-diff="+4,-5"}
<div>
  <h2>{{this.listTitle}}</h2>
  <ul>
    {{#each this.contacts as |contact|}}
    {{#each this.contacts}}
      <ContactItem @name="Zoey" @email="zoey@emberjs.com"/>
    {{/each}}

    <ContactItem
      @name="Tomster"
      @email="tomster@emberjs.com"
    />
  </ul>
</div>
```

Now we have a `contact` variable within the `each` block and we can use this object to pass in attributes to the `ContactItem` component.
We can also remove the hard-coded contact for Tomster since this should already be in our array of data:

```handlebars {data-filename="app/templates/components/contact-list.hbs" data-diff="+5,-6,-9,-10,-11,-12"}
<div>
  <h2>{{this.listTitle}}</h2>
  <ul>
    {{#each this.contacts as |contact|}}
      <ContactItem @name={{contact.name}} @email={{contact.email}}/>
      <ContactItem @name="Zoey" @email="zoey@emberjs.com"/>
    {{/each}}

    <ContactItem
      @name="Tomster"
      @email="tomster@emberjs.com"
    />
  </ul>
</div>
```

Now, we have our list of contacts based on the array of data in our component class.
Next, let's look at changing this value over time and loading data from an HTTP API.
