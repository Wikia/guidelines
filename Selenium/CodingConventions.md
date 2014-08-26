# Wikia Selenium Coding Conventions

This styleguide defines the Selenium coding conventions at Wikia. While it is managed by the Quality Assurance Team, it is here to serve the entire Selenium developer community at Wikia.

## TOC

* [Methods naming](#methods-naming)
  * [click prefix](#click-prefix)
  * [open prefix](#open-prefix)
  * [type prefix](#type-prefix)
  * [select prefix](#select-prefix)
  * [get prefix](#get-prefix)
  * [clear prefx](#clear-prefix)
  * [methods order](#methods-order) 

* [Automated Clicktracking tests](#automated-clicktracking-tests)
  * [add Clicktracking groups](#add-clicktracking-groups)
  * [add tracker installation](#add-tracker-installation)
  * [store events by areas](#store-events-by-areas)
  * [define expected events as Json objects](#define-expected-events-as-Json-objects)

* [General formatting](#general-formatting)
  * [indentation](#indentation) 

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
### select prefix

For each method that select something from dropdown, use "select" prefix.
```java
public void selectPinType() {
	waitForElementByElement(pinCategorySelector);
	Select pinCategorySelectorDropDown = new Select(pinCategorySelector);
	List<WebElement> pinCategoryList = pinCategorySelectorDropDown.getOptions();
	pinCategoryList.get(1).click();
}
```

### get prefix

For each method that return some value from page element, use "get" prefix
```java
public String getAssociatedArticleImageSrc() {
	waitForElementByElement(associatedArticleImage);
	String imageSrc = articleImageUrl.getAttribute("src");
	return imageSrc;
}
```

### clear prefix

For each method that clear some fields, use "clear" prefix
```java
public String clearTitle() {
	waitForElementByElement(titleField);
	titleField.clear();
}
```

### method order

Example:
```java
class Example{
	
	public String clickButton() {
		[...]
	}
	
	public String clickSave() {
		[...]
	}
	
	public String verifyTitle() {
		[...]
	}
	
	public String typeDescription() {
		[...]
	}
}

```

Methods in file should have alphabeticall order. If You want to add next click method You should add it after clickSave method, not in the end of the file. When methods will be in alphabeticall order we can faster find them in big files.


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

Sensibly separate events into areas  

You should create appropriate classes in package: com.wikia.webdriver.Common.Clicktracking.Events;  For example for events that are appropriate for Add Photo Modal, you should create the following class:

```java
public class EventsModalAddPhoto {

	public static JsonObject preview = (...);
	public static JsonObject publish = (...);
	public static JsonObject sourceMode = (...);
	
}		
```

### define expected events as Json objects

Expected event should be a Json object, because all events are Json objects. The clicktracking infrastructure verifies key:value pairs  
For reference about what key:value pairs are expected in the events, check official wikia-wide clicktracking spreadsheet: https://docs.google.com/a/wikia-inc.com/spreadsheet/ccc?key=0AsB-XECKL5EhdDlyTVdOWVM3MmRvcElUWUQ0ZnpVOFE&usp=drive_web

In order to well prepare the expected event:
1 Make sure you know how the expected Json object is structured 
2 Prepare the same structure in Java class for storing events

Eg, This is java representation of Json event that is fired on 'preview' click:

```java
public class EventsArticleEditMode {

	public static JsonObject preview = Json.createObjectBuilder()
			.add("0", Json.createObjectBuilder()
					.add(EventParameter.action.toString(), "click")
					.add(EventParameter.trackingMethod.toString(), "both")
					.add(EventParameter.category.toString(), "editor-ck"))
			.add("1", Json.createObjectBuilder()
					.add(EventParameter.label.toString(), "preview"))
			.build();

}		
```

## General formatting

The following rules have an impact on code readability.

### indentation

Rule for indentation is that each indentation is 1 tab which contains 4 whitespaces

```java
	public void executeScript(String script) {
		JavascriptExecutor js = (JavascriptExecutor) driver;
		js.executeScript(script);
		try {
			Thread.sleep(1000);
		} catch (InterruptedException e) {
			e.printStackTrace();
		}
	}
```
