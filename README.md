Version Buildpack
==================

As a developer,  
I want to display the source version of my app  
so that the team can refer to it for QA.

## Background

When you push your source to Heroku, the repo is used for slug compilation and then purged before the app is deployed. Unfortunately this prevents you from using something like the following in your app code:

	version = `git rev-parse --short HEAD`

So, let's capture the information during slug compilation. Heroku provides the SOURCE_VERSION environment variable as a nice SCM-agnostic way to do this.

## Usage

The buildpack uses SOURCE_VERSION to compile a [~/.profile.d](https://devcenter.heroku.com/articles/buildpack-api#default-config-values) script for your app environment. For example, if your HEAD commit is 2745779, the resulting script will be:

	~/.profile.d/version.sh

	# default VERSION to build source version
	export VERSION=\${VERSION:2745779}

The expansion syntax ensures that any existing value is not overidden. For better integration with other mechanisms that might control version, the runtime variable is named VERSION rather than SOURCE_VERSION.

Within your app code, you can now easily get your source version from the environment. For easy portablity between Heroku and your dev environment, you can even cascade the initialization:

	version = ENV['VERSION'] || `git rev-parse --short HEAD`.presence || '1.0'

## Links

- [Heroku Buildpacks](http://devcenter.heroku.com/articles/buildpacks)
- [Buildpack API](https://devcenter.heroku.com/articles/buildpack-api)
- [Default Config Values](https://devcenter.heroku.com/articles/buildpack-api#default-config-values)