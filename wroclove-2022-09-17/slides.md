---
theme: default
background: >-
  https://images.unsplash.com/photo-1578853166038-e19018efc083?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=2070&q=80
class: text-center
highlighter: shiki
lineNumbers: false
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
drawings:
  persist: false
title: How To Package A Rails Engine
---

# How To Package A Rails Engine
## From Generation To Automation

<!--
Dzień dobry everyone!

I'm thrilled to be here.

I'm going to talk to you today about packaging a Rails engine. From generation to automation.
-->

---
layout: intro
---

# Adrian Marin

<div class="leading-8 opacity-80">
  Author of Avo. An application building framework on top of Ruby on Rails.
  <br>
  <br>
  Worked as a freelancer, in corporations, and a Silicon Valley Start-up.
  <br>
  <br>
  Aspiring entrepreneur.<br>
</div>

<div class="my-10 grid grid-cols-[40px,1fr] w-min gap-y-4">
    <ri-github-line class="opacity-50"/>
    <div><a href="https://github.com/adrianthedev" target="_blank">adrianthedev</a></div>
    <ri-twitter-line class="opacity-50"/>
    <div><a href="https://twitter.com/adrianthedev" target="_blank">adrianthedev</a></div>
    <ri-user-3-line class="opacity-50"/>
    <div><a href="https://adrianthedev.com" target="_blank">adrianthedev.com</a></div>
    <ri-user-3-line class="opacity-50"/>
    <div><a href="https://avo.cool" target="_blank">avo.cool</a></div>
</div>

<img src="https://adrianthedev.com/img/profile_square.jpg" class="rounded-full w-40 abs-tr mt-16 mr-12"/>

<!--
My name is Adrian and I'm the author of Avo.

An application-building framework on top of Ruby on Rails.

Avo helps Ruby developers build apps 10x faster than traditional methods.
-->

---

# What is a Rails Engine

<div class="flex flex-col">
  <div class="flex space-x-2 justify-between w-full">
    <div class="w-1/2">
      <img src="/img/engine.jpg" class="" />
    </div>
    <div class="flex h-1/2">
      <img v-click="2" src="/img/minime.jpg" class="h-[250px]" />
    </div>
  </div>
  <div v-click="1" class="flex-1 mt-16">
    Engines can be considered miniature applications that provide functionality to their host application.
  </div>
</div>

<div class="flex align-center justify-center">
  <img v-click="3" src="/img/engines.jpg" class="h-[250px] -top-[300px] relative" />
</div>

<!--
- What is an engine?
- They have their own... and they are miniature apps inside the host app
- Example of marketplace with two engines

First of all, what is a Rails engine and why should we use one?
Engines can be considered miniature applications that provide functionality to their host application.
Engines help us group pieces of functionality that should be together.

More than that, each engine comes with it's own routes, controllers, views, models and everything you got used to when spinnng up a new Rails app. It's basically a mini rails app that can talk, in code, with your parent app.

If you're building a multi-tenancy app like a marketplace, you might have your own app, an engine for buyers and an engine for sellers. So you'll have three separate apps. The main app, the sellers app and the buyers app. So you'll be able to keep each domain under in it's own separate space.
**Rails itself is a supercharged engine.**
-->
---

# Sharing is caring

<div class="flex items-center justify-center">
  <img v-click src="/img/avo_the_best.jpg" class="" />
</div>

<!--
- This is a perfect setup for when you have engines just for yourself, but you might want to distribute your engines to more developers (maybe think of an example that is rememberable like "your coworker needs that thing too..."). Like, for example, you're building an admin panel framework for Rails apps.
- Pause
- So let's start building.
-->

---

# Generate an engine

```
rails plugin new admin --mountable
```

<img v-click src="/img/generate_app.jpg" alt="">
<img v-click src="/img/generate_test.jpg" class="relative transform -translate-y-full">

<!-- We'll run the new plugin command and are going to choose the mountable option.
This way we get the engine, dummy app, version file, the gemspec file, and other goodies we need to for packaging and distribution.

You might wonder what the dummy app does. It's there to help you test your engine. Test it as in automated tests or manual tests. -->

---

# Main functionality

 - find our models
 - display them on the sidebar and create a route for each one
 - show all records in their separate page


<img v-click src="/img/admin_demo.gif" />

<!--
- So let's add some functionality to our engine.

