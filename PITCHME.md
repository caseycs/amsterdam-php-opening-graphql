# Adding GraphQL to an existing project 

Ilia Kondrashov

---

## During next 15 minutes...

Or maybe a bit more ;-)

* About GraphQL: into, examples, use-cases
* Defining protocol
* Implementation: libraries, frameworks integration
* Writing the code: resolvers, middleware
* Error handling: format, types, logging
* Testing, security, tooling
* Lead time: how long it will take
* Questions

---

## Few words about myself

Ilia Kondrashov

I’m doing web development since ~2003

Development, infrastructure, product

---

## About GraphQL

* New REST
* Strictly typed
* Self-documented (including comments and deprecation marks)

+++

### Examples

```
schema {
  query: Query
}
type Query {
  pet: Pet
}
type Pet {
  name: String
}
```

+++

### Comments and deprecations

```
type Query {
  petty: Petty! @deprecated(reason: "Please use petNew instead")
  # Free text comment!
  pet: Pet!
}
```

+++

### Scalar types, required flag and composition 

```
type Pet {
  name: String!
  count: Integer
  isActive: Boolean!
  sibling: Pet
  children: [Pet]!
}
```

+++

### Use-cases

* External API
* Front-end, especially - fully separated ones, since Twig and whole back-end templating is slowly dying

### Who are using GraphQL?

* Facebook
* GitHub 
* Coursera
* Yelp
* ...

---

## Defining protocol

* Separating entities
* Different point of view, then database tables and Doctrine models



+++ 

### Enums

No more pain to understand all possible values:

```
type Schedule {
  weekdays: Weekday!
  ...
}
enum Weekday {
  FRIDAY
  MONDAY
  ...
}
```

+++ 

### Queries

```
type User {
  post(filter: PostFilter!): Post!
}
input PostFilter {
  uuid: Uuid
  slug: String
}
type Post {
  uuid: Uuid
}

```

+++

### Mutations

```
authLoginViaEmail(input: AuthLoginViaEmailInput): AuthLoginViaEmailPayload
input AuthLoginViaEmailInput {
  email: String!
  password: String!
}
type AuthLoginViaEmailPayload {
  viewer: Viewer!
}
type Viewer {
  uuid: Uuid!
}

```

+++

### Custom scalar types

Allows "on-the-fly" data conversion: both incoming, and resulting.

Examples: 

* Date
* DateTime
* Uuid

So your resolvers can operate with Carbon and UuidInterface.

---

## Libraries

What open-source community will bring to us

+++ 

### Github

![GitHub](assets/github.png)

+++

### Packagist

![Packagist](assets/packagist.png)

---

### Serving requests

We already have a frameworks

* Bullt-in server
* Facade method

+++

#### PSR-7 Server

Provides standalone server, which you can plug into your framework (Laravel, Symfony, Slim, Zend Expressive)

Laravel example:

```
use Psr\Http\Message\ServerRequestInterface;
Route::get('/', function (ServerRequestInterface $request) {
    return app(GraphQL\Server\StandardServer::class)
      ->executePsrRequest($request)
});
```

* Looks simple
* Works out of the box

+++

#### Facade method

Which you can inside regular controller action method:

```
$result = \GraphQL\GraphQL::executeQuery(
    $schema, $queryString, $rootValue, $context, $variables
);
```

* Simpler to debug & fine-tune
* Allows more customisation
* Can be integrated with whatever framework 

---

## Describing protocol

+++

### PHP code

+++ 

### Schema file

---

## Writing resolvers

Out-of-the box approach

+++

### Array resolvers

+++

### Closure resolvers

+++

### Circular dependencies

In order to instantiate resolvers regular DI will fail

```
type User {
  lastPost: Post!
}
type Post {
  user: User!
}
```

```
ArrayResolver\User
-> ClosureResolver\Post
-> ArrayResolver\Post
-> ArrayResolver\User
```

+++

### ProxyManager

OOP Proxy wrappers utilities - generates and manages proxies of your objects 

![Proxy Manager](assets/proxy-manager.png)

https://github.com/Ocramius/ProxyManager

+++

### Fields&relations visibility/restrictions

We have guests, users and admins.

It's very convenient to use GraphQL for all of them: simple, no code duplication.
However extra attention which field to show to whom is important. 

+++

Two approaches: fake/downgrade or throw error (like `permissionDenied`).

* Mark fields as required according to the real data model, and return empty value (empty string, -1, beginning of the century for the date) on user permissions mismatch
* Always "downgrade" related data queries according to user permissions
* Return `permissionDenied` application error only for definitely invalid use-cases, like filter enum value which is available only for owners.

---

## Writing middleware

No middleware support our of the box, but you can emulate them by wrapping resolvers into closures.

Example use-case: analyse resolver interface to extract permission check:

```
interface ResolverInterface
{
    public function resolve(array $root, array $args,
    Context $context);
}

interface AuthorizedResolverInterface
interface AdminResolverInterface
```
---

## Error handling

* What do we return to the client
* Types of errors
* Logging

+++

### Error format

### Handling multiple errors (validation)

* Multiple errors per request
* Nested errors

+++

### Types of errors

* Errors that we expose the client
* All other application errors

+++

### Logging

We do handle all the exceptions, yes. However we still need to log them.

---

## Testing

You will have to write custom PHPUnit abstraction layer. In our case:

```
assertQuerySuccess(Query $query, array $variables = []
  ): GraphQLResponse
assertQueryFail(Query $query, array $variables = []
  ): GraphQLResponse
assertQueryErrors(string ...$expectErrors): void
```

+++

### PHPUnit helper calls

Handy to have helper methods for every request:

```
mutationXxxxYyyy(
  array $variables,
  string $returnFields = '{default{fields}}'
): Query
queryXxxxYyyy(
  array $variables,
  string $returnFields = '{default{fields}}'
): Query
```

+++

### Test example

Faker to generate variables as much as possible, but allowing to overwrite them via `$variables`

```
public function testRegistrationSuccess()
{
  $mutation = $this->mutationAuthRegister(
    [],
    '{viewer{user{firstName}}}'
  );
  $this->assertQuerySuccess($mutation);
  $this->assertSame(
    $mutation->vars->firstName,
    $this->responseData['viewer.user.firstName']
  );
}
```

---

## Security

* Disable introspection on production
* Use CSRF token
* Limit maximal queries complexity
* Hide your back-office JS from public access

---

## Tooling

### Schema browser

[ChomeiQL](https://chrome.google.com/webstore/detail/chromeiql/fkkiamalmpiidkljmicmjfbieiclmeij?hl=en)

![GitHub](assets/chromeiql.png)

+++

### Allows you to add new call

[GraphQL Faker](https://github.com/APIs-guru/graphql-faker)

![GitHub](assets/chromeiql.png)

Proxies your current endpoint and allows you to add new queries and fields to existing ones.

--- 

## Lead time

* Learning curve: 1 week for playground and experiments
* First prototype: 1 week for basic integration
* First real use-case: 2 weeks before going live, including alignment with the front-end team

---

## Questions

Feel free to ask!

This presentation is available on GitHub

https://github.com/caseycs/amsterdam-php-opening-graphql