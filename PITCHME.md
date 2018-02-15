# Adding GraphQL to an existing project 

---

## During next 15 minutes...

Or maybe a bit more ;-)

* About GraphQL: into, examples, use-cases
* Defining protocol
* Implementation: libraries, frameworks integration
* Writing the code: resolvers, middleware
* Error handling: format, types, logging, testing, security
* Lead time: how long it will take
* Questions

---

## Few words about myself

---

## About GraphQL

Strictly typed

+++

### Examples

+++

### Use-cases

* External API
* Separate front-end

+++

### Who is using GraphQL?


---

## Defining protocol

* Separating entities
* Different point of view, then database tables and Doctrine models

+++ 

### Enums

+++ 

### Queries

+++

### Mutations

+++

### Custom scalar types

Examples: 

* Date
* DateTime
* Uuid

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

Provides standalone server

```
class GraphQL\Server\StandardServer
{
    public function processPsrRequest(
        Psr\Http\Message\ServerRequestInterface $request,
        Psr\Http\Message\ResponseInterface $response,
        Psr\Http\Message\StreamInterface $writableBodyStream
    ): Psr\Http\Message\ResponseInterface
}
```

Which you can plug into your framework (Laravel, Symfony, Slim, Zend Expressive)

```
use Psr\Http\Message\ServerRequestInterface;

Route::get('/', function (ServerRequestInterface $request) {
    ...
});
```

* Simple
* Works out of the box

+++

#### Facade method

Which you can inside regular controller action method:

```
$result = \GraphQL\GraphQL::executeQuery(
    $schema, 
    $queryString, 
    $rootValue = null, 
    $context = null, 
    $variableValues = null, 
    $operationName = null,
    $fieldResolver = null,
    $validationRules = null
);
```
* Can be integrated with whatever framework 
* Allows customisation

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

![GitHub](assets/proxy-manager.png)

https://github.com/webonyx/graphql-php

### Fields visibility

We have users, 

---

## Writing middleware

```
AuthorizedResolverInterface
AdminResolverInterface
```
---

## Error handling

* What do we return to the client
* Types of errors
* Logging

+++

### Error format

Handling multiple errors (validation)

* Multiple errors per request
* Nested errors

+++

### Types of errors

* Errors that we expose the client
* All other application errors

+++

### Logging

We do handle all the exceptions, yes. However we still need to log them.

### Testing framework

You will have to write custom PHPUnit abstraction layer. In our case:

```
function assertQuerySuccess(Query $query, array $variables = []): GraphQLResponse
function assertQueryFail(Query $query, array $variables = []): GraphQLResponse
function assertQueryErrors(string ...$expectErrors): void
```

### Testing helper calls

Handy to have helper methods for every request:

```
function mutationXxxxYyyy(array $variables, string $returnFields = '{default{fields}}'): Query
function queryXxxxYyyy(array $variables, string $returnFields = '{default{fields}}'): Query
```

Faker to generate variables as much as possible, but allowing to overwrite them via `$variables`

Test example:

```
public function testRegistrationSuccess()
{
  $mutation = $this->mutationAuthRegister(
    [],
    '{viewer{user{firstName}}}'
  );
  $this->assertQuerySuccess($mutation);
  $this->assertSame($mutation->vars->firstName, $this->responseData['viewer.user.firstName']);
}
```
+++

### Security

* Disable introspection on production
* Limit maximal queries complexity
* Hide your back-office JS from public access


---

## Lead time

Warning! Very broad assumptions!

* Learning curve: 1 week for playground and experiments
* Prototype: 1 week for basic integration
* First real use-case: 2 weeks all around, including alignment with the front-end team

---

## Benefits

* Separate front-end - Twig and other back-end templating are dying
* Full-fletch testing scenarios - starting from registering users

---

## Questions

This presentation is available on GitHub: https://github.com/caseycs/amsterdam-php-opening-graphql