It's going to find our models from the parent app, show them on the sidebar and create links to pages where we quickly display the records.

I'm not going to build the whole admin panel framework here. You can check out Avo by yourselves. It's definitely better than this :P
-->

---

# Test your engine


```ruby
require "test_helper"

module Admin
  class ResourcesControllerTest < ActionDispatch::IntegrationTest
    include Engine.routes.url_helpers

    test "the dashboard loads" do
      get admin.root_path
      assert_response :success
    end
  end
end
```

<img src="/img/test_output.jpg" class="w-128" />

<!-- Let's add a test to make sure nothing breaks when we push changes. -->
---

# Packaging

<img v-click src="/img/rubygems.jpg" alt="">

<!-- Ok. So now we have some functionality to pass around to our fellow developers. What's the easiest way of sending it their way? Make a gem. -->
---

# Gemspec

<div v-click>

```ruby
require_relative "lib/admin/version"
Gem::Specification.new do |spec|
  spec.name        = "admin"
  spec.version     = Admin::VERSION
  spec.authors     = ["Adrian"]
  spec.email       = ["adrian@adrianthedev.com"]
  spec.homepage    = "TODO"
  spec.summary     = "TODO: Summary of Admin."
  spec.description = "TODO: Description of Admin."
  # Prevent pushing this gem to RubyGems.org. To allow pushes either set the "allowed_push_host"
  # to allow pushing to a single host or delete this section to allow pushing to any host.
  spec.metadata["allowed_push_host"] = "TODO: Set to 'http://mygemserver.com'"

  spec.metadata["homepage_uri"] = spec.homepage
  spec.metadata["source_code_uri"] = "TODO: Put your gem's public repo URL here."
  spec.metadata["changelog_uri"] = "TODO: Put your gem's CHANGELOG.md URL here."

  spec.files = Dir.chdir(File.expand_path(__dir__)) do
    Dir["{app,config,db,lib}/**/*", "MIT-LICENSE", "Rakefile", "README.md"]
  end

  spec.add_dependency "rails", ">= 7.0.4"
end
```
</div>
---

# Gemspec

```ruby{all|3-9|11-16|18|20-22|all}
require_relative "lib/admin/version"
Gem::Specification.new do |spec|
  spec.name        = "admin"
  spec.version     = Admin::VERSION
  spec.authors     = ["Adrian"]
  spec.email       = ["adrian@adrianthedev.com"]
  spec.homepage    = "https://avohq.io"
  spec.summary     = "Configuration-based, no-maintenance, extendable Ruby on Rails admin panel framework."
  spec.description = "Avo abstracts away the common parts of building apps, letting your engineers work on your app's essential components. The result is a full-featured admin panel that works out of the box, ready to give to your end-users."

  spec.metadata["homepage_uri"] = spec.homepage
  spec.metadata["bug_tracker_uri"] = "https://github.com/avo-hq/avo/issues"
  spec.metadata["changelog_uri"] = "https://avohq.io/releases"
  spec.metadata["documentation_uri"] = "https://docs.avohq.io"
  spec.metadata["homepage_uri"] = "https://avohq.io"
  spec.metadata["source_code_uri"] = "https://github.com/avo-hq/avo"

  spec.files = Dir["{bin,app,config,db,lib,public}/**/*", "avo.gemspec", "Gemfile", "Gemfile.lock", "Rakefile", "README.md"]

  spec.add_dependency "rails", ">= 7.0.4"
  spec.add_dependency "zeitwerk"
  spec.add_dependency "pundit"
end
```

<!-- <img src="/img/rubygems_sidebar.jpg" v-after class="-top-[500px] left-[400px] right-0 relative"> -->


<!--
Before we go ahead and pakage it, we need to fill in some information in our gemspec. The gemspec file is like the `package.json` file in a npm package.
It holds all the description for the gem and some metadata information required by rubygems to host it on their platform.

In the first section we fill in the basic information like the name, the authors, and description.
Next comest the metadata needed by rubygems and other websites to properly identify your package.
The files option tells the gem tool what files to pack. You might have a node_modules directory inside your project and you probably shouldn't package that.
Last but not least, this is where you specify what dependencies your gem requires. The dependencies declared here are going to be read by the parent application and taken into account when the user runs a `bundle install`.

