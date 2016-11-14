# lein-git-version

I'm a big fan of DRY.  I already have version information in git for
my projects.  Remembering to maintain that information in my
project.clj is a PITA.  So... Here's a Leiningen plugin to obtain a
version identifier from git, using:

    git describe --match 'v*.*' --abbrev=4 --dirty=**DIRTY**

It uses annotated git tags (those set with `git tag -a`) as the
authoritative source of version information for your project.

It then substitutes that version into the project on the fly, so every
lein task that uses the project version sees the one obtained from
git.

In addition, it injects a file into
`project`/src/`project`/version.clj, containing a def for the version.

## Installation

Add: 

    ;; If :git-version isn't present in map, lein git-version does nothing
    :git-version {}

    ;; Add in the git-version plugin
    :profiles {:dev {:plugins [[org.clojars.cvillecsteele/lein-git-version "1.1.0"]]}}

to your `project.clj`.

## Usage

The version string set in your project.clj will be ignored, and all
lein tasks relying on it will instead see the git-ified version.

For example, with a project.clj that looks like this:

    (defproject nifty "bLAH BLaH"
      :description "Do nifty things"
      :git-version {}
      :profiles {:dev {:plugins [[org.clojars.cvillecsteele/lein-git-version "1.1.0"]]}}
      ...)

You can do this:      

    $ git tag -a -m "First version!" v1.0.0
    $ lein git-version
    1.0.0
    $ lein jar
    Created /Users/colinsteele/Projects/nifty/target/nifty-1.0.0.jar
    $ cat src/nifty/version.clj
    ;; Do not edit.  Generated by lein-git-version plugin.
    (ns nifty.version)
    (def timestamp 1479069352)
    (def version "1.0.0")
    (def gitref "04fc892e0cf9c8e993a5876c43f51988a653da38")
    (def gitmsg "commit 04fc892e0cf9c8e993a5876c43f51988a653da38
    Author: Colin Steele <cvillecsteele@gmail.com>
    Date:   Sun Nov 13 15:35:52 2016 -0500

        First version!")

## Customization

The `:git-version` map can have several entries:

    :git-version {:root-ns "my-namespace"
                  :path "some/other/place"
                  :version-cmd "git describe --match v*.* --abbrev=4 --dirty=**DIRTY**"
                  :ref-cmd "git rev-parse --verify HEAD"
                  :msg-cmd "git log -1 HEAD"
                  :ts-cmd "git log -1 --pretty=%ct"
                  :tag-to-version #(apply str (rest %))}

Each can be changed to customize what the plugin saves into
`version.clj`. The `-cmd` entries are split on the space character, so
don't have any extraneous ones lying around.

## License

Copyright © 2016 Colin Steele

Distributed under the Eclipse Public License, the same as Clojure.
