---
title: unittest框架总结以及ddt数据驱动
date: 2024-08-29 16:18:11
tags: 
    - python 
    - web test 
    - unittest
    - ddt
    - note
toc: true
categories: 
    - Python 
    - WebTesting

---

### 快速入门
<i style='color:gray;'>本文章于 2023 年学习时记录，无法保证准确性</i>
#### 用例编写

unittest 是 Python 自带的一个单元测试框架

此外，unittest 还支持自动化测试的接口测试、UI 测试。

<!-- more -->
```python
import unittest


class TestDemo(unittest.TestCase):
    def test_01_demo(self):
        self.assertEqual(1, 2, "判断1和2相等")

    def test_02_demo(self):
        self.assertEqual(1, 1, "判断1和1相等")


if __name__ == '__main__':
    unittest.main()
```

使用 unittest 编写测试用例，必须使用**测试类**来进行。

在测试类里面的方法就是测试用例了。

注意，测试用例的方法必须是以`test`开头的才可以。

例如上述代码的`test_01_demo`

`unittest.main()`会自动的发现模块中的所有测试类中的测试用例。

在测试用例中的方法中，我们可以使用`testCase`自带的断言功能来实现对预期结果和实践
结果的判断。

#### **`TestSuite`**

我们可以使用**测试套件**，把不同的测试用例都加载添加到测试套件中

通过直接运行测试套件来实现运行不同模块中的测试用例

```python
import unittest

suite = unittest.TestSuite()
case = unittest.defaultTestLoader.discover(start_dir='.', pattern="test*.py")
suite.addTest(case)
```

上述代码中使用`unittest.defaultTestLoader.discover`方法就可以实现在指定的文件夹
中区自动的发现我们的测试用例

`start_dir`后面是测试模块所在的文件目录，如果在同一目录则是`.`

`pattern`参数后跟的是以 test 开头的 python 文件如"test01.py" "test02.py"，因此我
们文件要以有规律化的方式起名

最后使用`suite.addTest`的方法把发现的测试用例加载到测试套件中。

#### 运行套件以及生成测试报告

unittest 自带的测试报告是文本格式的，可读性不好，所以我们一般用第三方的模块来生
成测试报告

```python
import unittest
from HTMLTestRunner import HTMLTestRunner

suite = unittest.TestSuite()
case = unittest.defaultTestLoader.discover(start_dir='.', pattern="test*.py")

suite.addTest(case)

with open("测试报告.html", "wb")as f:
    runner = HTMLTestRunner(stream=f, title="测试报告", description="这是项目的描述")
    runner.run(suite)

```

`HTMLTestRunner`是我们经常使用的第三方模块，可以将生成的测试报告以 HTML 方式展现
。其中：

`stream`是要把测试报告的文件保存到那个文件目录中

`title`是测试报告的标题

`description`是对测试报告的详细描述

---

### `assert`断言

#### 常用的断言

1. `assertEqual`判断两个值是否相等
2. `assertNotEqual`判断两个值是否不相等
3. `assertTrue`判断结果是否为真
4. `assertFalse`判断结果是否为假
5. `assertIn`判断第一个值是否在第二个值范围内
6. `assertNotIn`判断第一个值是否不在第二个值的范围内
7. `assertIs`判断两个对象是否属于同一对象
8. `assertIsNot`判断两个对象是否不属于同一种对象
9. `assertIsNone`判断某个值是否为空
10. `assertIsNotNone`判断某个是是否不为空
11. `assertIsInstance`判断某个对象是否属于某个 class 实例
12. `assertNotIsInstance`判断某个对象是否不属于某个 class 实例

代码示例：

```python
import unittest

class Demo:
    pass

class Demo2:
    pass

aa = Demo
a = 1
b = a
c = None

class TestDemo(unittest.TestCase):
    def test_01_demo(self):
        self.assertEqual(1, 1, "判断两个值是否相等")
        self.assertNotEqual(1, 2, "判断两个值是否不相等")
        self.assertTrue(1 == 1, "判断结果是否为真")
        self.assertFalse(1 == 2, "判断结果是否为假")
        self.assertIn(1, [2, 1, 3], "判断第一个值是否在第二个容器内")
        self.assertNotIn(1, [3, 2, 3], "判断第一个值是否不在第二个容器内")
        self.assertIs(a, b, "判断任意两个对象是否属于同一个对象")
        self.assertIsNot(a, 3, "判断任意两个对象是否不属于同一个对象")
        self.assertIsNone(c, "判断某个值是否为空值")
        self.assertIsNotNone(a, "判断某个值是否不为空")
        self.assertIsInstance(aa, Demo, "判断某个对象是否属于某个class实例")
        self.assertNotIsInstance(aa, Demo2, "判断某个对象是否不属于某个class实例")

if __name__ == '__main__':
    unittest.main()

```