You also have a `Gemfile`, but that's used only for the dummy app. So if there's anything you need on runtime in the parent app, put it in the `gemspec`.-->
---

# Build the thing

```
bundle exec rails build
```

<img src="/img/rails_build.gif" class="mt-10 ">

<!--
After we finish up updating all that information we can fire up the build command.

This will use the `files` configuration in your `gemspec`, take all of them and bundle them up in a `.gem` file.
-->
---

# Build in isolation

```docker{all|1|3|6-7|9|11-13|15|18|21|23}
FROM ruby:3.1.0

RUN apt-get update -qq && apt-get install -yqq build-essential apt-transport-https apt-utils

# Cache nokogiri
RUN apt-get install -yqq libxml2-dev libxslt1-dev build-essential patch ruby-dev zlib1g-dev liblzma-dev
RUN gem install nokogiri selenium-webdriver ffi ruby-debug-ide tilt

RUN gem install bundler -v 2.3.5

ENV RAILS_ENV=production
ENV NODE_ENV=production
ENV BUNDLE_WITHOUT=development:test

WORKDIR /admin/

# Copy the files
COPY . /admin

# Run bundle install
RUN bundle install --jobs 4 --retry 3

RUN bundle exec rails build
```

<!--
What I like to do is to sometimes build the gem in isolation.

I do that because there might be files in those paths that I don't want to ship with this release, or the compiled assets might contain unwanted changes, the env variables might not be right,  so I prefer to do it in isolation.

I do that using a docker where I start from a ruby image, copy the project, install my dependencies, and compile the assets again. This way the build process becomes idempotent and the resulted artifact is the same for a given state of files.

I use this workflow to publish prerelease versions with quickfixes and tryout features.
-->

---

# Helper script

```bash{all|3|5|6|7|8}
# scripts/build.sh

VERSION=$(bundle exec rails runner 'puts Admin::VERSION')

docker build -t admin-build -f docker/Dockerfile .
IMAGE_ID=$(docker create admin-build)
rm -f ./pkg/latest-admin.gem
docker cp $IMAGE_ID:/admin/pkg/admin-$VERSION.gem ./pkg/latest-admin.gem
```
<!-- The previous step only builds the gem, it doesn't really output anything in the local environment. I have a helper script that does that.

- takes the version
- builds the image
- gets the image id
- removes the old gem if found. I do that in case docker fails and actually doesn't build it right and then I risk uploading the wrong gem file.
- and copy the built gem file from docker to the local environment

- Cool! what's left?
-->
---

# Publish the thing!

<div v-click>

```
gem push --host https://rubygems.org/ ./pkg/latest-admin.gem
```

</div>
<img v-click src="/img/push.webp" class="mt-12">

<!-- Next, you publish that file to rubygems using a `gem push` command. You need to register an account and authenticate your environment with rubygems before pushing.

There it is. You published your work to the world. Now, anyone cam come in and use your thing.

When your developer friend wants to use it they add the gem to their gemfile and mount the engine in their routes file.
Now they can go to that route and use your gem. -->
---

# Good job!

<div class="flex space-x-4">
  <div class="flex items-center ">
    <img v-click src="/img/otter.gif" class="">
  </div>
  <div class="flex items-center ">
    <img v-click src="/img/applause.webp" class="">
  </div>
</div>

<!-- This is cool! You built something that someone else is using. But how do we go about when we want to introduce new features? We do that using versioning. -->

---

# Versioning

<!-- <div class="flex align-center justify-center h-10/12">
  <img src="/img/incoming.webp" class="">
</div> -->

<div v-click>

```diff
# lib/admin/version.rb
module Admin
-  VERSION = "0.1.0"
+  VERSION = "0.2.0"
end
```

</div>

<div v-click>

```
bundle install
```

</div>

<br/>

<div v-click>

### Bump gem

