# CakePHP to Laravel

- [Import other classes](#import-other-classes)
- [Exception throwing](#exception-throwing)
- [Authentication checking](#authentication-checking)

## Import other classes

### CakePHP:
```php
App::uses('SaleOrder', 'Model');
```

### Laravel:
```php
use App\Model\SaleOrder; 
```

## Exception throwing

### CakePHP
```php
throw new CakeException('Could not connect to eyesel database');
```

### Laravel
Since CakePHP uses only 1 exception class for error throwing, we need to do some modification in Laravel application. In this case, we have 3  possible ways:

1. Use Laravel built-in exceptions
2. Use other package built on exceptions
3. Create your own exception

Read more about this [here](https://laraveldaily.com/how-to-catch-handle-create-laravel-exceptions/).
```php
throw new DatabaseConnectionError("eyesel");
```

## Authentication checking

### CakePHP

```php
$this->Auth->loggedIn();
```

### Laravel

```php
Auth::check();
```

