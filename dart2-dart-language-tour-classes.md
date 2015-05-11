#Classes类
Dart 是一种面向对象语言，包含类和基于 mixin 的继承两部分。每个对象是一个类的实例，并且 Object 是所有类的父类。基于 mixin 的继承指的是除了每个类（除了 Object ）都有一个父类，类体还可以在多个类继承中被复写。

你可以用带有类构造函数的 new 关键字创建一个对象。构造函数决定了写成类名，还是类名.标识符形式。例如：

<pre>
var jsonData = JSON.decode('{"x":1, "y":2}');

// Create a Point using Point().
var p1 = new Point(2, 2);

// Create a Point using Point.fromJson().
var p2 = new Point.fromJson(jsonData);
</pre>

对象包含函数和数据成员（各自的方法和实例变量）。当你用一个对象调用一个方法：该方法可访问该对象的方法和数据。用 . 指向对象的方法和数据成员。

<pre>
var p = new Point(2, 2);

// Set the value of the instance variable y.
p.y = 3;

// Get the value of y.
assert(p.y == 3);

// Invoke distanceTo() on p.
num distance = p.distanceTo(new Point(4, 4));
</pre>

当你想对一个对象的成员进行一系列操作时，用串联操作（ cascade ）。

<pre>
querySelector('#button') // Get an object.
    ..text = 'Confirm'   // Use its members.
    ..classes.add('important')
    ..onClick.listen((e) => window.alert('Confirmed!'));
</pre>

一些类提供常量构造函数，创建一个编译时用的常量构造函数，用 const 关键字代替 new ：

<pre>
var p = const ImmutablePoint(2, 2);
</pre>

<pre>
var a = const ImmutablePoint(1, 1);
var b = const ImmutablePoint(1, 1);

assert(identical(a, b)); // They are the same instance!
</pre>

下面的部分来讨论如何实现类。

实例变量

这里是你如何声明实例变量：

<pre>
class Point {
  num x; // Declare instance variable x, initially null.
  num y; // Declare y, initially null.
  num z = 0; // Declare z, initially 0.
}
</pre>

所有未初始化的实例变量的值为空。

所有的实例变量生成一个隐含的 getter 方法。 Non-final 实例变量也产生一个隐含的 setter 方法。有关详细信息，参见 getter 和 setter 。

<pre>
class Point {
  num x;
  num y;
}

main() {
  var point = new Point();
  point.x = 4;          // Use the setter method for x.
  assert(point.x == 4); // Use the getter method for x.
  assert(point.y == null); // Values default to null.
}
</pre>

如果你在声明实例变量时进行了初始化（而不是在构造函数或方法），在实例创建时该值就确定了，它是在构造函数初始化列表中执行的。

构造函数

通过创建一个与类名同名的函数声明一个构造函数（或者加上一个额外的标识符命名构造函数的描述）。构造函数的最常见的形式，自动生成的构造函数，下面创建一个类的新实例：

<pre>
class Point {
  num x;
  num y;

  Point(num x, num y) {
    // There's a better way to do this, stay tuned.
    this.x = x;
    this.y = y;
  }
}
</pre>

this关键字是指当前实例。

注意：只有当名字冲突时才能使用 this。否则，Dart 会省略 this 。

在一个实例变量分配一个构造函数参数的模式是很常见的，Dart有语法糖使它使用起来更容易：

<pre>
class Point {
  num x;
  num y;

  // Syntactic sugar for setting x and y
  // before the constructor body runs.
  Point(this.x, this.y);
}
</pre>

默认构造函数

如果你不声明一个构造函数，系统会提供默认构造函数。默认构造函数没有参数，调用父类的无参数构造函数。

构造函数不能继承

子类不继承父类的构造函数。子类只有默认构造函数。（无参数，没有名字的构造函数）。

命名构造函数

使用命名构造函数来实现多个构造函数的类或提供额外的声明：

<pre>
class Point {
  num x;
  num y;

  Point(this.x, this.y);

  // Named constructor
  Point.fromJson(Map json) {
    x = json['x'];
    y = json['y'];
  }
}
</pre>

记住，构造函数不能继承，这意味着子类不会继承父类的构造函数。如果你想要一个类是父类定义的命名构造函数创建的，你必须在子类中实现该构造函数。

调用非默认的父类的构造函数

默认情况下，在子类的构造函数调用父类的无参数构造函数。如果父类没有构造函数，则必须手动调用父类的构造函数中的一个。在冒号后指定父类的构造函数（：），在构造函数前（如果有的话）。

<pre>
class Person {
  Person.fromJson(Map data) {
    print('in Person');
  }
}

