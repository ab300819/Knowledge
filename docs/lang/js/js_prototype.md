# JavaScript prototype

### #创建层级结构

#### ##定义类和属性

```js
function Employee () {
  this.name = "";
  this.dept = "general";
}
```

> 参考

```java
public class Employee {
    public String name = "";
    public String dept = "general";
}
```

#### ##继承类

```js
function Manager() {
  Employee.call(this);
  this.reports = [];
}
Manager.prototype = Object.create(Employee.prototype);

function WorkerBee() {
  Employee.call(this);
  this.projects = [];
}
WorkerBee.prototype = Object.create(Employee.prototype);
```

> 参考

```java
public class Manager extends Employee {
    public Employee[] reports = new Employee[0];
}

public class WorkerBee extends Employee {
    public String[] projects = new String[0];
}
```

#### ##继承并重载属性

```js
function SalesPerson() {
    WorkerBee.call(this);
    this.dept = 'sales';
    this.quota = 100;
}
SalesPerson.prototype = Object.create(WorkerBee.prototype);

function Engineer() {
    WorkerBee.call(this);
    this.dept = 'engineering';
    this.machine = '';
}
Engineer.prototype = Object.create(WorkerBee.prototype);
```

> 参考

```java
public class SalesPerson extends WorkerBee {
    public String dept = "sales";
    public double quota = 100.0;
}

public class Engineer extends WorkerBee {
    public String dept = "engineering";
    public String machine = "";
}
```

#### ##创建对象

```js
var jim = new Employee; // parentheses can be omitted if the constructor takes no arguments
// jim.name is ''
// jim.dept is 'general'

var sally = new Manager;
// sally.name is ''
// sally.dept is 'general'
// sally.reports is []

var mark = new WorkerBee;
// mark.name is ''
// mark.dept is 'general'
// mark.projects is []

var fred = new SalesPerson;
// fred.name is ''
// fred.dept is 'sales'
// fred.projects is []
// fred.quota is 100

var jane = new Engineer;
// jane.name is ''
// jane.dept is 'engineering'
// jane.projects is []
// jane.machine is ''
```