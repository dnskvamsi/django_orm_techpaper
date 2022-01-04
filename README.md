# Django ORM

### ORM

Object-relational Mapper. It takes a python object and it creates a relational mapping to the database.With the help of this we can interact with database using python.

### Django Shell

Django shell is a python shell that gives access to the database. Using the shell we can perform queries on the database.

Command to enter to shell is

```
python3 manage.py shell
```

### ORM Queries in Django

#### all()
To retrive all the objects from the table we use all() method.
Eg:-
```
Match.objects.all();
```

Here Match is the table name in the database. It is similar to 
```
SELECT * FROM Match;
```

The all() method returns a QuerySet of all the objects in the database.

#### filter()

It is used to retrive only specific objects based on the condition which is specified.
It is simiar to **WHERE** clause in SQL.

Eg:-

```
Match.objects.filter(season=2016);
```
The above command returns only the Matches data where season is 2016. (To retrive data in the season 2016).

We can perfrom many operations inside the filter method such as

* __startswith :- it returns a QuerySet that contains data that start with condition mentioned.
  
  Eg:- team__startswith = "Sun"

  The final result is a QuerySet containing all entries with a team that starts with “Sun”.such sunrisers and so on.

* __endswith :- It returns a QuerySet conatins data that ends with condition mentioned.
  
  Eg:- team__endswith = "kings"

  The final result is a QuerySet containing all entries with a team that ends with “kings”.such chennai super kings  and so on.

* __lte :- less than or equal to.
* __gte :- greater than or equal to.
* __exact :- matches the object with exact form mentioned.
* __iexact :- It  Matches the object by ignoring the case sensitivity.

* isnull = True :- it returns only the objects which has null values.
  Eg:-
```
  Match.objects.filter(umpire2__isnull=True);
```
It returns the object where umpire2 column has null values in the database.

**AND (&)**

To check for multiple conditions we use and operator.

Eg:-
```
Match.objects.filter(team1="sunrisers") & Model.objects.filter(winner="sunrisers")
Model.objects.filter(team1="sunrisers",winner="sunrisers")
```
It returns the QuerySet when the both conditions are met.
It is similar to 
```
SELECT * FROM Match 
WHERE team1 = "sunrisers" AND winner="sunrisers";
```
**OR (|)**

It returns the QuerySet when one the condition is met from the given condition.

Eg:-
```
Match.objects.filter(team1="sunrisers") | Match.objects.filter(team2="sunrisers")
from django.db.models import Q
Match.objects.filter(Q(team1="sunrisers") | Q(team2="sunrisers"))
```
It returns all the objects when either of the team1 or team2 matches with "sunrisers".

#### exclude()

It is the opposite of the filter. It return the data which doesn't match the given condition.
Eg:-

```
Match.objects.exclude(id=20);
```
It returns all the objects with id which is not equal to 20.

#### get()

If you know there is only one object that matches your query, you can use the get() method on a Manager which returns the object directly.

Eg:-
```
Match.objects.get(id=20);
```
If the provided condition has muliple matches then it returns a **MultipleObjectsReturned**
Exception.

#### Limiting QuerySets using slicing in python.

To limit the data which we have retrived we use array slicing in python.

Eg:-
```
Match.objects.all()[0:5];
```
It returns only first 5 objects from the database. Similar to 
```
SELECT * FROM Match LIMIT 5;
```

#### How to convert ORM to SQL in Django Shell.

```
p= Match.objects.all()
print(p.query)
```
The first of the code retuns the QuerySet.
When we apply query we get the SQL equivalent 

#### Aggregation (aggregate())

Aggregate functions are used to perform calculations on multiple rows of a specific column of the data.

In django first we need to import these functions using

```
from django.db.models import Sum,Count,Avg,Min,Max,F
```

These are used to summarize the data.
* Sum()
* Count()
* Avg()
* Min()
* Max()
* 
#### Sum()

This function is used to calculate the sum of all the values in a specific column.
```
Book.objects.all().aggregate(total=Sum('price'))
```
It returns the dictonary with key as total and value as total_sum calculated from the price column.

Simliar SQL code is
```
SELECT SUM("price") FROM Book
```

#### Avg()

This function is used to calculate the average of all the values in a specific column.
```
Book.objects.all().aggregate(avg_price=Avg('price'))
```
It returns the dictonary with key as avg_price and calculated average value as value.

Simliar SQL code is
```
SELECT Avg("price") FROM Book
```

#### COUNT()

This function is used to total number of objects are present in the table.
```
Matches.objects.filter(Q(team1="sunrisers") | Q(team2="sunrisers")).
aggregate(Count("id"))

```
The above code returns the Count of matches where Sunrisers has played.

