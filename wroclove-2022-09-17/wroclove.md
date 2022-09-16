1. Introduction
2. What can be packaged as a gem?
  a. A ruby package
  b. A railtie
  c. A rails engine
3. What is a rails engine?
4. Generate the engine
  a. What do we get
  b. Mount to the app
  C. Demo it works
  d. Maybe show how to use the dummy as the base app
5. Start adding functionality
  a. Demo a controller and view
  b. Demo api route
  C. Demo embedding views
6. Testing
  a. Show the dummy app
7. Build the gem
  a. Explain the compressing everything to a gem file
  b. Package only what you need
  c. Compile assets
  d. Use docker to package in isolation
8. Push to rubygems
9. Automate all of that using Github actions
  a. Testing on every PR
  b. Linting and code analysis
  c. Post merge actions
  d. Tag and release
10. Overview
  a. Generate a gem
  b. Add functionality
  c. Test
  d. Bundle together
  e. Automate it

Hey everyone!
I'm thrilled to be here and talk to you about the magic of Rails.
I'm going to talk to you today about packaging a Rails engine. From generation to automation.

---
# Intro

My name is Adrian and I'm the author of Avo. An application building framework on top of Ruby on Rails. Avo helps Ruby developers build apps 10x faster than traditional methods.
---

---
# Rails engine

First of all, what is a Rails engine and why should we use one?
Engines can be considered miniature applications that provide functionality to their host application.
Engines help us group pieces of functionality that should be together.
_Show a screenshot with the directory tree_
More than that, each engine comes with it's own routes, controllers, views, models and everything you got used to when spinnng up a new Rails app. It's basically a mini rails app that can talk, in code, with your parent app.

If you're building a multi-tenancy app like a marketplace, you might have your own app, an engine for buyers and an engine for sellers. So you'll have three separate apps. The main app, the sellers app and the buyers app. So you'll be able to keep each domain under in it's own separate space.
**Rails itself is a supercharged engine.**

<!-- Usually, when you generate an engine it will live in your apps root directory. If you have more than one engine that is of the same type you can move them in a separate directory.
_Show Facebook, twitter, pinterest engines in a channels directory._
For example, if you build a social media management platform, you might have separate engines for facebook, twitter and pinterest, and you move them together in a `channels` directory. -->


# Sharing is caring

This is a perfect setup for when you have engines just for yourself, but you might want to distribute your engines to more developers (maybe think of an example that is rememberable like "your coworker needs that thing too..."). Like, for example, you're building an admin panel framework for Rails apps.
_Show an "Avo the best admin panel framework for Rails" photo_
So let's start building.

_Play recording of the generation process_
We'll run the new plugin command and are going to choose the mountable option.
This way we get the engine, dummy app, version file, the gemspec file, and other goodies we need to for packaging and distribution.

You might wonder what the dummy app does. It's there to help you test your engine. Test it as in automated tests or manual tests.

# Functionality
So let's add some functionality to our engine.

_Build a minimal list of all of our models, links with routes and show how we can browse the engine._

We'll build a tiny navbar that shows all our models from the parent app, linking to each page where we quickly display them.

I'm not going to build the whole admin panel framework here. You can check out Avo by yourselves. It's definitely better than this :P

# Tests

Let's add a test to make sure nothing breaks when we push changes.

_Add a test to see the links are properly generated._

# Package it

Ok. So now we have some functionality to pass around to our fellow developers. What's the easiest way of sending it their way? Through a ruby gem.

_show the rubygems page_
_show the gemspec_

Before we go ahead and pakage it, we need to fill in some information in our gemspec. The gemspec file is like the `package.json` file in a npm package.
_Show an image of the gemspec_
It holds all the description for the gem, some metadata information required by rubygems to host it on their platform.
The files option tells the gem tool what files to pack. You might have a node_modules directory inside your project and you probably shouldn't package that.
Last but not least, this is where you specify what dependencies your gem requires. The dependencies declared here are going to be read by the parent application and taken into account when the user runs a `bundle install`.
You also have a `Gemfile`, but that's used only for the dummy app. So if there's anything you need on runtime in the parent app, put it in the `gemspec`.

After we finish up updating all that information we can fire up the build command.

_"bundle exec rails build" screenshot and GIF_

This will use the `files` configuration in your `gemspec`, take all of them and bundle them up in a `.gem` file.

# Build in isolation

What I like to do is to build the gem in isolation. I do that because there might be files in those paths that I don't want to ship with this release, or the compiled assets might contain unwanted changes so I prefer to do it in isolation.
I do that using a dockerfile where I start from a ruby image, copy the project, install my dependencies, and compile the assets again. This way the build process becomes idempotent and the resulted artifact is the same for a given state of files.
I use this workflow to publish prerelease versions with quickfixes and tryout features.