---

### `unittest`装饰器

`testCase`带有很多不同的装饰器来实现我们测试用例的运行过程。

但是在实际工作中会有大量的测试用例，但不是所有的用例都是需要运行的。

有的用例需要根据不同的情况来判断是否需要运行。

我们可以利用`testCase`自带的装饰器来实现用例运行过程的控制。

#### 装饰器

`@unittest.skip()` 控制某条用例不运行

`@unittest.skipif()` 如果条件为真，用例不运行

`@unittest.skipUnless()`如果条件为假，用例不运行

`@unittest.expectedFailure`预期测试失败，失败时输出`x`成功时输出`u`

例如：

```python
class TestDemo(unittest.TestCase):
    def test_01_demo(self):
        self.assertEqual(1, 1, "判断两个值是否相等")
        self.assertNotEqual(1, 2, "判断两个值是否不相等")

    def test_02_demo(self):
        self.assertEqual(1, 1, "判断两个值是否相等")
        self.assertNotEqual(1, 1, "判断两个值是否不相等")

    @unittest.skip("跳过第三条用例")
    def test_03_demo(self):
        self.assertEqual(1, 1, "判断两个值是否相等")
        self.assertNotEqual(1, 2, "判断两个值是否不相等")

    @unittest.skipIf(True, "结果为真，跳过用例")
    def test_04_demo(self):
        self.assertEqual(1, 1, "判断两个值是否相等")
        self.assertNotEqual(1, 2, "判断两个值是否不相等")

    @unittest.skipUnless(False, "结果为假，跳过用例")
    def test_05_demo(self):
        self.assertEqual(1, 1, "判断两个值是否相等")
        self.assertNotEqual(1, 2, "判断两个值是否不相等")

    @unittest.expectedFailure # 预期失败
    def test_06_demo(self):
        self.assertEqual(1, 1, "判断两个值是否相等")
        self.assertNotEqual(1, 1, "判断两个值是否不相等")

    @unittest.expectedFailure
    def test_07_demo(self):
        self.assertEqual(1, 1, "判断两个值是否相等")
        self.assertNotEqual(1, 2, "判断两个值是否不相等")


```

控制台结果：

```python
.Fsssxu
======================================================================
FAIL: test_02_demo (__main__.TestDemo.test_02_demo)
----------------------------------------------------------------------
```

#### 运行结果的说明

`.`表示测试通过

`F`表示测试失败

`s`表示测试跳过

`x`表示预期失败

`u`表示与预期相反

---

### `fixture`测试夹具

以初始化的夹具在前，测试运行结束后的夹具在后运行测试用例。

测试夹具的作用可以把我们在测试运行前和运行结束后需要运行的代码抽离出来，单独放到
夹具中区运行。方便我们对代码的组织和维护，减少重复的代码量。

`setUp`以及`setDown`是面向测试用例

`setUpClass`以及`tearDownClass`是面向测试类

两者可以组合使用同时出现

<!--此外还有setUpModule以及tearDownModule是面向测试模块的-->

#### `setUp`

在每个测试用例前运行，一般在里面写一些实现测试用例的前置条件

#### `tearDown`

在每个测试用例运行结束后运行，一般在里面写一些清理环境，释放资源之类的代码

#### `setUpClass`

在每个测试类运行前运行 代码前需要带上`@classmethod` 装饰器

#### `tearDownClass`

在每个测试类结束运行之后运行 代码前需要带上`@classmethod` 装饰器

#### 示例代码

```python
class TestDemo(unittest.TestCase):
    def setUp(self) -> None:
        print("开始运行测试用例")

    def tearDown(self) -> None:
        print("测试用例运行结束")

    @classmethod
    def setUpClass(cls) -> None:
        print("开始运行测试类")

    @classmethod
    def tearDownClass(cls) -> None:
        print("测试类结束运行")

    def test_01_demo(self):
        self.assertEqual(1, 1, "判断是否相等")

    def test_02_demo(self):
        self.assertEqual(1, 1, "判断是否相等")

    def test_03_demo(self):
        self.assertEqual(1, 1, "判断是否相等")
```

---

