# Wikia Selenium Coding Conventions

This styleguide defines the Selenium coding conventions at Wikia. While it is managed by the Quality Assurance Team, it is here to serve the entire Selenium developer community at Wikia. 

## TOC

* [Methods naming](#Methods-naming)
  * [click prefix](#click-prefix)
  * [open prefix](#open-prefix)
  * [type prefix](#type-prefix)

## Methods naming

Language rules have an impact on code quality understanding. They were chosen based on QA Automation Team expirience. 

### click prefix 

For each method that performs a click on page, use "click" prefix .

```java
public WikiArticleEditMode clickEditButton() {
	waitForElementByElement(editButton);
	waitForElementClickableByElement(editButton);
	scrollAndClick(editButton);
	PageObjectLogging.log("clickEditButton", "edit button clicked", true, driver);
	return new WikiArticleEditMode(driver);
}
```

### open prefix 

For each method that opens a new page, use "open" prefix .

```java
public SpecialWikiActivityPageObject openSpecialWikiActivity(String wikiURL) {
	getUrl(wikiURL + URLsContent.specialWikiActivity);
	return new SpecialWikiActivityPageObject(driver);
}
```

### type prefix 

For each method that types some content into the page, use "type" prefix .

```java
private void typeCategoryName(String category) {
	waitForElementByElement(addCategoryInput);
	addCategoryInput.sendKeys(category);
}
```
