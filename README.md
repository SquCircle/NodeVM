# NodeVM

基于**KittenN列表**的**AST树遍历执行器**，支持**变量**、**函数**、**作用域**、**控制流**等特性。

# NodeCode

可以解析为**NodeVM**节点树的**动态类型语言**，使用贴近**JavaScript**的C系语法设计。

## 体验链接
[NodeVM Player](kn.codemao.cn/player?type=2&workId=328981781)

## 语法

### 注释
NodeCode使用JavaScript风格的注释。支持单行与多行注释，多行注释**不支持嵌套**。
``` nodecode
// 单行注释
/*
	多行注释
*/
```

### 字面量
NodeCode支持**数字**、**字符串**、**布尔值**、**数组**、**字典**、**函数**字面量。
```nodecode
/* 数字 */
42
3.14

/* 字符串 */
"双引号字符串"
'单引号字符串'
"转义\t字符"

/* 布尔值 */
true
false

/* 数组 */
[1, "a", 3]
[1, Math.PI, Math.sin(45)]
/* 数组取值 */
var l = [1, 2, 3];
print(l[0]);	/* 0-based */

/* 字典 */
{
	"a": 123,
	Math.PI: 3.14159 /* 可以使用表达式的结果作为键名，类似于Map */
}
/* 字典取值 */
var d = {"a": 67};
print(d.a);
print(d["a"]);

/* 匿名函数 */
function(a) {
	return a + 1;
}
```

### 运算符
NodeCode提供了一些运算符。
```nodecode
/* 数值运算 */
+ - * /
^ /* 幂运算 */
% /* 模运算 *"

/* 复合赋值 */
+= -= *= /= ^= %=

/* 逻辑运算 */
== != > >= < <=
/* 与 */ &
/* 或 */ |
/* 非 */ !

/* 三元运算符 */
cond ? then : else
```

### 变量
NodeCode支持变量的**赋值**与**修改**，语法类似JavaScript。
可以将**字面量**赋值给变量。
使用`var`关键字定义变量。不支持**空定义**。支持重定义。
```nodecode
var intnum = 42;			/* 整数 */
var floatnum = 3.14;		/* 浮点数 */

var string1 = "NodeCode";	/* 双引号字符串 */
var string2 = 'NodeCode';	/* 单引号字符串 */

var t = true;				/* 布尔真值 */
var f = false;				/* 布尔假值 */

var list = [1, 2, 3];		/* 数组 */
var dict = {
	"a": 123
};							/* 字典 */
```

### 作用域
NodeCode的变量为**块级作用域**，变量只在本级与子级内可被访问。
```nodecode
var a = 1;
(function() {
	var b = 2;
	print(a);		/* 1 */
	print(b);		/* 2 */
})();
print(b);			/* 错误: b不可访问 */
```

### 函数
NodeCode的函数以`function`关键字定义。支持**具名函数**和**匿名函数**。
函数在NodeCode中属于**一等公民**，可以作为值进行赋值和传递。
支持**闭包**。
在函数节点后使用`(arg1, arg2, ...)`执行函数。支持**IIFE**和**递归执行**。
```nodecode
/* 惯例 */
function fact(n) {
	if (n < 2) {
		return 1;
	}
	return n * fact(n - 1);
}

print(fact(5));

/* 参数定义 */
function myFunc(a, b = 1 /* 默认值 */, ...c /* 可变参数，只能位于最后 */) {}

/* 赋值 */
var funcVar = function(a) /* 使用匿名函数，可以携带参数 */ {};

/* IIFE */
(function() {print("IIFE");})();
```

### 控制流
NodeCode支持**if-else if-else**、**while**、**for**、**break**、**continue**、**return**控制流。
```nodecode
/* while循环 & break */
var i = 0;
while(true) {
	i = i + 1;
	if (i > 10) {break;}
	print(i);
}

/* for循环 */
for(var i = 0; i < 10; i = i + 1) {
	print(i);
}

/* 条件 */
if (true) {
	print(1);
} else if (true) {
	print(2);
} else {
	print(3);
}
```

### 错误处理
NodeCode支持使用**try-catch**进行错误处理，同时提供`error(msg)`来抛出自定义错误。
```nodecode
try {
	print("try");
	error("test");
} catch(e) {
	print(e);
}
```

### I/O
NodeCode提供了两个内置I/O函数：`print()`和`input()`。

### 自举
NodeCode提供了`load()`函数，支持**从文本动态加载**NodeCode代码。
加载后会包装为**函数**，作用域位于**全局**，**不立即执行**。
```nodecode
var f = load("print(\"test\");");
/* 使用 */
f();
```

### 基本库
NodeCode提供了基本的**List**、**Math**、**String**、**Type**库。

## 例子

### 工厂风格OOP
```nodecode
/* 父类 */
function Animal() {
	var self = {};
	/* 父类方法 */
	function self.speak() {
		print("...");
	}
	return self;
}

/* 子类: Cat */
function Cat(name) {
	/* 继承 */
	var self = Animal();
	/* 封装 */
	var _name = name;
	
	/* 重写父类方法 */
	function self.speak() {
		print("喵");
	}
	/* 添加子类方法 */
	function self.getName() {
		return _name;
	}
	return self;
}

/* 子类: Dog */
function Dog() {
	/* 继承 */
	var self = Animal();
	
	/* 重写父类方法 */
	function self.speak() {
		print("汪");
	}
	return self;
}

/* 实例化 */
var myCat = Cat("哈基米");
var myDog = Dog();

/* 调用方法 */
print(myCat.getName());	/* 哈基米 */

var objs = [myCat, myDog];
/* 多态 */
for (var i = 0; i < List.length(objs); i = i + 1) {
	objs[i].speak();
}
/*
	喵
	汪
*/
```
