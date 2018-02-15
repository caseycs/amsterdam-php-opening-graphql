# Adding GraphQL to an existing project 

---

## During next 15 minutes...

Or maybe a bit more ;-)

* About GraphQL: into, examples, use-cases
* Defining protocol
* Implementation: libraries, frameworks integration
* Writing the code: resolvers
* Error handling: format, types, logging
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

Plug into your framework

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

```
use Psr\Http\Message\ServerRequestInterface;

Route::get('/', function (ServerRequestInterface $request) {
    ...
});
```

+++

Custom controller action

#### Facade method

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


---

## Lead time

---

## Questions

This presentation is available on GitHub: https://github.com/caseycs/amsterdam-php-opening-graphql