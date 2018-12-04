Now that we have our HTML and styles all figured out,
we'll create our first component - the `ContactItem`.
This `ContactItem` component will show the name and email for a single contact in our contact book.

## What is a Component?

Components are the building blocks for an Ember application UI.
Components let us break our application into smaller more manageable pieces similar to HTML elements.
These components can be reused many times in our application and we can use attributes to customize each component.
Ember components are like an extension to HTML and are heavily influenced by [native Web Components](https://developer.mozilla.org/en-US/docs/Web/Web_Components) - these components allow us to create our own custom tags with layout, style, and behavior.

In Ember we can create a new instance of a component from our template by using a CapitalCase element name.
Let's look at how we can modify our existing application.hbs template to call a new `ContactItem` component:

```handlebars {data-filename="app/templates/application.hbs" data-diff="+5,-6,-7,-8,-9,-10,-11"}
<div class="container">
  <nav>
    <ul>
      <!-- component -->
      <ContactItem />
      <li>
        <a class="active">
          <h4>Zoey</h4>
          <p>zoey@emberjs.com</p>
        </a>
      </li>

      <!-- standard HTML li -->
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

Notice that we can mix and match custom components and regular HTML elements as needed.
Here we replaced the entire `li` element for Zoey with this new `ContactItem` component.
Right now, if we save this file, our application will not render because there is no `ContactItem` component in our app.

Let's fix this by creating our first component!

# Component templates

In Ember we can create a component by declaring a component template, a component class, or both.
For small pieces of UI without much logic, we can create a "Template Only" component.

With Ember, the file location has to match our component naming.
Since we want to use a component called `ContactItem`, we need to create a template in `app/templates/components/contact-item.hbs`.
For every component the template file name is the dasherized version of the component that we want to use.
In this new template let's add the `li` for Zoey:

```handlebars {data-filename="app/templates/components/contact-item.hbs"}
<li>
  <a class="active">
    <h4>Zoey</h4>
    <p>zoey@emberjs.com</p>
  </a>
</li>
```

Now, when we save this file, our application will have a new `ContactItem` component with the HTML for the contact Zoey.
Next, let's look at customizing our component by sending in data via attributes.

# Component attributes

Similar to attributes on HTML elements, Ember components can also have attributes that modify their data and behavior.
Let's look at passing in the name and email for each user in our contact book into the `ContactItem` component so that it is more reusable.

First let's look at passing in attributes to Ember components.
Since Ember components act like HTML elements, attributes are applied to the outer element of our component.
To specifically send data in to the component template and JavaScript we need to use a special attribute syntax.
In Ember attributes starting with `@` are sent to the component and not applied to the HTML element.
So, let's add attributes for `@name` and `@email` to our existing `ContactItem` component in the `application.hbs` template, and let's replace the `li` for Tomster with a `ContactItem` component with matching `@name` and `@email` attributes:

```handlebars {data-filename="app/templates/application.hbs" data-diff="+7,+8,+9,+10,-11,-12,-13,-14,-15,-16"}
<div class="container">
  <nav>
    <ul>
      <ContactItem @name="Zoey" @email="zoey@emberjs.com"/>

      <!-- Attributes can be indented too -->
      <ContactItem
        @name="Tomster"
        @email="tomster@emberjs.com"
      />
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



Now when we look at our application there are two `li` elements for Zoey.
This is because the `ContactItem` component is hard coded with HTML for Zoey.
Instead, let's replace this with dynamic data using Handlebars!

# Displaying Data With Handlebars

In Handlebars dynamic data is surrounded by double curly braces.
This allows Ember to track changes in data and display it to our user, keeping the HTML up to date.
Let's use this syntax to display the values that are passed into our component via attributes:

```handlebars {data-filename="app/templates/components/contact-item.hbs" data-diff="+3,+4,-5,-6"}
<li>
  <a class="active">
    <h4>{{@name}}</h4>
    <p>{{@email}}</p>
    <h4>Zoey</h4>
    <p>zoey@emberjs.com</p>
  </a>
</li>
```

Here we are using the variables `@name` and `@email` to specify that these values come from our component's attributes.
Now, when we visit our application there are now list items for both Zoey and Tomster.

So far, we've created a component to break out the HTML for `ContactItem` and we used attributes and Handlebars to make this component reusable for different data.
Next, let's look at using a component to load in dynamic data via JavaScript.
