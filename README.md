## Ember Tutorials Soruce

This repository is part of a Work-In-Progress project to separate and expand on the Ember Tutorials in the [Ember Guides](https://guides.emberjs.com).

As this project is pre-1.0, no content should be taken as the final word. Additional review is still pending.

## Prerequisites

You will need the following things properly installed on your computer.

* [Git](https://git-scm.com/)
* [Node.js](https://nodejs.org/) (with npm)
* [Ember CLI](https://ember-cli.com/)
* [Google Chrome](https://google.com/chrome/)

## Local Development

To see what a local copy of the Tutorial markdown looks like:

* Clone the [Ember Tutorial App](https://github.com/ember-learn/tutorial-app) App repository
* link the `ember-tutorials-source` repository by running `npm link` inside this repository, then `npm link @ember-learn/cli-guides` in the guides-app
* `npm install` and `ember serve` in the guides app
* Visit your app at [http://localhost:4200](http://localhost:4200).
* Visit your tests at [http://localhost:4200/tests](http://localhost:4200/tests).

### Adding more things to the table of contents

See `pages.yaml` in the cli-guides-source. Whatever has a url of index will be what is shown for the top level path, like `/tutorial/`. There must be an `index.md` under each topic.

### Deploying

See instructions on the [cli-guides-app](https://github.com/ember-learn/cli-guides-source) README.
