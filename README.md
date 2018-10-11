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

```sql
select * from employees;
```

```php
$employees->find();
```




