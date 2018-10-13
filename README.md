# CakePHP 3 ORM By Example

Quick set of examples about query building in CakePHP 3 ORM

We're using https://github.com/lorenzo/cakephp3-advanced-examples/blob/master/config/Schema/employees.sql.gz

## Load employees table using any of the available methods:

- **Recommended** Using the ModelAwareTrait
- Using available Table associations
- **Last chance** Using the TableRegistry class

## Preconditions

- Let $employees be a `\App\Model\Table\EmployeesTable` instance
- All PHP snippets will return a `\Cake\ORM\Query`

## Using the console to test the examples

- Create a new project
- Import the dump
- Copy this file (for global options) https://github.com/bobthecow/psysh/wiki/Sample-config into `~/.config/psysh/config.php` (or `C:\Users\{USER}\AppData\Roaming\PsySH\config.php` on Windows)
- Create a new file in `~/.config/psysh/include/bootstrap.php`
  - This is a great place to setup some `class_alias`, or utility methods for your console experience
- Open your CakePHP Console `bin/cake/console`
- Get a reference to employees table using

```php
$employees = \Cake\ORM\TableRegistry::getTableLocator()->get('Employees');
```

# Simple queries

---

```sql
select * from employees;
```

```php
$employees->find();
```

---

```sql
select first_name, last_name from employees;
```

```php
$employees->find()
    ->select(['first_name', 'last_name']);
```

---

```sql
select * from employees where     (CONCAT(first_name, ' ', last_name) LIKE 'luft aron%') OR
(CONCAT(last_name, ' ', first_name) LIKE 'luft aron%');
```

- Expressions based
  - Note we use `$query->identifier()` to mark column names (or 'literal' value in array syntax)
  - Function code is portable vs harder to read
  - Resulting code is reusable, specific complex expressions/functions could be extracted
  - Carefully naming the query parts helps reading the final query

```php
$query = $employees->find();
$concatFirstLast = $query->func()->concat([
    $query->identifier('first_name'),
    "' '" => 'literal',
    $query->identifier('last_name')
]);
$concatLastFirst = $query->func()->concat([
    $query->identifier('last_name'),
    "' '" => 'literal',
    $query->identifier('first_name')
]);
$likeFirstLast = $query->newExpr()->like($concatFirstLast, 'luft aron%');
$likeLastFirst = $query->newExpr()->like($concatLastFirst, 'luft aron%');
$query
    ->where($query->newExpr()->or_([
        $likeFirstLast,
        $likeLastFirst
    ]));
```

- Array based, mysql (**not portable**)

```php
$employees->find()
    ->where([
        'OR' => [
            "CONCAT(first_name, ' ', last_name) LIKE" => 'luft aron%',
            "CONCAT(last_name, ' ', first_name) LIKE" => 'luft aron%',
        ]
    ]);
```

```sql
SELECT 
  e.first_name,
  s.from_date, 
  s.salary
FROM 
  employees AS e
  INNER JOIN salaries AS s ON e.id = (s.employee_id) 
WHERE 
  (
    s.from_date BETWEEN '1985-12-01' 
    AND '1987-12-01' 
    AND s.salary > 60000
  ) 
LIMIT 
  10
```

```php
$employees
    ->select([
        'Employees.first_name',
        'Salaries.from_date',
        'Salaries.salary',
    ])
    ->innerJoinWith('Salaries');
    $between = $employees->newExpr()->between(
        'Salaries.from_date',
        Time::create(1985, 12, 01),
        Time::create(1987, 12, 01)
    );
    $greaterThan = $employees->newExpr()
        ->gt('Salaries.salary', 60000);
    $employees->where([
        $between,
        $greaterThan
    ]);
```
