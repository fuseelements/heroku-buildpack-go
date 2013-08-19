# Heroku Buildpack for Go

This is a [Heroku buildpack][buildpack] for [Go][go] inspired by
<https://github.com/kr/heroku-buildpack-go> but designed to use project and all
dependencies committed into the projects' `go` directory.

## Requirements

1. Your project must have a `go` directory that contains a `src` sub-directory. The
   `src` directory will be the project's `GOPATH` workspace containing the
   project's
source files and the source files for all dependencies.
2. A `.goinstall` files that with one line of space-separated package names.
   This line will be passed as packages argument to the `go install` command.

## Example

Given a the following project file layout:

    .git
    .gitignore
    .goinstall
    Procfile
    go
      src
        myapp
          main.go

The `.gitignore` file should ignore items in the workspace `bin` and `pkg`
directories as well as any embedded VCS directories.

    --- .gitignore ---
    # Go workspace
    .bzr/
    .hg/
    bin/
    pkg/

The `.goinstall` file content would contain the project's single package on one line:

    --- .goinstall ---
    myapp


The `Profile` would contain the heroku procecess type and command. See
<https://devcenter.heroku.com/articles/procfile#declaring-process-types> for
more information.

    --- Profile ---
    web: myapp

You would run the following command to create a heroku app that uses this
buildpack:

    $ heroku apps:create myapp --buildpack https://github.com/fuseelements/heroku-buildpack-go.git

To deploy the app to heroku:

    $ git push heroku master

    ...
    -----> Fetching custom git buildpack... done
    -----> Go project app detected
    -----> Installing Go 1.1.2... done
    -----> Running: go install -tags heroku myapp
    -----> Discovering process types
           Procfile declares types -> web

    -----> Compiled slug size: 1.2MB
    -----> Launching... done, v4
           http://myapp.herokuapp.com deployed to Heroku

## Caveats

This buildpack adds a `heroku` [build constraint][build-constraint], to enable
heroku-specific code. See the [App Engine build constraints
article][app-engine-build-constraints]
for details.

[go]: http://golang.org/
[buildpack]: http://devcenter.heroku.com/articles/buildpacks
[build-constraint]: http://golang.org/pkg/go/build/
[app-engine-build-constraints]: http://blog.golang.org/2013/01/the-app-engine-sdk-and-workspaces-gopath.html
