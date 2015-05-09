#异常

你的Dart代码可以抛出和捕获异常。异常是对尚未发生的错误指示。如果没有捕获异常，隔离引发异常中止，具有代表性的就是隔离和程序都终止

与java相反，Dart所有的异常都是不需检测的异常。会抛出方法未声明的异常，你不需要捕捉任何异常。

Dart 提供异常、错误类型以及众多的预定义的类型。当然，你可以定义你自己的异常。然而，Dart程序可抛出任何异常。

Throw 抛出异常

这里抛出了一个异常：

<pre>
throw new FormatException('Expected at least 1 section');
</pre>
你也可以抛出任意对象：

<pre>
throw 'Out of llamas!';
</pre>
由于抛出异常是一种表示，所以你可以在声明时抛出异常，也可以在其他任何地方抛出异常。

<pre>
distanceTo(Point other) =>
    throw new UnimplementedError();
</pre>
Catch 捕获异常

捕获了一个异常后，就停止了捕获异常过程。捕获一个异常给你机会去处理它：

<pre>
try {
  breedMoreLlamas();
} on OutOfLlamasException {
  buyMoreLlamas();
}
</pre>

为了处理含有多种类型异常的代码，你可以选择多个catch子句。第一个catch子句匹配···类型处理的异常，若果catch子句未载明，这个子句就可处理任何类型的抛出对象。

<pre>
try {
  breedMoreLlamas();
} on OutOfLlamasException {
  // A specific exception
  buyMoreLlamas();
} on Exception catch (e) {
  // Anything else that is an exception
  print('Unknown exception: $e');
} catch (e) {
  // No specified type, handles all
  print('Something really unknown: $e');
}
</pre>
像之前的代码展示一样，你可以用on或者catch，或者两者都用。当你需要指定异常类型的时候用on，当你的异常处理者需要异常对象时用catch。


Finally

为了确保不论是否抛出异常，代码都正常运行，用finally子句。若果没有捕获到匹配子句的异常，finally子句运行以后异常被传递了：


<pre>
try {
  breedMoreLlamas();
} finally {
  // Always clean up, even if an exception is thrown.
  cleanLlamaStalls();
}
</pre>

在匹配了所有捕获之后，子句finally子句运行了。

<pre>
try {
  breedMoreLlamas();
} catch(e) {
  print('Error: $e');  // Handle the exception first.
} finally {
  cleanLlamaStalls();  // Then clean up.
}
</pre>
通过阅读异常部分可了解更多。



