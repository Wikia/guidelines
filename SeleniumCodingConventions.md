# Wikia Selenium Coding Conventions

This styleguide defines the Selenium coding conventions at Wikia. While it is managed by the Quality Assurance Team, it is here to serve the entire Selenium developer community at Wikia. 

## TOC

* [Methods naming](#Methods-naming)
  * [click prefix](#click-prefix)
  * [open prefix](#open-prefix)
  * [type prefix](#type-prefix)

## Methods naming

Language rules have an impact on code quality understanding. They were chosen based on QA Automation Team expirience. 

### click method

For each method that performs a click on page, use "click" prefix .

```java
// not best practice
public WikiArticleEditMode clickEditButton() {
	waitForElementByElement(editButton);
	waitForElementClickableByElement(editButton);
	scrollAndClick(editButton);
	PageObjectLogging.log("clickEditButton", "edit button clicked", true, driver);
	return new WikiArticleEditMode(driver);
}