### 清理函数

清理函数默认在`tearDown`后运行

清理函数的使用，需要先自己封装一个方法，这个方法里面的的代码就是用来清理的

清理函数可以放在测试类中的任何位置，通过`addCleanup()`注册我们的清理函数

清理函数可以使用`doCleanups()`对某个用例生效，也可放在`setUp`中对所有用例生效

在 python3.9 之后新增了`addClassCleanup`以及`doClassCleanup`，他们是针对测试类进
行清理，用法同上。

`addClassCleanup`一般与`setClassUp`搭配使用

```py
import unittest

def clear():
    print("开始清理函数")

class TestDemo(unittest.TestCase):
    def setUp(self) -> None:
        self.addCleanup(clear)
        print("开始运行测试")
    def tearDown(self) -> None:
        print("测试运行结束")
    def test_01_demo(self):
        self.assertEqual(1, 1, "判断是否相等")
    def test_02_demo(self):
        self.assertEqual(1, 1, "判断是否相等")
    def test_03_demo(self):
        slf.assertEqual(1, 1, "判断是否相等")
        # self.doCleanups()
    def test_04_demo(self):
        self.assertEqual(1, 1, "判断是否相等")
    def test_05_demo(self):
        self.assertEqual(1, 1, "判断是否相等")

if __name__ == '__main__':
    unittest.main()
```

注意：清理函数不是必须要和夹具配置使用，也可以单独使用。

---

### ddt 数据驱动

#### 介绍

ddt 是 python 的一个专门从来实现数据驱动的第三方的包

使用 ddt 数据驱动来实现数据与测试分离管理

```
# 使用pip下载
pip install ddt -i https://pypi.douban.com/simple
```

下载后即可通过导包来使用 ddt

```python
# 使用Python导包
form ddt import ddt, data, unpack, file_data # 装饰器
```

#### 各项装饰器的使用

##### ddt

在测试用例前加上`@ddt`装饰器即可使用 ddt 数据驱动

```python
@ddt
class TestDemo(unittest.TestCase):
```

##### data

需要进行测试的数据，放在 data 中。在测试中直接引用 value 即可

```python
from ddt import ddt, data, unpack, file_data
import unittest

@ddt
class TestDemo(unittest.TestCase):
    @data(1, 2, 3)
    def test_01_demo(self, value):
        self.assertEqual(1, value, "判断是否相等")

if __name__ == '__main__':
    unittest.main()
```

##### file_data

可以实现自动的从文件中读取测试数据，不需要像 data 一样手动传入数据。

可以使用 json 或者 yaml

例如在同目录下有名为 data.json 数据文件时

```json
{
	"01": { "name": "张三", "age": 23 },
	"02": { "name": "李四", "age": 24 },
	"03": { "name": "王五", "age": 25 }
}
```

在测试时可以用@file_data 直接引用

```python
from ddt import ddt, data, unpack, file_data
import unittest

@ddt
class TestDemo(unittest.TestCase):
	@file_data("data.json")
    @unpack
    def test_05_demo(self, age, name):
        self.assertEqual(23, age, name)

if __name__ == '__main__':
    unittest.main()
```

同理在引用 yaml 文件时操作相同，但注意：

ddt 是直接支持 json 格式文件的读取，但是对于 yaml 文件需要额外安装一个第三方的
python 包才可以。pyyaml

```
pip install pyyaml -i https://douban.com/simple
```

##### unpack

对数据进行解包，特别在每项数据都是成组出现时

例如数据内为元组时

```python
from ddt import ddt, data, unpack, file_data
import unittest

@ddt
class TestDemo(unittest.TestCase):
    # 不使用unpack时
    @data((1, 2), (2, 2), (3, 4))
    def test_01_demo(self, value):
        self.assertEqual(value[0], value[1], "判断是否相等")
    # 使用unpack时
    @data((1, 2), (2, 2), (3, 4))
    @unpack
    def test_02_demo(self,value1, value2):
        self.assertEqual(value1, value2, "判断是否相等")

if __name__ == '__main__':
    unittest.main()
```

例如数据内为字典时

```python
from ddt import ddt, data, unpack, file_data
import unittest


@ddt
class TestDemo(unittest.TestCase):

    @data({"name": "张三", "age": 23}, {"name": "李四", "age": 24}, {"name": "王五", "age": 25})
    @unpack
    def test_01_demo(self, name, age):
        self.assertEqual(23, age, name)


if __name__ == '__main__':
    unittest.main()
```
