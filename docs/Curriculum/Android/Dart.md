## 变量

- 变量：var、dynamic、”type-defined“
- 常量：const（编译时就已经固定了常量的值）、final（编译时就已经不确定了常量的值，第一次初始化时才固定）

- 默认值：null



## 内建类型

- Number：int、double
- String：String、Runes
- Boolean：bool



- List：List
- Map：Map（除了直接{}，也可以用Map()）
- Set：Set（由于直接var s = {}会给s赋一个Map，所以应该明确指定s为Set\<xxx\>类型）



- Symbol：略



## 函数特性

- Dart中函数是一等公民：可以将函数作为参数、变量的值。
- 所有函数都会返回值（即使返回值为void），如果不显式返回，默认返回null
- 单行lambda

### 0. main函数

- 参数为可选的List\<String\>
- 返回值为void



### 1. 可选参数

- 位置可选参数（[]型参数）
  - 定义：String say(String from, String msg, **[String device]**) {...}
  - 默认值：String device = true（使用等号）
- 命名可选参数（{}型参数）
  - 定义：void enableFlags(**{bool bold, bool hidden}**) {...}
  - 调用：enableFlags(bold: true, hidden: false);
  - 默认值：bool hidden = true（使用等号）
  - **{}型可选参数的必选化**：const Scrollbar({Key key, @required Widget child}){...}
    - 使用了@required注解；这表示Widget child必须传入，否则会报错误



### 2. 级联调用

```dart
void main() {
  querySelector('#sample_text_id')
    ..text = 'Click me!'
    ..onClick.listen(reverseText);
}
```

- querySelector('#sample_text_id')产生了一个对象，假设为obj
- 则..text和..onClick就是分别在obj进行的两次操作
- （注意区别..调用和.调用）



### 3. 匿名函数

- 语法：(参数列表){函数体}
- 单行lambda语法：(参数列表) => 返回值



## 特殊表达式

- 基本运算符：略

- /与~/
  - A / B：double除法，结果均为double（不会产生截断）
  - A ~/ B：int除法，结果均为int（会产生截断）
- is、is!、as
  - A is Type：匹配对象的类型，A的类型是Type，则返回真
  - (A as Type).TypeMethod()：A通过is进行判断，如果为真，转型为Type；如果为假，则抛出异常
- ?:、??
  - **A ? B : C**：略
  - **A ?? B**：等价于A != null ? A : B
- .调用、..调用、?.调用
  - A.B调用：略
  - A..B调用：略
  - A?.B调用：如果A为null，上述调用会报错，但A?.B仅会返回null（对null的任何调用都是null）



## 控制流程

- 基本控制流程：略

- switch-case语句

  - case语句可以拥有局部作用域
  - 如果case为空，且不break，则会直接执行下一个case
  - 如果case不为空，且不break，则需要手动设置label，并且使用continue LABEL来指定下一个执行的case

  ```dart
  var command = 'CLOSED';
  switch (command) {
    case 'CLOSED':
      executeClosed();
      continue nowClosed;// 结束后执行nowClosed标签后的代码
  
    nowClosed:
    case 'NOW_CLOSED':
      executeNowClosed();
      break;
  }
  ```



## 异常

- assert

- throw
- try-catch-finally



## 类与对象

### 1. 基础

- new是可选的
- obj.runtimeType可以动态获取对象的类型
- this：获取当前类的对象，仅用于重名时。



### 2. 构造函数

- **默认构造函数**定义

  ```dart
  class Point {
  	num x, y;
  
  	Point(this.x, this.y);
    	// 等价于：
      // Point(num x, num y) {
      //     this.x = x;
      //     this.y = y;
      // }
  }
  ```


- **命名构造函数**定义

  ```dart
  ClassName.name(...)
  ```

  - 可以满足不同参数的初始化要求

  - 例子

    ```dart
    // 定义
    class Student {
        String name;
        int age;
    
        Student(this.name, this.age);
        Student.withAge(this.age) : name = 'Unknown';
    }
    
    // 调用
    void main() {
    	var s1 = Student('Alice', 20);
    	var s2 = Student.withAge(30);
    }
    ```

- **重命名构造函数**定义：略



- **常量构造函数**定义

  ```dart
  class ImmutablePoint {
    	static final ImmutablePoint origin = const ImmutablePoint(0, 0);
  
    	final num x, y;
  
    	const ImmutablePoint(this.x, this.y);
  }
  
  var a = ImmutablePoint(1, 1);
  var b = ImmutablePoint(1, 1);
  ```

  - 两个相同的编译时常量会产生一个唯一的， 标准的实例：此时a和b的对象相等。
  - **常量上下文内**，构造函数或者字面量前的 const 可以省略。

### 2.Getter和Setter方法

- 类名.类属性：会调用类属性的getter/setter方法

```dart
class Rectangle {
  num left, top, width, height;

  Rectangle(this.left, this.top, this.width, this.height);

  // 定义两个计算属性： right 和 bottom。
  num get right => left + width;
  set right(num value) => left = value - width;
  num get bottom => top + height;
  set bottom(num value) => top = value - height;
}

void main() {
  var rect = Rectangle(3, 4, 20, 15);
  assert(rect.left == 3);
  rect.right = 12;
  assert(rect.left == -8);
}

```



