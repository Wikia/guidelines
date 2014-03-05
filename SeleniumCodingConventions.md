# Wikia Selenium Coding Conventions

This styleguide defines the Selenium coding conventions at Wikia. While it is managed by the Quality Assurance Team, it is here to serve the entire Selenium developer community at Wikia.

## TOC

* [Methods naming](#methods-naming)
  * [click prefix](#click-prefix)
  * [open prefix](#open-prefix)
  * [type prefix](#type-prefix)
* [Automated Clicktracking tests](#automated-clicktracking-tests)
  * [add Clicktracking groups](#add-clicktracking-groups)
  * [add tracker installation](#add-tracker-installation)
  * [store events by areas](#store-events-by-areas)

## Methods naming

The following naming rules have an impact on good code quality understanding. They were chosen based on QA Automation Team expirience. 

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

## Automated clicktracking tests

The following rules apply to writing Automated Clicktracking Tests.
Explanantion of clicktracking can be found on QA internal page](https://internal.wikia-inc.com/wiki/Automated_Clicktracking_Tests) about Automated Clicktracking Tests. [

### add clicktracking groups

For each clicktracking test add 'ClickTracking' group, and use 'ClickTracking' as prefix for the main and test-specific groups 

```java
@Test(groups = {
			"ClickTracking",
			"ClickTrackingSomeAreaTests",
			"ClickTrackingSomeAreaTests_001"
	})
```

### add tracker installation

For each clicktracking test add trackerInstallation method. 

Remmember to do it after the wiki page you want to test is ready.
This means you shouldn't do it at the very beginning of the test.
This means you shouldn't do it before you are sure that the page is loaded.

You can do that using .executeScript(ClickTrackingScriptsProvider.trackerInstallation) method. This method is available in every PageObject, because it comes from BasePageObject class.

```java
public void ClicktrackingSomeAreaTest_001_verifySomething() {
		...
		somePageObject.executeScript(ClickTrackingScriptsProvider.trackerInstallation);
		...
}		
```	

### store events by areas

Take time to sensibly separate events into areas  

You should create appropriate classes in package: com.wikia.webdriver.Common.Clicktracking.Events;  For example for events that are appropriate for Add Photo Modal, you should create the following class:

```java
public class EventsModalAddPhoto {

	public static String eventFlickrLinkClick = "photo-tool-find-flickr";
	public static String eventThisWikiLinkClick = "photo-tool-find-this-wiki";
	public static String eventFindButtonClick = "photo-tool-button-find";
	public static String eventUploadButtonClick = "photo-tool-button-upload";
	public static String eventAddRecentPhotoClick = "photo-tool-add-recent-photo";

}		
```
