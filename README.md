Source Version Buildpack
==================

As a developer,  
I want to display the source version of my app  
so that the team can refer to it for QA.

## Background

When you push your source to Heroku, the repo is used for slug compilation and then purged before the app is deployed. Unfortunately this prevents you from using something like the following in your app code:

	version = `git rev-parse --short HEAD`

So, let's capture the information during slug compilation. Heroku provides the SOURCE_VERSION environment variable as a nice SCM-agnostic way to do this. We can use a buildpack to create a [profile.d](https://devcenter.heroku.com/articles/profiled) script that will export SOURCE_VERSION into the app runtime environment.

## Usage

First thing, add the buildpack to your app:

	$ heroku buildpacks:add https://github.com/ianpurvis/heroku-buildpack-version

During your next deployment, the buildpack will generate the environment configuration script:

	-----> Fetching custom git buildpack... done
	-----> Source app detected
	-----> Creating profile.d script
	       SOURCE_VERSION: d9f63da90bc76ee878a0e9a54d9e85db7da4a52b
	       Script installed to .profile.d/source_version.sh

The script itself is simple, just a single export statement with default expansion: 

	export SOURCE_VERSION=${SOURCE_VERSION:-f5efc0615dbd0f64f718e142bad858b8e1cf59bb}

Note the expansion syntax ':-' ensures that any existing value is not overidden.

In dev environments, scripting `git rev-parse` is still probably the most convenient way to get your current source version. For easy portablity, you can cascade the initialization:

	version = ENV['SOURCE_VERSION'] || `git rev-parse --short HEAD`.presence || '1.0'

## Links

- [Heroku Buildpacks](http://devcenter.heroku.com/articles/buildpacks)
- [Buildpack API](https://devcenter.heroku.com/articles/buildpack-api)
- [Default Config Values](https://devcenter.heroku.com/articles/buildpack-api#default-config-values)