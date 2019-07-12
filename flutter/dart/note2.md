## Exceptions 异常

1、Throw
抛出异常

``` throw FormatException('Expected at least 1 section')  ```

2、 Catch捕捉异常
````
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
````

异常冒泡使用rethrow
````
void misbehave() {
  try {
    dynamic foo = true;
    print(foo++); // Runtime error
  } catch (e) {
    print('misbehave() partially handled ${e.runtimeType}.');
    rethrow; // Allow callers to see the exception.
  }
}

void main() {
  try {
    misbehave();
  } catch (e) {
    print('main() finished handling ${e.runtimeType}.');
  }
}
````

3、 Finally

为了确保有些代码运行是否抛出异常，使用finally 分句。如果没有catch分句匹配这个异常，这个异常将会冒泡到finally分句中，在finally分句中执行， 例如：
````
try {
  breedMoreLlamas();
} finally {
  // Always clean up, even if an exception is thrown.
  cleanLlamaStalls();
}

````

finally分句在任何一个匹配catch分句运行之后 运行，
````
try {
  breedMoreLlamas();
} catch (e) {
  print('Error: $e'); // Handle the exception first.
} finally {
  cleanLlamaStalls(); // Then clean up.
}

````