class Employee extends Person {
  // Person does not have a default constructor;
  // you must call super.fromJson(data).
  Employee.fromJson(Map data) : super.fromJson(data) {
    print('in Employee');
  }
}

main() {
  var emp = new Employee.fromJson({});

  // Prints:
  // in Person
  // in Employee
}
</pre>

由于父类在调用构造函数前会检测参数，参数可以是一个表达式，如函数调用：

<pre>
class Employee extends Person {
  // ...
  Employee() : super.fromJson(findDefaultData());
}
</pre>

警告：父类的构造函数的参数不能访问。例如，参数可调用静态方法但是不能调用实方法。

初始化列表

除了调用父类的构造函数初始化实例变量，你也可以在构造函数体运行之前初始化实例。用逗号隔开使其分别初始化。

<pre>
class Point {
  num x;
  num y;

  Point(this.x, this.y);

  // Initializer list sets instance variables before
  // the constructor body runs.
  Point.fromJson(Map jsonMap)
      : x = jsonMap['x'],
        y = jsonMap['y'] {
    print('In Point.fromJson(): ($x, $y)');
  }
}
</pre>

警告：右手边的初始化程序无法完成。

重载构造函数

有时一个构造函数的唯一目的是重载到另一个构造函数在同一个类中。如果一个重载构造函数主体是空的的话，那么调用这个构造函数的时候，调用后面直接加冒号即可。

<pre>
class Point {
  num x;
  num y;

  // The main constructor for this class.
  Point(this.x, this.y);

  // Delegates to the main constructor.
  Point.alongXAxis(num x) : this(x, 0);
}
</pre>

静态的构造函数

如果你的类产生的对象永远不会改变，你可以把这些对象编写成常量。为此，定义一个 const 构造函数能确保所有的实例变量是不变的。

<pre>
class ImmutablePoint {
  final num x;
  final num y;
  const ImmutablePoint(this.x, this.y);
  static final ImmutablePoint origin =
      const ImmutablePoint(0, 0);
}
</pre>

工厂构造函数

当实现一个构造函数使用 factory 关键词，这个构造函数不必非要创建类的新实例。例如，工厂构造函数可能从缓存返回实例，或者它可能返回子类型的实例。
下面的示例演示一个工厂构造函数从缓存返回的对象：

<pre>
class Logger {
  final String name;
  bool mute = false;

  // _cache is library-private, thanks to the _ in front
  // of its name.
  static final Map<String, Logger> _cache =
      <String, Logger>{};

  factory Logger(String name) {
    if (_cache.containsKey(name)) {
      return _cache[name];
    } else {
      final logger = new Logger._internal(name);
      _cache[name] = logger;
      return logger;
    }
  }

  Logger._internal(this.name);

  void log(String msg) {
    if (!mute) {
      print(msg);
    }
  }
}
</pre>

注：工厂构造函数不能用 this。

调用一个工厂构造函数，你用 new 关键字：

<pre>
var logger = new Logger('UI');
logger.log('Button clicked');
</pre>

方法

方法的功能是提供一个对象的行为。

实例方法

对象的实例方法可以访问实例变量和 this 。以下示例中的 distanceTo() 方法是实例方法的一个例子：

<pre>
import 'dart:math';

class Point {
  num x;
  num y;
  Point(this.x, this.y);

  num distanceTo(Point other) {
    var dx = x - other.x;
    var dy = y - other.y;
    return sqrt(dx * dx + dy * dy);
  }
}
</pre>

setters 和 Getters 是一种提供对方法属性读和写的特殊方法。每个实例变量有一个隐式 getter 方法，如果适当的话还会有个 setter 方法。你可以创建额外的属性通过实现 getters 和 setters ，用 get 和 set 关键词：

<pre>
class Rectangle {
  num left;
  num top;
  num width;
  num height;

  Rectangle(this.left, this.top, this.width, this.height);

  // Define two calculated properties: right and bottom.
  num get right             => left + width;
      set right(num value)  => left = value - width;
  num get bottom            => top + height;
      set bottom(num value) => top = value - height;
}

main() {
  var rect = new Rectangle(3, 4, 20, 15);
  assert(rect.left == 3);
  rect.right = 12;
  assert(rect.left == -8);
}
</pre>

用 getter 和 setter ，你可以始于实例变量，然后把他们包装成方法，这都不改变客户端代码。

注：操作符如增量（++）以预期的方式工作，不论是否明确定义了一个 getter 。为了避免任何意外的副作用，操作符一旦调用 getter ，就会把他的值存在临时变量里。
抽象方法

