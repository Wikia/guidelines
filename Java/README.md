# Wikia's Java Coding Guidelines

All code should be developed for, and compiled with Java 8.

Our standard package prefix is com.wikia

## Syntax and Formatting

Wikia follows [Google guidelines](https://google-styleguide.googlecode.com/svn/trunk/javaguide.html)

With following addition:
 - 100 column limit should be used by all projects, with exceptions noted in Google guidelines.

## Coding Conventions

In addition to the conventions described in the Google standards, 

### Avoid checked exceptions

Checked exceptions are an interesting experiment that didn't really work out.

The only exception (sorry) to this rule is if you are wrapping or extending some standard Java functionality that throws one of the well-known checked exceptions (IOException, JDBCException, etc) and you want to propagate that exception to callers that are already probably being forced to handle those exceptions by the standard library.

### Do not return null or accept null as an argument

If you want to represent a value that may or may not be present, make it explicit by using java.util.Optional.

Avoid using Optional.get() and Optional.isPresent(). Prefer map() and flatMap() for mutating optionals, and orElseGet() and orElseThrow() for flattening them.

For an example of how this works, see the [Optional Examples](Optional_example.md)

## API Conventions

In addition to other conventions, the following rules apply to APIs that are to be exposed to third party developers (i.e. through being open sourced).

### No dependencies on third-party classes

Public APIs should only expose classes or methods controlled by Wikia, or that are part of the Java standard library. Exposing third-party classes or methods in APIs will inevitably make it impossible to upgrade the third-party library without breaking the API.

## Standard Libraries/Tools

* *Logging:* [SLF4J](http://www.slf4j.org) is the standard logging API. java.util.logging should be avoided if possible. 
* *Collections:* [Guava](https://github.com/google/guava) is the standard "collections enhancement" API, and should be used in preference to commons-collections.
* *Unit Testing:* [Junit 4](http://junit.org) for testing, [Mockito](http://mockito.org) for mock objects.
* *Building:* [Gradle](https://gradle.org)

## IDE Configurations

### InteliJ configuration:

If you are InteliJ user you can use [this configuration file](formatter/intellij-java-google-style.xml) to make it automatically format code conforming to the guidelines.

To make this work:
 - put [this](formatter/intellij-java-google-style.xml) file in your [InteliJ config directory](https://intellij-support.jetbrains.com/entries/23358108-Directories-used-by-the-IDE-to-store-settings-caches-plugins-and-logs).
 - launch InteliJ
 - go to Settings -> Editor -> Code Style -> Java
 - from Scheme combobox choose: GoogleStyle

```bash
	# linux one-liner for installing config file
	curl https://raw.githubusercontent.com/Wikia/guidelines/master/Java/formatter/intellij-java-google-style.xml -o ~/.IntelliJIdea14/config/codestyles/intelij-java-google-style.xml
	# OS X one-liner. Replace IntelliJIdea14 with the version of IntelliJ you are using
	curl https://raw.githubusercontent.com/Wikia/guidelines/master/Java/formatter/intellij-java-google-style.xml -o ~/Library/Preferences/IntelliJIdea14/codestyles/intellij-java-google-style.xml
```

### Eclipse configuration

If you prefer Eclipse then you can use [this configuration file](formatter/java-wikia-style.xml)
