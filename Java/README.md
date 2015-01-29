# Wikia's Java Coding Guidelines

Wikia follows [Google guidelines](https://google-styleguide.googlecode.com/svn/trunk/javaguide.html)

With following addition:
 - 100 column limit should be used by all projects, with exceptions noted in Google guidelines.


## InteliJ configuration:

If you are InteliJ user you can use [this configuration file](formatter/intellij-java-google-style.xml) to make it automatically format code conforming to the guidelines.

To make this work:
 - put [this](formatter/intellij-java-google-style.xml) file in your [InteliJ config directory](https://intellij-support.jetbrains.com/entries/23358108-Directories-used-by-the-IDE-to-store-settings-caches-plugins-and-logs).
 - launch InteliJ
 - go to Settings -> Editor -> Code Style -> Java
 - from Scheme combobox choose: GoogleStyle

```bash
	# linux one-liner for installing config file
	curl https://raw.githubusercontent.com/Wikia/guidelines/master/Java/formatter/intellij-java-google-style.xml -o ~/.IntelliJIdea14/config/codestyles/intelij-java-google-style.xml
```


## Eclipse configuration

If you prefer Eclipse then you can use [this configuration file](formatter/java-wikia-style.xml)
