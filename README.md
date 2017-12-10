# Project: `site/`

This project contains the landing page, navigation, documentation, blog, and standard look-and-feel for the apps in the [GameDevUtils.com](http://gamedevutils.com/) suite of tools.

## The Need

The individual tools at [GameDevUtils.com](http://gamedevutils.com/) are built as separate [ReactJS](https://reactjs.org/) single-page applications ([SPA](https://reactjs.org/docs/glossary.html)). So, this landing page site project serves multiple purposes:

1. Have a landing page that showcases, describes, and promotes the apps.
1. Host announcements and blog posts in a single location.
1. Use [Jekyll](https://jekyllrb.com/) to make generating a static site easier.
1. Don't duplicate the infrastructure for documentation across apps.
1. Provide a unified, filtereable search across all apps.
1. To ultimately provide for versioning and localization of content.
1. To ultimately provide for accessibility compliance.

## The Technology

The `site/` project is written in vanilla HTML, CSS, JavaScript, [Liquid](https://github.com/Shopify/liquid/wiki/liquid-for-designers), and [GitHub-flavored markdown](https://help.github.com/categories/writing-on-github/). [Jekyll v3.6.x](https://jekyllrb.com/) is used to render the static pages, which are then hosted on [GitHub Pages](https://pages.github.com/). The design is based on the [Bootstrap v3.3.x](https://getbootstrap.com/docs/3.3/) and uses the [United theme](https://bootswatch.com/3/united/) from [Bootswatch v3.3.x](https://bootswatch.com/3/).

For the most part, all of this should be transparent to you. You can just edit the markdown, push your changes, then revel and profit.

## The Automated Tests

Yes, the result of the `site\` project build and deploy is just a static blog. I want to catch some of the more-common, fat-fingered errors before I publish to the internets and embarrass myself further. The content is embarrassing enough, thank you very much.

So, what do the tests look for?

* **Valid Markdown:** [Markdown Lint Tool](https://github.com/markdownlint/markdownlint#markdown-lint-tool) is used to check a long list of [formatting violations](https://github.com/markdownlint/markdownlint/blob/master/docs/RULES.md#rules).
* **Valid HTML:** [HTMLProofer](https://github.com/gjtorikian/html-proofer#htmlproofer) is used to check a variety of [common issues](https://github.com/gjtorikian/html-proofer#whats-tested).
* **Valid CSS:** [CSS Lint](http://csslint.net/) provides many checks against common [problems with style sheets](https://github.com/CSSLint/csslint/wiki/Command-line-interface#--list-rules).
* **Valid JS/JSX:** [ESLint](https://eslint.org/) scans the JavaScript and JSX for [common script violations](https://eslint.org/docs/rules/).
* **Valid Liquid:** ?? TODO
* **Valid Bootstrap:** ?? TODO
* **Accessibility:** ?? TODO

## Continuous Integration

Test are great for validating your changes before committing to the git server. The following paragraphs describe how our tests are triggered (automatically and manually).

### Code Changes on/for the `master` Branch

The tests for this project are run on every `git push` or [pull request](https://help.github.com/articles/about-pull-requests/) to the `master` branch, triggered by a [web hook on GitHub](https://developer.github.com/webhooks/) that's registered to the [CircleCI](https://circleci.com/) build service.

### Testing Your Code Locally

If you had to push your changes to trigger the tests with each edit, you would pollute your source control history and significantly increase the amount of time it takes to make progress on your project. The [CircleCI Command Line Interface (CLI)](https://circleci.com/docs/2.0/local-jobs/) is configured for this project to make it possible to reproduce the functionality of the CircleCI service locally, without having to trigger a commit and push.

### Configuring CircleCI for Docker

There are several tech stacks to choose from. Our CircleCI configuration is based on the Docker image for Ruby 2.4.1 with Node. You should find all the information you need to get up and running with the CircleCI CLI at the following links:

* [Using the CircleCI Command Line Interface](https://circleci.com/docs/2.0/local-jobs/)
* [Getting Started with Pre-Built CircleCI Docker Images](https://circleci.com/docs/2.0/circleci-images/)
* [Base Docker Image (circleci/ruby:2.4.1-node)](https://hub.docker.com/r/circleci/ruby/tags/)

The CircleCI configuration file is located in our project at `./.circleci/config.yml`. It's a good base image for [Jekyll](https://jekyllrb.com/).

> **Behind the Scenes in `config.yml`:** Once the [bundler gem](http://bundler.io/) is installed (`gem install bundler`), and the gems that we have configured in the `Gemfile` are installed (`bundler install`), Ruby is ready.
>
> The next several build steps install the [NodeJS](https://nodejs.org/) packages [via NPM](https://www.npmjs.com/) that we will use, as described in the `package.json` file.

### NPM Lint Scripts

The lint scripts assume that you have done the following:

1. Initialized the bundler (by running `gem install bundler` in the `./docs/` folder)
1. Installed the gems (by running `bundler install` in the `./docs/` folder)
1. Built your site (by running `jekyll build` in the `./docs/` folder)

> **Note:** You should see some static HTML in the `./docs/_site/` folder at this point. If everything went well, we should be able to run the individual lint scripts separately (from the project's root folder).

To focus on a particular issue, you can run the following individual lint scripts to view the errors for that component:

* `npm run lint-md` - validates the markdown for the project
* `npm run lint-css` - validates the CSS for the project
* `npm run lint-js` - validates the JS and JSX for the project
* `npm run lint-html` - validates the HTML for the project

> **Note:** There are two quirks with the markdown linter ...
>
> 1. It doesn't care if the site has been built (with 'jekyll build') or not. The markdown linter parses and evaluates the raw site files.
> 1. The CLI for markdownlint wasn't as [glob](https://www.npmjs.com/package/glob)-friendly as the other tools, so the lint script makes two passes - one for `*.md` files and one for `*.markdown` files. The only downside to that is that you'll have to fix the errors in your ".md" files before you even see the errors in the ".markdown" files.
>
> *I plan to merge the two lists of markdown files so that the two-pass evaluation isn't needed any more, but I have bigger fish to fry these days.*
