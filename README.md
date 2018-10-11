# CakePHP 3 ORM By Example

Quick set of examples about query building in CakePHP 3 ORM

We're using https://github.com/lorenzo/cakephp3-advanced-examples/blob/master/config/Schema/employees.sql.gz

## Load employees table using any of the available methods:

* **Recommended** Using the ModelAwareTrait
* Using available Table associations
* **Last chance** Using the TableRegistry class

## Preconditions

* Let $employees be a `\App\Model\Table\EmployeesTable` instance
* All PHP snippets will return a `\Cake\ORM\Query`

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
---
```php
$employees->find()
    ->select(['first_name', 'last_name']);
```
---
```sql
select * from employees where     (CONCAT(first_name, ' ', last_name) LIKE 'luft aron%') OR
(CONCAT(last_name, ' ', first_name) LIKE 'luft aron%');
```
---
* Expressions based (WIP)
```php
$query = $employees->find();
$concatFirstLast = $query->func()->concat();  
$query->find()
    ->where([
        'OR' => [
            "CONCAT(first_name, ' ', last_name) LIKE" => 'luft aron%',
            "CONCAT(last_name, ' ', first_name) LIKE" => 'luft aron%',
        ]
    ]);
```
---
* Array based, mysql (**not portable**)
```php
$employees->find()
    ->where([
        'OR' => [
            "CONCAT(first_name, ' ', last_name) LIKE" => 'luft aron%',
            "CONCAT(last_name, ' ', first_name) LIKE" => 'luft aron%',
        ]
    ]);
```
