> 24 - Nov - 2024

# Value

* Value ----> Information / Data

```
In programming - value means the system/program - pass some kind of information or data to perform some kind of action based on that passing information/data. 

if we need to write a program to build and run and represent in-front of other people, 
then inside that program, how we arrange/structure the value/information/data...
so that out build app run iffecently and can be scaled supported at future and easy to identify the bug.
```

```
Inside a app if we don't store/save a value, then we can not perform sequence of actions.

Value become variable... so a variable is work like a label for that store/save value.

In a program have multiple value/information/data/variable... 

base on these variables can produce different multiple unique useful values and can perform certain actions...

So this kind of creation of unique values called expression --> 
a + b = c ---> this total line is called expression
1 + 4 = 5 ---> this total line is called expression

so operator is responsible also for creating new unique values...

value ---> variable ---> operator ---> expression ---> dataType ---> primitive/non-primitive

When problem come to you... its comes to you as like a value/information/data...
& when we design the solution for that problem, we need the help of operator + expression...
that combination will create unique values.
So from input to output --> we need operator & expression
```

```
value ---> variable ---> operator ---> expression ---> dataType ---> primitive/non-primitive ---> pass by value/reference
```

## Data Type

* It is responsible for allow you to perform which kind of actions we can perform with variable...

|No| DataType   | Category      |                    |
|--|------------|---------------|--------------------|
|1 | string     | primitive     | basic/simple data  |
|2 | number     | primitive     | basic/simple data  |
|3 | bigInt     | primitive     | basic/simple data  |
|4 | boolean    | primitive     | basic/simple data  |
|5 | undefined  | primitive     | basic/simple data  |
|6 | null       | primitive     | basic/simple data  |
|7 | symbol     | primitive     | basic/simple data  |
|8 | object     | non-primitive | complex data       |

* `undefined` --> **variable** is created, but its **value** is not assigned into it...
* `value` is handled by these `dataTypes`...
* `object` is a **complex dataType**, that is **built** by this `basic` `dataType`...
  * string ---> "count"
  * number ---> 10
  * object ---> { "count" : 10 } ---> { key : value }
    * key ---> is unique identifier to get that value from object
    * value ---> is actual value inside that object, to perform some action based on it...

* So how to handle primitive and non-primitive value/dataType

* Primitive types
  * in primitive variable - value is assigned by - `pass by value` (actual value)
  * in non-primitive variable - value is assigned by - `pass by reference` (memory location of value)

## Closer

Child function create a separate memory for storing parent data -- </br>
this memory called closer. (or additional memory, scope)
