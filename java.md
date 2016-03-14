# Java style guide

The purpose of this guide is to provide some conventions for working on
Java code within M&S.

## Code guidance

There are a number of existing guidance books and documents for writing good
java code. One excellent recommended resource is the [Java for small teams](https://www.gitbook.com/book/ncrcoe/java-for-small-teams/details).

## Code formatting

Generally, it is recommended that projects use the [Google style guide for Java]
(https://google.github.io/styleguide/javaguide.html)

This style is not mandatory, but what is mandatory that a new project must
always apply a consistent style and provide the tooling and documentation to
make it trivial for developers to follow that style.

For existing projects written using different conventions it is important that
modifications match the style of the surrounding code rather than introducing
new formatting.

## Code checking

The code rules are available as a [Checkstyle](http://checkstyle.sourceforge.net/)
configuration. Checkstyle support is available in a variety of build and
development tools.

This Checkstyle configuration is based on the Google style guide, apart from
some minor changes such as the company name used for import statement ordering.
The

These Checkstyle rules should be added to the build script of each project.
The file reports violations at 'error' level which mean that any violation
should fail the build.

## Build tooling

Projects must include a top level Makefile containing at minimum targets for
`test` and for `build`. This is so it can be built without prior knowledge of
the specific build system used.

For most projects it is recommended that these targets call on to a build
script written in  either gradle or maven. If
gradle is used then the project must include a [Gradle wrapper]
(https://docs.gradle.org/current/userguide/gradle_wrapper.html) to reduce the
setup burden on the environments where the code will be built.

The must include a call to the Checkstyle

## Integrated Development Environment setup

https://gist.github.com/gkzhong/3916852
