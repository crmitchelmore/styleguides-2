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

## Integrated Development Environment Setup

Both eclipse and Intellij support a Checkstyle plugin which can be installed and
configured to use the ruleset.
