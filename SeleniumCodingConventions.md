# Wikia Selenium Coding Conventions

This styleguide defines the Selenium coding conventions at Wikia. While it is managed by the Quality Assurance Team, it is here to serve the entire Selenium developer community at Wikia.

## TOC

* [Methods naming](#Methods-naming)
  * [click prefix](#click-prefix)
  * [open prefix](#open-prefix)
  * [type prefix](#type-prefix)
* [Automated Clicktracking Tests](#Automated-Clicktracking-Tests)
  * [add Clicktracking groups](#add-Clicktracking-groups)
  * [add tracker installation method](#add-tracker-installation-method)

## Methods naming

The following naming rules have an impact on good code quality understanding. They were chosen based on QA Automation Team experience. 

### click prefix

For each method that performs a click on page, use "click" prefix.

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

For each method that opens a new page, use "open" prefix.

```java
public SpecialWikiActivityPageObject openSpecialWikiActivity(String wikiURL) {
	getUrl(wikiURL + URLsContent.specialWikiActivity);
	return new SpecialWikiActivityPageObject(driver);
}
```

### type prefix

For each method that types some content into the page, use "type" prefix.

```java
private void typeCategoryName(String category) {
	waitForElementByElement(addCategoryInput);
	addCategoryInput.sendKeys(category);
}
```

## Automated Clicktracking Tests

The following rules apply when writing Automated Clicktracking Tests

### add Clicktracking groups

For each clicktracking test add 'ClickTracking' group, and use 'ClickTracking' as prefix for the main and test-specific groups 

```java
@Test(groups = {
			"ClickTracking",
			"ClickTrackingSomeAreaTests",
			"ClickTrackingSomeAreaTests_001"
	})
```

### add tracker installation method

For each clicktracking test add trackerInstallation method. 

Remmember to do it after the wiki page you want to test is ready.
This means you shouldn't do it at the very beginning of the test.
This means you shouldn't do it before you are sure that the page is loaded.

You can do that using .executeScript(ClickTrackingScriptsProvider.trackerInstallation) method. This method is available in every PageObject, because it comes from BasePageObject class.

```java
public void ClicktrackingSomeAreaTest_001_verifySomething() {
		...
		visualEditMode.executeScript(ClickTrackingScriptsProvider.trackerInstallation);
		...
}		
```	
```
