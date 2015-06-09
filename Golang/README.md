# Wikia's Golang Coding Guidelines

This guidelines are new and subject to change. Our organizational experience
with go is limited.

First and foremost, read [effective
go](https://golang.org/doc/effective_go.html). Most questions about how to
program in go are addressed there.

## Syntax and Formatting

Use the [`gofmt` command](https://golang.org/doc/effective_go.html#formatting).
It will format your code to community standard for you.

## Coding Conventions

### Program to Interfaces

Program to an interface, not an implementation. Here is an
example:

```go
type MediaWikiGateway interface {
   GetArticle(articleId int) (*ArtileValue, error)
}

type ArticleServiceValue struct {
  mediaWikiGateway MediaWikiGateway
}

func NewArticleService(gateway MediaWikiGateway) *ArticleServiceValue {
   return &ArticleServiceValue{gateway}
}

func (articleService *ArticleServiceValue) ProcessArticle(articleId int) {
   article, error := articleService.getArticle(articleId)
   // do something with the article
}
```

This allows you to mock any functionality provided by the `gateway` in the
article service making it easier to test while also enabling dependency
injection.

### Immutability

Mutable objects and programs that mutate object state are harder to reason
about, test, and debug. For domain or value objects (e.g. users) favor immutable objects over mutable
ones. 

There may be limited language support for immutability. However, we can use some
conventions to help enforce immutability in our programs.

Here is an example:

```go
type UserValue struct {
   username string
   email string
}

type User interface {
   Username() string
   Email() string
}

func NewUser(username, email string) *UserValue {
  return &UserValue{username, email}
}

func (user UserValue) Username() string {
  return user.username
}

func (user UserValue) Email() string {
   return user.email
}
```

Note that in the example above none of the `UserValue` methods mutate the state
of the struct nor can they since the receiver is not a pointer. Note also that
there are no `Set` methods. The value is only created with the `NewUser`
function and no means for mutation is provided. 

See also [Getters](https://golang.org/doc/effective_go.html#Getters) from effective go.
