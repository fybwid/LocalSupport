Getting Involved
------------------

Project work is organised via this Pivotal Tracker instance:


https://www.pivotaltracker.com/n/projects/742821

The `CURRENT` column is what people are currently working on - feel free to reach out to anyone working on those to see if they are interested in collaborating.

`BACKLOG` column tasks are ordered by priority (according to the client) and have 1, 2 or 3 points indicating difficulty (3 high). You are most welcome to start any task in the backlog, but please do come and say hi in our Slack channel:

https://agileventures.slack.com/messages/localsupport/

You can ask questions in the PT tickets themselves, or in the slack instance.  If you're not getting a response, please do tag `@tansaku` in the slack channel.  That's Sam Joseph, the project manager who'll be happy to help, or direct you to someone who can. 

There's also the `ICEBOX` column where if you see something interesting you want to work on you can start on that.  However please do ensure it has an estimate.  If it does not then please coordinate with the ProjectManager to get the story voted on to create an estimate.  This helps you get input on what it would take to get the story done as well as helping the team understand what it is you want to work on.

It's also great to look through the current PRs to see what code people are submitting:

https://github.com/AgileVentures/LocalSupport/pulls

Leaving comments and +1s where appropriate.  For more general details on joining any AgileVentures project please see: 

https://github.com/AgileVentures/AgileVentures/blob/master/JOINING_A_PROJECT.md


Development Process
------------------

Our default working branch is `develop`.  We do work by creating branches off `develop` for new features and bugfixes.  Any feature should include appropriate Cucumber acceptance tests and RSpec unit tests.  We try to avoid view and controller specs, and focus purely on unit tests at the model and service level where possible.  A bugfix may include an acceptance test depending on where the bug occurred, but fixing a bug should start with the creation of a test that replicates the bug, so that any bugfix submission will include an appropriate test as well as the fix itself.


Each developer will usually work with a [fork](https://help.github.com/articles/fork-a-repo/) of the [main repository on Agile Ventures](https://github.com/AgileVentures/LocalSupport). Before starting work on a new feature or bugfix, please ensure you have [synced your fork to upstream/develop](https://help.github.com/articles/syncing-a-fork/):

```
git pull upstream develop
```

Note that you should be re-syncing daily (even hourly at very active times) on your feature/bugfix branch to ensure that you are always building on top of very latest develop code.

We use [Pivotal Tracker](https://www.pivotaltracker.com/n/projects/742821) to manage our work on features, chores and bugfixes, and we have an [integrated Pivotal/GitHub workflow](https://blog.pivotal.io/pivotal-labs/labs/level-up-your-development-workflow-with-github-pivotal-tracker) that requires the following procedures:

Please create feature/bug-fix branches that include the id of the relevant pivotal tracker ticket, e.g.

```
git checkout -b 112900047_make_capybara_wait_for_javascript_element
```

Please ensure that each commit in your pull request makes a single coherent change and that the overall pull request only includes commits related to the specific GitHub issue that the pull request is addressing.  This helps the project managers understand the PRs and merge them more quickly.

This is a learning project and so we currently do NOT require developers to rebase their work into a single commit - in particular because this interferes with the history of comments in the pull request.

Whatever you are working on, or however far you get please open a "Work in Progress" (WIP) [pull request](https://help.github.com/articles/creating-a-pull-request/) so that others in the team can comment on your approach.  Even if you hate your horrible code :-) please throw it up there; you'll get automated feedback on code style from [hound](https://houndci.com/) and we'll help guide your code to fit in with the rest of the project.

### Pull Requests

Please ensure that your pull request description has a hyperlink to the Pivotal Tracker ticket that it corresponds to, to allow anyone to quickly jump to a description of the story, bug or chore that the pull-request is addressing.  Where possible please do add a couple of sentences explaining the approach taken in the pull request.  This is an example of a good PR with a good description:

https://github.com/AgileVentures/LocalSupport/pull/418

### Git Comments

On your final git commit please include a comment in this format:

```
makes Capybara check for visibility more robust [Finishes #112900047]
```

which will close the relevant Pivotal Tracker ticket when we merge in your pull-request.  A hyperlink to the relevant Pivotal Tracker ticket in the pull request description will also be appreciated.

Code Style
-------------

We follow the [Ruby Style Guide](https://github.com/bbatsov/ruby-style-guide) configured according to https://github.com/AgileVentures/LocalSupport/blob/develop/.rubocop.yml

[Hound](http://houndci.com) inserts style suggestions into pull requests and you can see these in advance by either:

1. Configuring your own fork of LocalSupport with Hound OR
2. Running rubocop from the command line on your diff: https://github.com/m4i/rubocop-git


Feature Flags
-------------

We use Feature Flags to allow a feature to be turned off and on at will.  This allows us to deploy the code to production and check for side effects, before we actually make it available to end users.  We can then enable it at a precise time of our choosing (since merges to develop, staging and master are auto-deployed to each of http://develop.harrowcn.org.uk, http://staging.harrowcn.org.uk and http://harrowcn.org.uk), and can also switch the feature off just as easily if problems occur. 

Please ask for advice on whether a feature you are working on requires a feature flag.  If you do set up a new [Feature Flag (or 'toggle')](http://martinfowler.com/bliki/FeatureToggle.html) please ensure to:

1) Add a migration to place that feature flag in the database, e.g.

```
class AddSearchInputBarOnOrgPages < ActiveRecord::Migration
  def up
    Feature.create(name: :search_input_bar_on_org_pages)
  end

  def down
    Feature.find_by(name: :search_input_bar_on_org_pages).destroy
  end
end
```

2) Activate the feature in `db/seeds.rb` file like so:

```
Feature.activate('search_input_bar_on_org_pages')
```

So that new developers will be set up with the activated features when they first check out a copy of the project and run `rake db:seed`

Notes from original README:

Run "rake doc:app" to generate API documentation for your models, controllers, helpers, and libraries.


Acceptance Testing
------------------

Our Acceptance (or feature) tests are written in Cucumber to use a high level language that is as close as possible to the specifications provided by the client.

Our Acceptance tests are sandboxed against network connections with VCR and Puffing Billy and we use poltergeist as a JavaScript driver for those tests that involve JavaScript functionality on the front end.  Poltergeist uses PhantomJS under the hood and we use the phantomjs gem to fix a particular version of PhantomJS.  If you have PhantomJS installed globally you may want to remove it to ensure the correct version of PhantomJS is in use during the running of the Acceptance tests.

Code Climate
------------

Code Climate does automated review of our pull requests.  They are automated so they are not to be taken too seriously.  They are hints and reminders about code quality.  If you can fix the issues raised that's great, although sometimes you won't have time.  If you have Code Climate issues on your pull request that you can't or won't have time to fix and you'd like to get it merged, please open "refactoring" chores on Pivotal Tracker - one per code climate issue.  Then link those tracker tickets into your pull request to show that you've taken then step.