Instance ， getter 和 setter 方法可以是抽象的，定义一个接口而实现了其他类。创建一个抽象方法，使用分号（；）代替方法体：

<pre>
abstract class Doer {
  // ...Define instance variables and methods...

  void doSomething(); // Define an abstract method.
}

class EffectiveDoer extends Doer {
  void doSomething() {
    // ...Provide an implementation, so the method is not abstract here...
  }
}
</pre>

调用抽象方法会导致运行时错误。

详情见抽象类。

重载操作符

你可以重写在下表中显示的操作符。例如，如果你定义了一个向量类，你可以定义一个 + 方法来加两个向量。

<table>
<tbody>
<tr>
<td><</td><td>+</td><td>|</td><td>[]</td>
</tr>

<tr>
<td>></td><td>/</td><td>^</td><td>[]=</td>
</tr>

<tr>
<td><=</td><td>~/</td><td>&</td><td>~</td>
</tr>

<tr>
<td>>=</td><td>*</td><td><<</td><td>==</td>
</tr>

<tr>
<td>-</td><td>%</td><td>>></td><td></td>
</tr>

</tbody>
</table>




以下是一个类中重写 + 和 - 操作符的例子：

<pre>
class Vector {
  final int x;
  final int y;
  const Vector(this.x, this.y);

  /// Overrides + (a + b).
  Vector operator +(Vector v) {
    return new Vector(x + v.x, y + v.y);
  }

  /// Overrides - (a - b).
  Vector operator -(Vector v) {
    return new Vector(x - v.x, y - v.y);
  }
}

main() {
  final v = new Vector(2, 3);
  final w = new Vector(2, 2);

  // v == (2, 3)
  assert(v.x == 2 && v.y == 3);

  // v + w == (4, 5)
  assert((v + w).x == 4 && (v + w).y == 5);

  // v - w == (0, 1)
  assert((v - w).x == 0 && (v - w).y == 1);
}
</pre>

如果你重写了 == ，你也应该重写对象的 hashCode 的 getter 方法。对于重写 == 和 hashCode 例子，参见实现 map 的 key 。

想要知道更多关于重载的信息，参见扩展类。

抽象类

使用 abstract 修饰符来定义一个抽象类，该类不能被实例化。抽象类用于定义接口，经常需要一些实现。如果你想让你的抽象类被实例化，定义一个工厂构造函数。

抽象类常有抽象方法。下面是声明一个含有抽象方法的抽象类的例子：

<pre>
// This class is declared abstract and thus
// can't be instantiated.
abstract class AbstractContainer {
  // ...Define constructors, fields, methods...

  void updateChildren(); // Abstract method.
}
</pre>

下面的类不是抽象类，因此它可以被实例化，即使定义了一个抽象方法：

<pre>
class SpecializedContainer extends AbstractContainer {
  // ...Define more constructors, fields, methods...

  void updateChildren() {
    // ...Implement updateChildren()...
  }

  // Abstract method causes a warning but
  // doesn't prevent instantiation.
  void doSomething();
}
</pre>

隐式接口

每个类隐式的定义了一个接口，含有类的所有实例和他实现的所有接口。如果你想创建一个类 A 支持类 B 的 API ，但不想继承类 B ，那么，类 A 应该实现类 B 的接口。

一个类实现一个或更多接口通过用 implements 子句声明，然后提供 API 接口要求。例如：

<pre>
// A person. The implicit interface contains greet().
class Person {
  // In the interface, but visible only in this library.
  final _name;

  // Not in the interface, since this is a constructor.
  Person(this._name);

  // In the interface.
  String greet(who) => 'Hello, $who. I am $_name.';
}

// An implementation of the Person interface.
class Imposter implements Person {
  // We have to define this, but we don't use it.
  final _name = "";

  String greet(who) => 'Hi $who. Do you know who I am?';
}

greetBob(Person person) => person.greet('bob');

main() {
  print(greetBob(new Person('kathy')));
  print(greetBob(new Imposter()));
}
</pre>

这里是具体说明一个类实现多个接口的例子：

<pre>
class Point implements Comparable, Location {
  // ...
}
</pre>

扩展一个类

用 extends 创建一个子类，用 supper 指向父类：

<pre>
class Television {
  void turnOn() {
    _illuminateDisplay();
    _activateIrSensor();
  }
  // ...
}

class SmartTelevision extends Television {
  void turnOn() {
    super.turnOn();
    _bootNetworkInterface();
    _initializeMemory();
    _upgradeApps();
  }
  // ...
}
</pre>

