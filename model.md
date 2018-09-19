# CakePHP to Laravel Model

- [Base model class](#base-model-class)
- belongsTo
    - [single relation](#belongsto-single-relation)
    - [single relation with the explicit foreign key](#belongsto-single-relation-with-the-explicit-foreign-key)
    - [multiple relations](#belongsto-multiple-relations)
    - [custom relation](#belongsto-custom-relation)
- [Model table name](#model-table-name)
- [Creating a model, assigning values and saving](#creating-a-model,-assigning-values-and-saving)

## Base model class

### CakePHP
```php
App::uses('AppModel', 'Model');

class ApiToken extends AppModel
```

### Laravel
```php
use Illuminate\Database\Eloquent\Model;

class ApiToken extends Model
```

## belongsTo single relation

### CakePHP
```php
public $belongsTo = 'User';
```

### Laravel
```php
public function user()
{
    return $this->belongsTo(User::class);
}
```

## belongsTo single relation with the explicit foreign key

### CakePHP
```php
public $belongsTo = [
    'User' => [
        'foreignKey' => 'updated_by',
        'className' => 'User'
    ]
];
```

### Laravel
```php
public function user()
{
    return $this->belongsTo(User::class, 'updated_by');
}
```

## belongsTo multiple relations

### CakePHP
```php
public $belongsTo = ['Region', 'Company'];
```

### Laravel
```php
public function region()
{
    return $this->belongsTo(Region::class);
}

public function company()
{
    return $this->belongsTo(Company::class);
}
```

## belongsTo custom relation

### CakePHP
```php
public $belongsTo = [
    'CurrencyRate' => [
        'foreignKey' => false,
        'conditions' => ['Price.currency_code = CurrencyRate.code']
    ]
];
```

### Laravel
```php
public function currencyRate()
{
    return $this->belongsTo(CurrencyRate::class, 'currency_code', 'code');
}
```

## Model table name

### CakePHP
```php
public $useTable = 'logs';
```

### Laravel
```php
protected $table = 'logs';
```

## Creating a model, assigning values and saving

### CakePHP
```php
$user = new User; // in model $user = new self();

$user->set(array(
    'name' => "Test name",
    'created_at' => date('Y-m-d H:i')
));

$user->save();
```
### Laravel
```php
$user = new User;

$user->fill([
    'name' => "Test name",
    'created_at' => \Carbon\Carbon::now()
]);

$user->save();
```

