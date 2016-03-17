# Java style guide

The purpose of this guide is to provide some conventions for working on Java
code within M&S.

## Code guidance

There are a number of existing guidance books and documents for writing good
java code. The general recommendation is to read and follow the advice in the
excellent [Java for small
teams](https://www.gitbook.com/book/ncrcoe/java-for-small-teams).

## Code formatting

As noted in the 'Java for small teams' book, having a [consistent
style](https://ncrcoe.gitbooks.io/java-for-small-teams/content/style/100_use_a_consistent_code_layout.html)
is more important than any particular rules over details of column alignment.

Generally, it is recommended that projects use the [Google style guide for
Java](https://google.github.io/styleguide/javaguide.html) or a small deviation
from it.

For existing projects written using different conventions it is important that
modifications match the style of the surrounding code rather than introducing
new formatting.

## Code checking

The code rules are available as a
[Checkstyle](http://checkstyle.sourceforge.net/) configuration. Checkstyle
support is available in a variety of build and development tools.

The [default ruleset](java-checkstyle.xml) is available from this repository. If
a project uses a different ruleset it should publish this, either within the
project or as an alternative configuration within this repository.

## Build tooling

Projects must include a top level Makefile containing at minimum targets for
`test` and for `build`. This is so it can be built without prior knowledge of
the specific build system used.

For most projects it is recommended that these targets call on to a build script
written in  either Gradle or Maven. If gradle is used then the project must
include a [Gradle
wrapper](https://docs.gradle.org/current/userguide/gradle_wrapper.html) to
reduce the setup burden on the environments where the code will be built.

The build should include a call to the Checkstyle ruleset. It may also call
other static analysis tools such as [PMD](https://pmd.github.io/) or
[FindBugs](http://findbugs.sourceforge.net/)

An [example build script configured for gradle](https://gist.github.com/clarkster/e139320867ed3bdf9fb4).

## Integrated Development Environment Setup

It is recommended that all team developers set up an IDE with the
code rules and validations built in. This should make the rules transparent to
adhere to and allow you to make use of the built in formatting tools.

### Intellij

The recommended setup for Intellij 15 is:

* Install the Checkstyle plugin
* Configure the Checkstyle plugin to point to the project settings as a remote
URL. This will cause any Checkstyle errors to display as errors in the IDE.
* Download these
[Intellij settings](java-intellij-config.xml),
save the file to your
[local library](https://www.jetbrains.com/idea/help/project-and-ide-settings.html),
(`~/IdeaIC15/codestyles` on Mac) and apply as the IDE default.

Note that this settings file is based on one supplied by google, incorporating
changes from an [open PR](https://github.com/google/styleguide/pull/96), and
updating the company package to com.mns.

### Eclipse

The recommended setup for Eclipse is similar:

* Install the Checkstyle plugin for Eclipse
* Configure the plugin to point to the project settings
* Apply [the code formatting profile](https://github.com/google/styleguide/blob/gh-pages/eclipse-java-google-style.xml) to match these settings.

### Git Pre-Commit Hook

An alternative or an addition to running the checks in an IDE is to configure
a client-side
[Pre-Commit Hook](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks) so
that style checks and unit tests are run automatically on `svn commit`

An [example pre-commit hook for a gradle project](https://raw.githubusercontent.com/selesse/git-hooks/master/java/pre-commit-gradle-tests).

Note that this client-side validation is merely a helper to stop accidentally
committing some non-valid code. The main server-side validation is configured
to take place as part of the build.
