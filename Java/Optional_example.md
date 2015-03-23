# Examples of using Optional safely

Here is some sample code that uses Optional#isPresent and Optional#get. While the code works fine,
this style of using optionals robs you of one of the advantages of using Optional: having the
compiler enforce whether you are accessing it safely or not.

```java
    Optional<UserPreferences> userPreferences = this.userPreferenceService.lookup(userId);

    if (!userPreferences.isPresent()) {
      Problem response =
          Problem.builder()
              .status(Response.Status.NOT_FOUND.getStatusCode())
              .type(new URI("/user-not-found.html"))
              .title(String.format("No preferences found for user %d", userId))
              .build();

      return
          Response.status(Response.Status.NOT_FOUND)
              .entity(response)
              .type(Problem.MEDIA_TYPE)
              .build();
    }

    return Response.ok(userPreferences.get().getPreferences())
        .build();
```

Here is the same code, rewritten to be type-safe. We use map() to convert our returned value into
a Response object if it exists, and then use orElseGet() to provide the alternative Response if
the preferences were not found.

```java
   return this.userPreferencesService.lookup(userId)
     .map(userPreferences -> Response.ok(userPreferences).build())
   	 .orElseGet(() -> {
      Problem response =
          Problem.builder()
              .status(Response.Status.NOT_FOUND.getStatusCode())
              .type(new URI("/user-not-found.html"))
              .title(String.format("No preferences found for user %d", userId))
              .build();

      return
          Response.status(Response.Status.NOT_FOUND)
              .entity(response)
              .type(Problem.MEDIA_TYPE)
              .build();
     });
```

This could be made clearer by extracting the boiler-plate into a helper function.

```java
   return this.userPreferencesService.lookup(userId)
     .map(userPreferences -> Response.ok(userPreferences).build())
   	 .orElseGet(() -> StandardErrors.notFound(
   	 					new URI("/user-not-found.html"), 
   	 					String.format("No preferences found for user %d", userId)));
```

Be aware, however, that this style may not be applicable in every situation. Java being
what it is, there will be situations where falling back on the unsafe style (especially if
you have multiple Optional values that you want to deal with in different ways) might
lead to clearer code overall.