[gregorym/bump](https://github.com/gregorym/bump)

</div>
<!-- The generated plugin comes with a `version.rb` file that is used in the package and read by rubygems when we push it.

In order to update the version you simply need to increment the version number in the version file and run `bundle` to have that reflected in the gemfile lock.

There is the `bump` gem that helps you with this. You run the bump command with a major, minor, or patch argument and it will do it for you. It will bump the version number, run the bundle command and even cut a tag and commit the changes for you if that's what you need.
 -->
---

# Release notes

<img v-click src="/img/release_notes.jpg" alt="">

<!-- It's nice that for every release to have dedicated release notes that outline what changed from one release to another.

There are a few ways of doing that. You can have one large `Changelog.md` file in you repo and fill it in manually with all that happened; you can use a page on your website or docs page; you might use Confluence, or you can use GitHub Releases.

With Avo we use GH releases. We tag each new version with a git tag and create a release on GH with a changelog.
I'll talk more about releases later. -->

---

# Updates workflow


<div v-click>


 - checkout a new branch
 - add your changes and commit
 - push to GitHub and open a PR
 - merge the PR
 - pull the `main` branch
 - bump the version number
 - build the gem and push to rubygems
 - update the changelog

</div>

<img v-click src="/img/spent.webp" alt="">

<!-- It's a bit too much, I know. But don't worry... -->

---

# Automate all the things

<img src="/img/automate.webp" alt="">

<!-- ...we can automate the process.  -->
---

# Github Actions

<img src="/img/github_actions.gif" alt="">

<!-- We're going to use GitHub Actions to do it.

GitHub Actions and other CI systems work by using a yaml file as configuration. You basically tell it when to run ceratin tasks (when you create/update/close PRs, when you push code, on a designated time or on the push of a button), you give it the configuration using environment variables and secrets, and you tell it what to do; what tasks to run.

GA also has a marketplace of pre-made actions ready for you to use like git checkout, setup ruby, add caching layers, label PRs, trigger other automations, and much much more.
 -->
---

# Automate testing

```yaml{all|1|3-9|11|12-20|15-20|21-32|29|34|36-40|42|43|44-48|48|49-50|51-56|57-59|60-64|61|65-71}{maxHeight:'100'}
name: Tests

on:
  pull_request:
    branches:
      - main
  push:
    branches:
      - main

jobs:
  Test:
    strategy:
      matrix:
        ruby:
          - '3.0.3'
          - '3.1.0'
        rails:
          - '6.0'
          - '7.0'
    env:
      RAILS_ENV: test
      PGHOST: localhost
      PGUSER: postgres
      PGPORT: 5432
      POSTGRES_HOST: localhost
      POSTGRES_USERNAME: postgres
      POSTGRES_PORT: 5432
      COVERAGE: ${{ matrix.rails == '7.0' && matrix.ruby == '3.0.3' }}
      RAILS_VERSION: ${{matrix.rails}}
      BUNDLE_GEMFILE: ${{ github.workspace }}/gemfiles/rails_${{ matrix.rails }}_ruby_${{ matrix.ruby }}.gemfile
      BUNDLE_PATH_RELATIVE_TO_CWD: true

    runs-on: ubuntu-latest

    services:
      postgres:
        image: postgres:11.5
        ports: ["5432:5432"]
        options: --health-cmd pg_isready --health-interval 10s --health-timeout 5s --health-retries 5

    steps:
    - uses: actions/checkout@v2
    - name: Set up Ruby
      uses: ruby/setup-ruby@v1
      with:
        bundler: default
        ruby-version: ${{ matrix.ruby }}
    - name: Install PostgreSQL 11 client
      run: sudo apt-get -yqq install libpq-dev
    - name: Bundle install
      run: |
        bundle config path vendor/bundle
        bundle install --jobs 4 --retry 3
        bin/rails db:create
        bin/rails db:migrate
    - name: Run tests
      id: run_tests
      run: bundle exec rspec
    - uses: actions/upload-artifact@v2
      if: steps.run_tests.outcome == 'failure'
      with:
        name: rspec_failed_screenshots
        path: ./spec/dummy/tmp/screenshots
    - name: Generate coverage
      uses: codecov/codecov-action@v1
      if: ${{ env.COVERAGE }}
      with:
        token: ${{ secrets.CODECOV_TOKEN }}
        file: ./coverage/coverage.xml
        name: codecov-umbrella
```

<!-- Now, that we know a little bit about our CI system, let's make sure we run tests on each PR and each push.
_Show the test.yml config_

- explain the file

This is the basic setup. You can go wild after this. Add caching layers for bundler and yarn, install multiple databases, run the tests in matrixes testing against multiple ruby or Rails versions, spit out artifacts with screenshots when tests fail, generate coverage, and much much more.
You can find a few of these enhancements in Avo's test.yml file. I'm happy to help anyone with explaining how they work.

Now, if the tests fail, the jobs shows up as failed on the PR page. -->
---

# Automate linting and code analysis

```yaml{all|1|2|10-16|23-26|all}{maxHeight:'100'}
name: Reviewdog
on: [pull_request]
jobs:
  standardrb:
    name: runner / standardrb
    runs-on: ubuntu-latest
    steps:
      - name: Check out code
        uses: actions/checkout@v1
      - name: standardrb
        uses: SennaLabs/action-standardrb@master
        with:
          github_token: ${{ secrets.github_token }}
          reporter: github-pr-review
          rubocop_version: 0.1.6
          rubocop_flags: --format progress
  eslint:
    name: runner / eslint
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v1
      - run: yarn
      - name: eslint
        uses: reviewdog/action-eslint@v1
        with:
          reporter: github-pr-review
```

<div class="z-50 absolute top-24 w-11/12 h-full">
  <img src="/img/review_warning.jpg" class="absolute inset-auto top-0 z-50 left-0 mt-0 m-auto" v-click>
  <img src="/img/review_suggestion.jpg" class="absolute inset-auto top-0 z-50 left-0 mt-0 m-auto" v-click>
</div>

<!-- Setting up linting and code anaylisys CI jobs. this is being done more easily using a pre-made actions from reviewdog. They have templates for eslint and rubocop and standardrb.
We run this for every PR. -->
---

# Automate PR labeling

<div v-click class="flex mb-4">
  <img src="/img/tag_feature.jpg" class="m-auto">
  <img src="/img/tag_chore.jpg" class="m-auto">
  <img src="/img/tag_fix.jpg" class="m-auto">
  <img src="/img/tag_refactor.jpg" class="m-auto">
</div>

<div v-click>

```yaml{all|2-4|13-15}{maxHeight:'100'}
name: PR Labeler
on:
  pull_request:
    types: [opened]
permissions:
  contents: read
jobs:
  pr-labeler:
    permissions:
      pull-requests: write
    runs-on: ubuntu-latest
    steps:
      - uses: TimonVS/pr-labeler-action@v3
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

</div>

<!-- You might have a small repo or a big one, but the fact of the matter is that things can get messy fast and tagging PRs definitely helps with housekeeping.

We use a popular tagging scheme where we divide PRs into `features`, `fixes`, `chores`, or `refactors`.

The way we automate is with another action called `pr-labeler-action`. It will read the branch name and figure out from there what kind of PR this is.
This way we can build the release notes automatically more easily.

You can go wild and use multiple strategies to read from the PR body, and probably from the commit messages too, but this is what works for us.
-->
---

# Automate the release notes

```yaml{all|2-5|10-15|all}{maxHeight:'100'}
name: Build release notes
on:
  push:
    branches:
      - main
jobs:
  update_release_notes:
    runs-on: ubuntu-latest
    steps:
      - uses: release-drafter/release-drafter@v5
        id: release_drafter
        with:
          config-name: release-drafter.yml
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

<img v-click src="/img/release_notes.jpg" class="-top-[300px] w-[550px] relative">

<!--
You might have guessed it. We're going to use a pre-made action for this step. We'll use the `release-drafter` GH action.

We run this after we merge a PR. It will make a diff between the last release and the current HEAD and identify all the merged PRs. Then it will create a draft release on GH with the PRs neatly separated into categories. This also pulls out the author of the PR and adds a reference to that PR which GH nicely turns into a link.

We also get a nice list with all the contributors for that release. -->
---

# Automate cutting a release

```yaml{all|2-5|9|10-13|14-17|18-19|20-22|23-25|26-38|33|34|35|38-47|48-57}{maxHeight:'100'}
name: Build, Generate Ruby Gem and Release
on:
  push:
    tags:
      - 'v*.*.*'
jobs:
  build:
    steps:
    - uses: actions/checkout@v2
    - name: Set up Ruby
      uses: ruby/setup-ruby@v1
      with:
        ruby-version: 3.1.0
    - name: Bundle install
      run: |
        bundle config path vendor/bundle
        bundle install --jobs 4 --retry 3
    - name: Build gem
      run: bundle exec rails build
    - name: Get gem version
      id: gem_version
      run: echo "::set-output name=tag::$(sed "s|refs/tags/v||" <<< ${{ github.ref }})"
    - name: Get release notes
      id: get_release_notes
      run: echo "::set-output name=release_notes::$(node .github/get-release-notes-from-draft.js ${{github.repository}} ${{secrets.GITHUB_TOKEN}})"
    - name: Create Release
      id: create_release
      if: ${{steps.get_release_notes.outputs.release_notes != ''}}
      uses: actions/create-release@v1
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      with:
        tag_name: ${{ github.ref }}
        release_name: Release ${{ github.ref }}
        body: ${{fromJson(steps.get_release_notes.outputs.release_notes)}}
        draft: false
        prerelease: false
    - name: Upload Release Asset
      id: upload-release-asset
      uses: actions/upload-release-asset@v1
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      with:
        upload_url: ${{ steps.create_release.outputs.upload_url }}
        asset_path: pkg/avo-${{steps.gem_version.outputs.tag}}.gem
        asset_name: avo-${{steps.gem_version.outputs.tag}}.gem
        asset_content_type: application/x-tar
    - name: Publish to rubygems.org
      id: publish-to-rubygems-package-registry
      env:
        RUBYGEMS_TOKEN: ${{ secrets.RUBYGEMS_TOKEN }}
      run: |
        mkdir ~/.gem
        touch ~/.gem/credentials
        chmod 600 ~/.gem/credentials
        echo ":rubygems_api_key: ${RUBYGEMS_TOKEN}" >> ~/.gem/credentials
        gem push --host https://rubygems.org/ ./pkg/avo-${{steps.gem_version.outputs.tag}}.gem
```

<!-- We're getting close to the grand finally. Automating the release process.

So we talked about adding a new feature or fix to the repo using gitflow (branch, fix, commit, pr, merge), we talked about adding tags to PRs, and cutting github releases, and now we're going to put everything together using...  you guessed it GitHub Actions.

So let's go through this action. -->
---

# How can others use it?

<div v-click>

- `bundle add 'admin'`

</div>

<div v-click>

```ruby
# Gemfile
gem 'admin'
```

</div>

<div v-click>

```ruby
# config/routes.rb

Rails.application.routes.draw do
  root "home#index"

  mount Admin::Engine => "/admin"
end
```


</div>

<img v-click src="/img/admin_demo.gif" />

---

# Recap

- what is a Rails engine?
- generating and testing inside the dummy app
- share it using rubygems and configure it using the gemspec file
- push to rubygems
- push updates using versioning
- updates workflow
- automate it using GitHub Actions
  - testing
  - linting & code analysis
  - PR labeling
  - release notes
  - automate the final release

---

# Community

<br>
<br>
<br>
<div class="flex justify-between">
<div v-click>

### Romanian Ruby developers

[rubyromania.ro](https://rubyromania.ro)

<img src="/img/ruby_romania.jpg" class="w-90 sabsolute inset-auto right-12 top-32" />

</div>


<div class="fle" v-click>

<div class="left-0 ml-0">

### Short Ruby Newsletter

<img src="/img/short_ruby_logo.png" class="w-auto absolute inset-auto left-auto right-0 -mt-12 mr-16 w-16" />

[shortruby.com](https://shortruby.com/)

</div>

<img src="/img/short_ruby.gif" class="w-90 absoleute inset-auto left-12 top-64" />

</div>
</div>

---
layout: center
class: text-center
---

# Dziękuję!

<div class="my-10 grid grid-cols-[40px,1fr] w-min gap-y-4">
    <ri-twitter-line class="opacity-50"/>
    <div><a href="https://twitter.com/adrianthedev" target="_blank">adrianthedev</a></div>
    <ri-user-3-line class="opacity-50"/>
    <div><a href="https://adrianthedev.com" target="_blank">adrianthedev.com</a></div>
    <ri-user-3-line class="opacity-50"/>
    <div><a href="https://avo.cool" target="_blank">avo.cool</a></div>
    <ri-user-3-line class="opacity-50"/>
    <div><a href="https://avo.cool" target="_blank">avo.cool/repo</a></div>
    <ri-user-3-line class="opacity-50"/>
    <div><a href="https://shortruby.com" target="_blank">shortruby.com</a></div>
    <ri-user-3-line class="opacity-50"/>
    <div><a href="https://rubyromania.ro" target="_blank">rubyromania.ro</a></div>
</div>
