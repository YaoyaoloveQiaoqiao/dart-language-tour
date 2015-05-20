# 类
Dart 是一种面向对象语言，包含类和基于 mixin 的继承两部分。每个对象是一个类的实例，并且 [Object](https://api.dartlang.org/apidocs/channels/stable/dartdoc-viewer/dart:core.Object) 是所有类的父类。基于 mixin 的继承指的是每个类（除了 Object ）都只有一个父类，类体还可以在多个类继承中被重用。

要创建一个对象，你可以使用 `new` 关键词并在其后跟上一个构造函数。构造函数可以写成`类名`，或者`类名.标识符`形式。例如：

```
var jsonData = JSON.decode('{"x":1, "y":2}');

//用 Point() 创建一个点。
var p1 = new Point(2, 2);

// 用 Point().fromJson() 创建一个点。
var p2 = new Point.fromJson(jsonData);
```

对象的成员分为函数和数据两类（各自的方法和实例变量）。当你调用一个方法时，你是通过一个对象来调用它的：该方法可访问该对象的方法和数据。用 . 指向对象的方法和数据成员。

```
var p = new Point(2, 2);

// 给 y赋值。
p.y = 3;

// 获取 y 的值。
assert(p.y == 3);

// 用 p 对象调用 distanceTo() 。
num distance = p.distanceTo(new Point(4, 4));
```

当你想对一个对象的成员进行一系列操作时，用串联操作（ cascade ）。

```
querySelector('#button') // 获取一个对象。
    ..text = 'Confirm'   // 调用他的成员。
    ..classes.add('important')
    ..onClick.listen((e) => window.alert('Confirmed!'));
```

一些类提供常量构造函数，要创建一个编译时用的常量构造函数，使用 `const` 关键字代替 `new` ：

```
var p = const ImmutablePoint(2, 2);
```

```
var a = const ImmutablePoint(1, 1);
var b = const ImmutablePoint(1, 1);

assert(identical(a, b)); // 他们是相同的实例！
```

下面的部分来讨论如何实现类。

实例变量

这里是你如何声明实例变量：

```
class Point {
  num x; // 声明实例变量 x ，默认值为 null 。
  num y; // 声明实例变量 y ，默认值为 null 。
  num z = 0; // 声明实例变量 z ，初始化为 0 。
}
```

所有未初始化的实例变量的值为 `null` 。

所有的实例变量会自动生成一个隐式的 getter 方法。 Non-final 实例变量也会自动生成一个隐式的 setter 方法。有关详细信息，参见 [getter and setter](https://www.dartlang.org/docs/dart-up-and-running/ch02.html#getters-and-setters) 。

```
class Point {
  num x;
  num y;
}

main() {
  var point = new Point();
  point.x = 4;          // 用 setter 方法得到 x 。
  assert(point.x == 4); // 用 getter 方法得到 x 。
  assert(point.y == null); // 值为 null 。
}
```

如果你在声明实例变量时进行了初始化（而不是在构造函数或方法），在实例创建时该值就确定了，它是在构造函数初始化列表中执行的。

构造函数

要声明一个构造函数，只需创建一个与类同名的方法（或者加上一个额外的标识符命名构造函数的描述）。构造函数最常见的形式，就是自动生成的构造函数，下面创建一个类的新实例：

```
class Point {
  num x;
  num y;

  Point(num x, num y) {
    // 有个更好的方法来实现。
    this.x = x;
    this.y = y;
  }
}
```

`this` 关键字是指当前实例。

注意：只有当名字冲突时才能使用 this。否则，Dart 会忽略 this 。

为一个实例变量分配一个构造函数参数的模式是很常见的，Dart有语法糖使它使用起来更容易：

```
class Point {
  num x;
  num y;

  // 用语法糖来设置 x ， y 。
  // 在构造函数运行之前。
  Point(this.x, this.y);
}
```

默认构造函数

如果你不声明一个构造函数，系统会提供默认构造函数。默认构造函数没有参数，它将调用父类的无参数构造函数。

构造函数不能继承

子类不继承父类的构造函数。子类只有默认构造函数。（无参数，没有名字的构造函数）。

命名构造函数

使用命名构造函数可以为一个类声明多个构造函数，或者说是提供额外的声明：

```
class Point {
  num x;
  num y;

  Point(this.x, this.y);

  // 命名构造函数
  Point.fromJson(Map json) {
    x = json['x'];
    y = json['y'];
  }
}
```

记住，构造函数不能继承，这意味着子类不会继承父类的构造函数。如果你希望子类在创建之后能够拥有在父类中声明的命名构造函数，你就必须在子类中实现该构造函数。

调用非默认的父类的构造函数

默认情况下，在子类的构造函数将会调用父类的无参数构造函数。如果父类没有构造函数，则必须手动调用父类的构造函数中的一个。在冒号（：）之后、构造函数之前指定父类的构造函数（如果有的话）。

```
class Person {
  Person.fromJson(Map data) {
    print('in Person');
  }
}

class Employee extends Person {
  // Person 没有默认构造函数;
  // 你必须调用 super.fromJson(data).
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
```

在调用父类构造函数前会检测参数，这个参数可以是一个表达式，如函数调用：

```
class Employee extends Person {
  // ...
  Employee() : super.fromJson(findDefaultData());
}
```

警告：父类构造函数的参数不能访问 `this` 。例如，参数可调用静态方法但是不能调用实方法。

初始化列表

除了调用父类构造函数，你也可以在构造函数体运行之前初始化实例变量。用逗号隔开使其分别初始化。

```
class Point {
  num x;
  num y;

  Point(this.x, this.y);

  // 初始化列表在构造函数运行前设置实例变量。

  Point.fromJson(Map jsonMap)
      : x = jsonMap['x'],
        y = jsonMap['y'] {
    print('In Point.fromJson(): ($x, $y)');
  }
}
```

警告：右手边的初始化程序无法访问 `this` 关键字。

重定向构造函数

有时一个构造函数的目的只是重定向到同一个类中的另一个构造函数。如果一个重定向的构造函数的主体为空，那么调用这个构造函数的时候，直接在冒号后面调用这个构造函数即可。

```
class Point {
  num x;
  num y;

  // 这个类的主构造函数。
  Point(this.x, this.y);

  // 主构造函数的代表。
  Point.alongXAxis(num x) : this(x, 0);
}
```

静态构造函数

如果你的类产生的对象永远不会改变，你可以让这些对象成为编译时常量。为此，需要定义一个 `const` 构造函数并确保所有的实例变量都是 `final` 的。

```
class ImmutablePoint {
  final num x;
  final num y;
  const ImmutablePoint(this.x, this.y);
  static final ImmutablePoint origin =
      const ImmutablePoint(0, 0);
}
```

工厂构造函数

当实现一个使用 `factory` 关键词修饰的构造函数时，这个构造函数不必创建类的新实例。例如，工厂构造函数可能从缓存返回实例，或者它可能返回子类型的实例。
下面的示例演示一个工厂构造函数从缓存返回的对象：

```
class Logger {
  final String name;
  bool mute = false;

  // _cache 是一个私有库,幸好名字前有个 _ 。 
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
```

注：工厂构造函数不能用 this。

调用一个工厂构造函数，你需要使用 `new` 关键字：

```
var logger = new Logger('UI');
logger.log('Button clicked');
```

方法

方法就是为对象提供行为的函数。

实例方法

对象的实例方法可以访问实例变量和 `this` 。以下示例中的 `distanceTo()` 方法是实例方法的一个例子：

```
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
```

setters 和 Getters 是一种提供对方法属性读和写的特殊方法。每个实例变量都有一个隐式的 getter 方法，合适的话可能还会有 setter 方法。你可以通过实现 getters 和 setters 来创建附加属性，也就是直接使用 `get` 和 `set` 关键词：

```
class Rectangle {
  num left;
  num top;
  num width;
  num height;

  Rectangle(this.left, this.top, this.width, this.height);

  // 定义两个计算属性: right and bottom.
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
```

借助于 getter 和 setter ，你可以直接使用实例变量，并且在不改变客户代码的情况下把他们包装成方法。

注：不论是否显示地定义了一个 getter，类似增量（++）的操作符都能以预期的方式工作。为了避免产生任何向着不期望的方向的影响，操作符一旦调用 getter ，就会把他的值存在临时变量里。

抽象方法

Instance ， getter 和 setter 方法可以是抽象的，也就是定义一个接口，但是把实现交给其他的类。要创建一个抽象方法，使用分号（；）代替方法体：

```
abstract class Doer {
  // ...定义实例变量和方法...

  void doSomething(); // 定义一个抽象方法。
}

class EffectiveDoer extends Doer {
  void doSomething() {
    // ...提供一个实现，所以这里的方法不是抽象的...
  }
}
```

调用抽象方法会导致运行时错误。

详情见 [Abstract classes](https://www.dartlang.org/docs/dart-up-and-running/ch02.html#abstract-classes)。

重载操作符

你可以重写在下表中列出的操作符。例如，如果你定义了一个向量类，你可以定义一个 + 方法来加两个向量。

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




以下是一个类中重写 `+` 和 `-` 操作符的例子：

```
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
```

如果你重写了 `==` ，你也应该重写对象中 `hashCode` 的 getter 方法。对于重写 `==` 和 `hashCode` 例子，参见实现 [Implementing map keys](https://www.dartlang.org/docs/dart-up-and-running/ch03.html#implementing-map-keys) 。

想要知道更多关于重载的信息，参见 [Extending a class](https://www.dartlang.org/docs/dart-up-and-running/ch02.html#extending-a-class) 。

抽象类

使用 `abstract` 修饰符来定义一个抽象类，该类不能被实例化。抽象类在定义接口的时候非常有用，实际上抽象中也包含一些实现。如果你想让你的抽象类被实例化，请定义一个工厂构造函数。

抽象类通常包含抽象方法。下面是声明一个含有抽象方法的抽象类的例子：

```
// 这个类是抽象类，因此不能被实例化。
abstract class AbstractContainer {
  // ...定义构造函数，域，方法...

  void updateChildren(); // 抽象方法。
}
```

下面的类不是抽象类，因此它可以被实例化，即使定义了一个抽象方法：

```
class SpecializedContainer extends AbstractContainer {
  // ...定义更多构造函数，域，方法...

  void updateChildren() {
    // ...实现 updateChildren()...
  }

  // 抽象方法造成一个警告，但是不会阻止实例化。
  void doSomething();
}
```

隐式接口

每个类隐式的定义了一个接口，含有类的所有实例和它实现的所有接口。如果你想创建一个支持类 B 的 API 的类 A，但又不想继承类 B ，那么，类 A 应该实现类 B 的接口。

一个类实现一个或更多接口通过用 `implements` 子句声明，然后提供 API 接口要求。例如：

```
// 一个 person ，包含 greet() 的隐式接口。
class Person {
  // 在这个接口中，只有库中可见。
  final _name;

  // 不在接口中，因为这是个构造函数。
  Person(this._name);

  // 在这个接口中。
  String greet(who) => 'Hello, $who. I am $_name.';
}

//  Person 接口的一个实现。
class Imposter implements Person {
  // 我们不得不定义它，但不用它。
  final _name = "";

  String greet(who) => 'Hi $who. Do you know who I am?';
}

greetBob(Person person) => person.greet('bob');

main() {
  print(greetBob(new Person('kathy')));
  print(greetBob(new Imposter()));
}
```

这里是具体说明一个类实现多个接口的例子：

```
class Point implements Comparable, Location {
  // ...
}
```

扩展一个类

使用 `extends` 创建一个子类，同时 `supper` 将指向父类：

```
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
```

子类可以重载实例方法， getters 方法， setters 方法。下面是个关于重写 Object 类的方法   `noSuchMethod()` 的例子,当代码企图用不存在的方法或实例变量时，这个方法会被调用。

```
class A {
  // 如果你不重写 noSuchMethod 方法, 就用一个不存在的成员，会导致 NoSuchMethodError 错误。
  void noSuchMethod(Invocation mirror) {
    print('You tried to use a non-existent member:' +
          '${mirror.memberName}');
  }
}
```

你可以使用 `@override` 注释来表明你重写了一个成员。

```
class A {
  @override
  void noSuchMethod(Invocation mirror) {
    // ...
  }
}
```

如果你用 `noSuchMethod()` 实现每一个可能的 getter 方法，setter 方法和类的方法，那么你可以使用 `@proxy` 标注来避免警告。

```
@proxy
class A {
  void noSuchMethod(Invocation mirror) {
    // ...
  }
}
```

关于注释的更多信息，请参 [Metadata](https://www.dartlang.org/docs/dart-up-and-running/ch02.html#metadata)。

枚举类型

枚举类型，通常被称为 enumerations 或 enums ，是一种用来代表一个固定数量的常量的特殊类。

使用枚举

声明一个枚举类型需要使用关键字 `enum` ：

```
enum Color {
  red,
  green,
  blue
}
```

在枚举中每个值都有一个 `index`  getter 方法，它返回一个在枚举声明中从 0 开始的位置。例如，第一个值索引值为 0 ，第二个值索引值为 1 。

```
assert(Color.red.index == 0);
assert(Color.green.index == 1);
assert(Color.blue.index == 2);
```

要得到枚举列表的所有值，可使用枚举的 `values` 常量。

```
List<Color> colors = Color.values;
assert(colors[2] == Color.blue);
```

你可以在 switch 语句中使用枚举。如果 e 在 `switch (e)` 是显式类型的枚举，那么如果你不处理所有的枚举值将会弹出警告：

```
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
```

枚举类型有以下限制：

- 你不能在子类中混合或实现一个枚举。
- 你不能显式实例化一个枚举。

更多信息，见 [Dart Language Specification](https://www.dartlang.org/docs/spec/)。

为类添加特征：mixins

mixins 是一种多类层次结构的类的代码重用。

要使用 mixins ，在 with 关键字后面跟一个或多个 mixin 的名字。下面的例子显示了两个使用mixins的类：

```
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
```

要实现 mixin ，就创建一个继承 Object 类的子类，不声明任何构造函数，不调用 super 。例如：

```
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
```

更多信息，见文章 [Mixins in Dart](https://www.dartlang.org/articles/mixins/) 。

类的变量和方法

使用 `static` 关键字来实现类变量和类方法。

静态变量

静态变量（类变量）对于类状态和常数是有用的：

```
class Color {
  static const red =
      const Color('red'); // 一个恒定的静态变量
  final String name;      // 一个实例变量。 
  const Color(this.name); // 一个恒定的构造函数。
}

main() {
  assert(Color.red.name == 'red');
}
```

只有当静态变量被使用时才被初始化。

注：本章内容依据代码风格指南推荐的 lowerCamelCase 来为常量命名。

静态方法

静态方法（类方法）不在一个实例上进行操作，因而不必访问 `this` 。例如：

```
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
```

注：考虑到使用高阶层的方法而不是静态方法，是为了常用或者广泛使用的工具和功能。

你可以将静态方法作为编译时常量。例如，你可以把静态方法作为一个参数传递给静态构造函数。