# Helper script

The previous step only builds the gem, it doesn't really output anything in the local environment. I have a helper script that does that.

# Publish

Next, you publish that file to rubygems using a `gem push` command. You need to register an account and authenticate your environment with rubygems before pushing.

There it is. You published your work to the world. Now, anyone cam come in and use your thing.

When your developer friend wants to use it they add the gem to their gemfile and mount the engine in their routes file.
Now they can go to that route and use your gem.

This is cool! You built something that someone else is using.

# Updates incoming

But how do we go about when we want to introduce new features? We do that using versioning.

# Versioning

The generated plugin comes with a `version.rb` file that is used in the package and read by rubygems when we push it.
For each gem we push, rubygems will check out the version file and use that as the version number displayed.
In order to update the version you simply need to increment the version number in the version file and run `bundle` to have that reflected in the gemfile lock.

There is the `bump` gem that helps you with this. You run the bump command with a major, minor, or patch argument and it will do it for you. It will bump the version number, run the bundle command and even cut a tag and commit the changes for you if that's what you need.

# Release notes

It's nice that for every release to have dedicated release notes that outline what changed from the last one. There are a few ways of keeping a changelog. You can have one large `Changelog.md` file in you repo and fill it in manually with all that happened; you can use a page on your website or docs page; you might use Confluence, or you can use GitHub Releases.

With Avo we use GH releases. We tag each new version with a git tag and create a release on GH with a changelog.
I'll talk more about releases in the automation section.

# Updates workflow

So the general workflow is:

 - checkout a new branch
 - add your changes and commit
 - push to GitHub and open a PR
 - merge the PR
 - pull the `main` branch
 - bump the version number
 - build the gem and push to rubygems
 - update the changelog



# Automation

Now, let's automate the process. We're going to use GitHub Actions to do it.

0. How GA works
GitHub Actions and other CI systems work by using a yaml file as configuration. You basically tell it when to run ceratin tasks (when you create/update/close PRs, when you push code, on a designated time or on the push of a button), you give it the configuration using environment variables and secrets, and you tell it what to do; what tasks to run.

GA also has a marketplace of pre-made actions ready for you to use like git checkout, setup ruby, add caching layers, label PRs, trigger other automations, and much much more.

1. Testing
Now, that we know a little bit about our CI system, let's make sure we run tests on each PR and each push.
_Show the test.yml config_

We tell it when we need to run the tests; on PRs on `main` and regular commits, we give it the required environment variables, give it the base image, and start configuring our testing job.
_Have the code present with highlighted areas as I speak_
Each job receives a list of tasks that it will run in the cloud. These tasks can be running simple bash commands to running the pre-made actions.
The first one is to do a git checkout of the project so we have something to work on. Next we set up ruby, do a bundle install, and we run our tests.
This is the basic setup. You can go wild after this. Add caching layers for bundler and yarn, install multiple databases, run the tests in matrixes testing against multiple ruby or Rails versions, spit out artifacts with screenshots when tests fail, generate coverage, and much much more.
You can find a few of these enhancements in Avo's test.yml file. I'm happy to help anyone with explaining how they work.

Now, if the tests fail, the jobs shows up as failed on the PR page.

_Screenshot of the linting CI job_
2. Setting up linting and code anaylisys CI jobs. this is being done more easily using a pre-made actions from reviewdog. They have templates for eslint and rubocop and standardrb.
We run this for every PR.

4. Labeling PRs

You might have a small repo or a big one, but the fact of the matter is that things can get messy fast and tagging PRs definitely helps with housekeeping.

We use a popular tagging scheme where we divide PRs into `features`, `fixes`, `chores`, or `refactors`.

The way we automate is with another action called `pr-labeler-action`. It will read the branch name and figure out from there what kind of PR this is.
This way we can build the release notes automatically more easily.

5. Building release notes
It's nice that for every release to have notes that outline what changed from the last time we published.
We can automate that using the `release-drafter` GH action.
_Screenshot of the job and the output_

We run this after we merge a PR. It will make a diff between the last release and the current HEAD and identify all the merged PRs. Then it will create a draft release on GH with the PRs neatly separated into categories. This also pulls out the author of the PR and adds a reference to that PR which GH nicely turns into a link.

We also get a nice list with all the contributors for that release.

6. Cutting a release
We're getting close to the grand finally. Automating the release process.

So we talked about adding a new feature or fix to the repo using gitflow (branch, fix, commit, pr, merge), we talked about cutting releases using git tags and github releases, and now we're going to put everything together using...  you guessed it GitHub Actions.

So let's create a new action and set to
