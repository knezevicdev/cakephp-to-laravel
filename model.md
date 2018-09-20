# CakePHP to Laravel Model

- [Base model class](#base-model-class)
- belongsTo
    - [single relation](#belongsto-single-relation)
    - [single relation with the explicit foreign key](#belongsto-single-relation-with-the-explicit-foreign-key)
    - [multiple relations](#belongsto-multiple-relations)
    - [custom relation](#belongsto-custom-relation)
- [Model table name](#model-table-name)
- [Creating a model, assigning values and saving](#creating-a-model,-assigning-values-and-saving)
- [Data validation](#data-validation)
- [Casting data from model](#casting-data-from-model)
- [saveMany and saveAll function](#savemany-and-saveall-function)

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
$user = new User(); // in model $user = new self();

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

## Data validation

### CakePHP
```php
public $validate = [
    'first_name' => 'notEmpty',
    'last_name' => 'notEmpty',
    'address' => 'notEmpty',                                
    'city' => 'notEmpty',
    'idcard_number' => [
        'notEmpty',
        'unique' => [
        "rule" => ["checkUnique", ["idcard_number", "region_id"]],
            "message" => "An ID card number already exists for that country"
        ]
    ]
];
```

### Laravel
Model data validation like one in CakePHP is not implemented. Data validation is separated from the model, so you need to take care of it at other levels. (Services/Controllers/Requests/etc.)

## Casting data from model

### CakePHP
```php
function getDiscount() {
    return (float)$this->getData('discount');
}


function getIsAdmin() {
    return (float)$this->getData('is_admin');
}
```

### Laravel
```php
protected $casts = [
    'discount' => 'float',
    'is_admin' => 'boolean'
];
```

## saveMany and saveAll function

### CakePHP
```php
$categories = [
    [
        'Category' => [
            'code' => $category_code_1,
            'type' => $category_type_2,
            'name' => $category_name_3,
        ]
    ],
    [
        'Category' => [
            'code' => $category_code_2,
            'type' => $category_type_2,
            'name' => $category_name_2,
        ]
    ]
];

$category = new Category();

$category->saveMany($categories); // or $category->saveAll($categories);
```

### Laravel
```php
$categories = [
    [
        'code' => $category_code_1,
        'type' => $category_type_2,
        'name' => $category_name_3,
    ],
    [
        'code' => $category_code_2,
        'type' => $category_type_2,
        'name' => $category_name_2,
    ]
];

foreach($categories as $category) {
    Category::create($category);
}
```

## Model events

### CakePHP
```php
function beforeSave($options = []) {
    $this->data['User']['first_name'] = formatCapitalFirstLetters($this->data['User']['first_name']);

    return true;
}

function afterSave($created = false, $options = [])
{
        $log = new CrawlerLog();
        if ($created) {
            $action = 'Create';
        } else {
            $action = 'Update';
        }
        $log->set(array(
            'model' => $this->name,
            'model_id' => $this->data[$this->name]['id'],
            'user_email' => AuthComponent::user('email'),
            'action' => $action,
            'created_at' => date('Y-m-d H:i'),
        ));
        $log->save();
}
```

### Laravel
```php
// php artisan make:observer UserObserver --model=User

class UserObserver
{
    // before functions - creating, updating, saving, deleting, restoring
    // after functions - retrieved, created, updated, saved, deleted, restored

    public function saving(User $user) {
        $user->first_name = formatCapitalFirstLetters($user->first_name);

        return true;
    }

    public function created(User $user) {
        $this->saveLog($user, "Create");
    }

    public function updated(User $user) {
        $this->saveLog($user, "Update");
    }

    public function saveLog(User $user, $action) {
        $log = new CrawlerLog();
        $log$user->fill([
            'model' => "User",
            'model_id' => $user->id,
            'user_email' => $user->email),
            'action' => $action,
            'created_at' => \Carbon\Carbon::now()
        ]);
        $log->save();
    }
}

// The observer needs to be set at App\Providers\AppServiceProvider

class AppServiceProvider extends ServiceProvider
{
    public function boot()
    {
        User::observe(UserObserver::class);
    }

    public function register()
    {
        //
    }
}
```