子类可以重载实例方法， getters 方法， setters 方法。下面是个关于重写 Object 类的方法   noSuchMethod() 的例子,当代码企图用不存在的方法或实例变量时，这个方法会被调用。

<pre>
class A {
  // Unless you override noSuchMethod, using a
  // non-existent member results in a NoSuchMethodError.
  void noSuchMethod(Invocation mirror) {
    print('You tried to use a non-existent member:' +
          '${mirror.memberName}');
  }
}
</pre>

你可以使用 @override 注释来表明你重写了一个成员。

<pre>
class A {
  @override
  void noSuchMethod(Invocation mirror) {
    // ...
  }
}
</pre>

如果你用 noSuchMethod() 实现每一个可能的 getter 方法，setter 方法和类的方法，那么你可以使用 @proxy 标注来避免警告。

<pre>
@proxy
class A {
  void noSuchMethod(Invocation mirror) {
    // ...
  }
}
</pre>

关于注释的更多信息，请参阅元数据。

枚举类型

枚举类型，通常被称为 enumerations 或 enums ，是一种特殊的用来代表一个固定数量的恒定值的类。

使用枚举

声明一个枚举类型使用关键字 enum ：

<pre>
enum Color {
  red,
  green,
  blue
}
</pre>

在枚举每个值都有一个索引 getter 方法，它返回一个在枚举声明值中从 0 开始的位置。例如，第一个值索引值为 0 ，第二个值索引值为 1 。

<pre>
assert(Color.red.index == 0);
assert(Color.green.index == 1);
assert(Color.blue.index == 2);
</pre>

要得到枚举列表的所有值，可使用枚举的值常量。

<pre>
List<Color> colors = Color.values;
assert(colors[2] == Color.blue);
</pre>

你可以在 switch 语句中使用枚举。如果 e 在 switch (e) 是显式类型
的枚举，那么你如果你不处理所有的枚举值会弹出警告：

<pre>
enum Color {
  red,
  green,
  blue
}
// ...
Color aColor = Color.blue;
switch (aColor) {
  case Color.red:
    print('Red as roses!');
    break;
  case Color.green:
    print('Green as grass!');
    break;
  default: // Without this, you see a WARNING.
    print(aColor);  // 'Color.blue'
}
</pre>

枚举类型有以下限制：

- 你不能在子类中混合或实现一个枚举。
- 你不能显式实例化一个枚举。

更多信息，见 Dart 语言规范。

为类添加特征：mixins

mixins 是一种多类层次结构的类的代码重用。

使用 mixins ，使用 with 关键字后面跟一个或多个 mixin 的名字。下面的例子显示了两个类使用mixins：

<pre>
class Musician extends Performer with Musical {
  // ...
}

class Maestro extends Person
    with Musical, Aggressive, Demented {
  Maestro(String maestroName) {
    name = maestroName;
    canConduct = true;
  }
}
</pre>

实现 mixin ，创建一个类继承 Object 类，不声明任何构造函数，不调用 super 。例如：

<pre>
abstract class Musical {
  bool canPlayPiano = false;
  bool canCompose = false;
  bool canConduct = false;

  void entertainMe() {
    if (canPlayPiano) {
      print('Playing piano');
    } else if (canConduct) {
      print('Waving hands');
    } else {
      print('Humming to self');
    }
  }
}
</pre>

更多信息，见文章 Dart 里的 Mixins 。

类的变量和方法

使用 static 关键字来实现类的变量和方法。

静态变量

静态变量（类变量）对于类状态和常数是有用的：

<pre>
class Color {
  static const red =
      const Color('red'); // A constant static variable.
  final String name;      // An instance variable.
  const Color(this.name); // A constant constructor.
}

main() {
  assert(Color.red.name == 'red');
}
</pre>
只有当使用静态变量时才被初始化。

注：本章内容依据 lowerCamelCase 风格指南推荐为常量命名。

静态方法

静态方法（类方法）不在一个实例上操作，从而不必访问 this 。例如：

<pre>
import 'dart:math';

class Point {
  num x;
  num y;
  Point(this.x, this.y);

  static num distanceBetween(Point a, Point b) {
    var dx = a.x - b.x;
    var dy = a.y - b.y;
    return sqrt(dx * dx + dy * dy);
  }
}

main() {
  var a = new Point(2, 2);
  var b = new Point(4, 4);
  var distance = Point.distanceBetween(a, b);
  assert(distance < 2.9 && distance > 2.8);
}
</pre>
注：考虑到使用高阶层的方法代替静态方法，为了广泛使用的工具和功能。

你可以使用静态方法作为编译时常量。例如，你可以通过静态方法作为一个参数为常数的构造函数。


