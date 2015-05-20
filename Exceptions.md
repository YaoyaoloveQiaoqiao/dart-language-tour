# 异常

你的Dart代码可以抛出异常和捕获异常。异常就是出现预期之外的结果的错误。如果没有捕获异常, isolate 将会使异常挂起，往往会导致 isolate 和程序终止。

与java相反，Dart中所有异常都是不需检测的异常。方法并不会声明它将抛出哪些异常，而且你不需要去捕捉任何异常。

Dart 除了提供异常、错误类型以外还提供了众多预定义的子类型。当然，你可以定义你自己的异常类型。毕竟，Dart程序可以将任何非空对象作为异常抛出，不只局限与异常和错误对象。

Throw 抛出异常

这里抛出了一个异常：

```
throw new FormatException('Expected at least 1 section');
```

你也可以将任意对象作为异常抛出：

```
throw 'Out of llamas!';
```

由于抛出异常是单个表达式，所以你可以将 in=> 语句作为异常抛出，也可以在其他任何地方抛出异常。

```
distanceTo(Point other) =>
    throw new UnimplementedError();
```

Catch 捕获异常

捕获了一个异常后，就停止了捕获异常过程。捕获一个异常，你就有机会去处理它：

```
try {
  breedMoreLlamas();
} on OutOfLlamasException {
  buyMoreLlamas();
}
```

为了处理含有多种类型异常的代码，你可以选择多个catch子句。第一个匹配抛出对象类型的catch子句将会处理这个异常。如果catch子句未说明所捕获的异常类型，这个子句就可处理任何被抛出的对象。

```
try {
  breedMoreLlamas();
} on OutOfLlamasException {
  // 一个具体异常
  buyMoreLlamas();
} on Exception catch (e) {
  // 任意一个异常
  print('Unknown exception: $e');
} catch (e) {
  // 非具体类型
  print('Something really unknown: $e');
}
```

像上面展示的代码一样，你可以用 `on` 或者 `catch` ，或者两者都用。当你需要指定异常类型的时候用on，当你的异常处理者需要异常对象时用catch。


Finally

为了确保不论是否抛出异常，代码都正常运行，请使用  `finally` 子句。如果没有 `catch` 匹配子句的异常， `finally `子句运行以后异常将被传播：

```
try {
  breedMoreLlamas();
} finally {
  // 即使抛出一个异常时也会进行清理
  cleanLlamaStalls();
}
```

在匹配了所有 `catch` 之后，子句 `finally` 运行了。

```
try {
  breedMoreLlamas();
} catch(e) {
  print('Error: $e');  // 先处理异常
} finally {
  cleanLlamaStalls();  // 然后清理
}
```

通过阅读 [Exceptions](https://www.dartlang.org/docs/dart-up-and-running/ch03.html#exceptions)部分可了解更多。



