# Vendor

Vendor copies go dependencies to ./vendor.

## Install

	$ go get github.com/bmizerany/vendor

## Use

Vendor all dependencies for a main package:

	$ vendor ./cmd/myapp

Update an already vendored dependency:

	$ vendor github.com/lib/pq

Got an admin or deploy script with dependencies not in the "main" packages?

	$ vendor admin.go

It just works. That's all.

## Motivation

We wanted a simple vendoring tool. Vendor stays out of our way and makes less
assumptions about what we want and don't want in our vendor directory. Instead
it puts dependencies into our vendor directory when we ask it to. 

Vendor assumes:

* Dependencies are in `GOPATH` before running Vendor

Vendor assumes that you have the dependencies you want to vendor already in your `GOPATH`.

* Dependencies will be copied to `./vendor`

Vendor assumes you're running it in the parent directory of your vendor folder. 
If your vendor folder does not exist it will create it for you in the current directory.

* Dependencies in their working directories are structured in they way you want them to be structured be under `./vendor`

Vendor does not check if there are uncommitted changes in the dependencies' working
directory. The check is costly in terms of time and usually gets in the way
when you're iterating on one package and vendoring it in another to test or experiment.

* Dependencies aren't always `import`ed

We have a script that `go install`s `vendor/github.com/backplaneio/tools/cmd/bpagent`
and runs it for development. Everyone on the team is using the same binary,
guaranteed.

Because this isn't imported, other dependency tools won't allow us to vendor it
and keep it up to date. We resorted to using `cp -R` manually on that package and all of its
dependencies. This was fine the first few times until @voutasaurus threatened to
burn the office down, so I made sure this tool could do it.

* Dependencies that exist in subdirectories of the working directory vendor is run in are to be ignored

Vendor will not vendor anything that is already in the working tree.

* Dependencies are either not versioned or poorly versioned

Vendor assumes that you would rather manage versions of dependencies yourself.

* You're using source control

The rollback function for Vendor is `git checkout -f -- vendor`.
Vendor isn't careful because it assumes you are and can rollback any mistakes
you may make while using vendor. Vendor doesn't make mistakes. :) 

## Goals

* Be fast
* Get out of the way
* Do (only) what is asked of it