Similar SQL code is
```
SELECT COUNT(*) FROM Match
WHERE team1= "sunrisers" or team2 ="sunrisers";
```
The above Example gives the total count of students who marks is greater than 50.

#### Min()

To get the minimum value of a specified column

```
Match.objects.filter(Q(team1__startswith="Sun") | Q(team2__startswith="Sun")).aggregate(Min("id"))
```
The above code give the minimun id number from the team that starts with "Sun".

**Max()** is similar to Min() instead it returns the Maximum of the id present. 


#### F()

F() expressions are used to perform some operations on the database when retriving the data.

```
Reporter.objects.all().update(stories_filed=F('stories_filed') + 1)
```

It updates the field value of stories_filed by incrementing 1.

### annotate()

This clause is used to group the data based on categorical column data.

```
Store.objects.annotate(min_price=Min('books__price'), max_price=Max('books__price'))
```
It retrieve the Store model, join (through the many-to-many relationship) with the Book model, and aggregate on the price field of the book model to produce a minimum and maximum value.

Eg:-

```
Match.objects.annotate(team=Count('team1')).filter(team1__startswith="sun")
```

It retrives the QuerySet that has team1 name starting with "sun" and count the total matches they has played.

### Migrations

* When we run make migrations, we don’t push anything inside database. We just prepare data to get ready to go into the data base.
* Migration files are django’s way of preparing changes to be made to the database. It can be anything like add, delete or update fields.
* Migration files contains all the data that we have to make changes in.
* The migration files will be passed when we run ‘migrate’ and gets translated into sql, and this sql code is taken and run by the database and the action is performed.
* With the command 
  ```
  python manage.p[y] sqlmigrate appname0001(prefix of filename)’ 
  ```
we can run the specific file and see the sql code. This will generate and commit new changes in the database.

* 
  ```
  python manage.py showmigrations
  ```
  It gives the list of all the migrations taking pace and the one that haven’t been committed yet.

* With the help of migration files we can roll back or migrate to any level of changes that we want to apply.
* 
### Transaction

A Transaction is a group of tasks or group of commands (work) that modifies the data stored in the Databases. It can also be defined as **single unit of work** that is performed on the database.

* In SQL to start a transaction we use 
  ```
  BEGIN TRANSACTION
  ```
* And then series of statements or commands for the particular task.
* A transaction is not completed without rollback or commit transaction.
``` 
ROLLBACK TRANSACTION
```
* It is a statement which is used to rollback (restore) the database into the previous state.
```
COMMIT TRANSACTION
```
* COMMIT TRANSACTION is used to modify the database permenently.

The entire cycle is called a Transaction.

## ACID Properties
The Term ACID stands for Atomicity, Consistency ,Isolation,Durability.

These 4 properties play a very important role in maintaining the data-integrity.

### Why ACID properties ?

This properties ensures that the database transaction is completed entirely. It never leaves in the transcations in the half-completed state.

### Atomicity

It means **All or Nothing**. If any operation is performed on a data it should be executed completely or not executed at all (There is no partial execution on the data).

**Eg:-** Let us assume a person is transfering some amount from account A to account B. Let's say he started the transaction and the amount is debited from account A but not credited into the account B (operation failed). Then we use rollback transaction to go back to the previous state. If the transaction is completed only then we commit out database.

### Consistency

It simply states that any changes to a values in an instance are consistent with the values in other instance. At the end of transaction all the data must be left in the consistent state.

**Eg:-** If an amount is debited from a account. The same amount will be credited to the other account not more or less.

This ensures data consistency in the database.

### Isolation

Isolation occures when there is a concurrent transactions. It states that we can convert a parallel schedule into serial schedule.

When two or more transactions occur simultaneously. The data from one transaction should not effect the other transaction untill it is commited.

**Eg:-**  Let's say there are 2 transactions T1 and T2 which is performed
on the database concurrently. What isolation says is first perform one operation T1 and then go to T2.(T1->T2)
or (T2->T1). Let's say a person is transfering an amount 20 Rs to one account B and 30 Rs to another account C. First the amount of 20 Rs will be debited from his account and later balance is updated in his account after that from the remaining balance the 30 Rs will be debited.

When two or more events is happening parallely the consistency of the data is remained.

### Durability

Durability says that all the updates and modifications performed on the database are committed permanently. Once a cycle of Transaction is completed.

###

### How to perform Atomic Transaction in Django

import transcation from django.db

```
from django.db import transaction
```
Inside view function use transaction.atomic() for the unit to which you need to perform atomic transaction.

```
with transaction.atomic():
    do_more_stuff()
```
