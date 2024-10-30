.. _get-started:

入门
===================================

**Get Started**

.. _`getstarted`:
.. _`installation`:

安装 ``pytest``
----------------------------------------

**Install** ``pytest``

.. tabs::

    .. tab:: 中文

        ``pytest`` 需要：Python 3.8+ 或 PyPy3。

        1. 在命令行中运行以下命令：

        .. code-block:: bash

            pip install -U pytest

        2. 检查是否安装了正确的版本：

        .. code-block:: bash

            $ pytest --version
            pytest 8.3.3

    .. tab:: 英文

        ``pytest`` requires: Python 3.8+ or PyPy3.

        1. Run the following command in your command line:

        .. code-block:: bash

            pip install -U pytest

        2. Check that you installed the correct version:

        .. code-block:: bash

            $ pytest --version
            pytest 8.3.3

.. _`simpletest`:

创建你的第一个测试
----------------------------------------------------------

**Create your first test**

.. tabs::

    .. tab:: 中文

        创建一个名为 ``test_sample.py`` 的新文件，其中包含一个函数和一个测试：

        .. code-block:: python

            # test_sample.py 的内容
            def func(x):
                return x + 1


            def test_answer():
                assert func(3) == 5

        测试

        .. code-block:: pytest

            $ pytest
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 1 item

            test_sample.py F                                                     [100%]

            ================================= FAILURES =================================
            _______________________________ test_answer ________________________________

                def test_answer():
            >       assert func(3) == 5
            E       assert 4 == 5
            E        +  where 4 = func(3)

            test_sample.py:6: AssertionError
            ========================= short test summary info ==========================
            FAILED test_sample.py::test_answer - assert 4 == 5
            ============================ 1 failed in 0.12s =============================

        ``[100%]`` 指的是运行所有测试用例的总体进度。完成后，pytest 会显示失败报告，因为 ``func(3)`` 没有返回 ``5``。

        .. note::

            您可以使用 ``assert`` 语句来验证测试预期。pytest 的 :ref:`高级断言自省 <python:assert>` 将智能地报告断言表达式的中间值，这样您就可以避免 :ref:`JUnit 遗留方法 <testcase-objects>` 的许多名称。

    .. tab:: 英文

        Create a new file called ``test_sample.py``, containing a function, and a test:

        .. code-block:: python

            # content of test_sample.py
            def func(x):
                return x + 1


            def test_answer():
                assert func(3) == 5

        The test

        .. code-block:: pytest

            $ pytest
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 1 item

            test_sample.py F                                                     [100%]

            ================================= FAILURES =================================
            _______________________________ test_answer ________________________________

                def test_answer():
            >       assert func(3) == 5
            E       assert 4 == 5
            E        +  where 4 = func(3)

            test_sample.py:6: AssertionError
            ========================= short test summary info ==========================
            FAILED test_sample.py::test_answer - assert 4 == 5
            ============================ 1 failed in 0.12s =============================

        The ``[100%]`` refers to the overall progress of running all test cases. After it finishes, pytest then shows a failure report because ``func(3)`` does not return ``5``.

        .. note::

            You can use the ``assert`` statement to verify test expectations. pytest’s :ref:`Advanced assertion introspection <python:assert>` will intelligently report intermediate values of the assert expression so you can avoid the many names :ref:`of JUnit legacy methods <testcase-objects>`.

运行多个测试
----------------------------------------------------------

**Run multiple tests**

.. tabs::

    .. tab:: 中文

        ``pytest`` 将在当前目录及其子目录中运行所有形式为 test_*.py 或 \*_test.py 的文件。更一般地，它遵循: :ref:`标准测试发现规则 <test discovery>`。

    .. tab:: 英文

        ``pytest`` will run all files of the form test_*.py or \*_test.py in the current directory and its subdirectories. More generally, it follows :ref:`standard test discovery rules <test discovery>`.


断言引发了某个异常
--------------------------------------------------------------

**Assert that a certain exception is raised**

.. tabs::

    .. tab:: 中文

        使用 :ref:`raises <assertraises>` 辅助程序断言某些代码引发了异常：

        .. code-block:: python

            # test_sysexit.py的内容
            import pytest


            def f():
                raise SystemExit(1)


            def test_mytest():
                with pytest.raises(SystemExit):
                    f()

        您还可以使用 :ref:`raises <assertraises>` 提供的上下文来断言预期异常是引发的 :class:`ExceptionGroup` 的一部分：

        .. code-block:: python

            # test_exceptiongroup.py的内容
            import pytest


            def f():
                raise ExceptionGroup(
                    "Group message",
                    [
                        RuntimeError(),
                    ],
                )


            def test_exception_in_group():
                with pytest.raises(ExceptionGroup) as excinfo:
                    f()
                assert excinfo.group_contains(RuntimeError)
                assert not excinfo.group_contains(TypeError)

        使用“安静(quiet)”报告模式执行测试函数：

        .. code-block:: pytest

            $ pytest -q test_sysexit.py
            .                                                                    [100%]
            1 passed in 0.12s

        .. note::

            ``-q/--quiet`` 标志可在此示例和后续示例中保持简短的输出。

    .. tab:: 英文

        Use the :ref:`raises <assertraises>` helper to assert that some code raises an exception:

        .. code-block:: python

            # content of test_sysexit.py
            import pytest


            def f():
                raise SystemExit(1)


            def test_mytest():
                with pytest.raises(SystemExit):
                    f()

        You can also use the context provided by :ref:`raises <assertraises>` to
        assert that an expected exception is part of a raised :class:`ExceptionGroup`:

        .. code-block:: python

            # content of test_exceptiongroup.py
            import pytest


            def f():
                raise ExceptionGroup(
                    "Group message",
                    [
                        RuntimeError(),
                    ],
                )


            def test_exception_in_group():
                with pytest.raises(ExceptionGroup) as excinfo:
                    f()
                assert excinfo.group_contains(RuntimeError)
                assert not excinfo.group_contains(TypeError)

        Execute the test function with “quiet” reporting mode:

        .. code-block:: pytest

            $ pytest -q test_sysexit.py
            .                                                                    [100%]
            1 passed in 0.12s

        .. note::

            The ``-q/--quiet`` flag keeps the output brief in this and following examples.

在一个类(class)中对多个测试进行分组
--------------------------------------------------------------

**Group multiple tests in a class**

.. tabs::

    .. tab:: 中文

        .. regendoc:wipe

        一旦您开发了多个测试，您可能希望将它们分组到一个类中。pytest 使得创建包含多个测试的类变得简单：

        .. code-block:: python

            # test_class.py 的内容
            class TestClass:
                def test_one(self):
                    x = "this"
                    assert "h" in x

                def test_two(self):
                    x = "hello"
                    assert hasattr(x, "check")

        ``pytest`` 会发现所有遵循其 :ref:`Python 测试发现约定 <test discovery>` 的测试，因此它会找到两个以 ``test_`` 为前缀的函数。无需继承任何类，但请确保将类名前缀为 ``Test``，否则该类将被跳过。我们可以简单地通过传递文件名来运行该模块：

        .. code-block:: pytest

            $ pytest -q test_class.py
            .F                                                                   [100%]
            ================================= FAILURES =================================
            ____________________________ TestClass.test_two ____________________________

            self = <test_class.TestClass object at 0xdeadbeef0001>

                def test_two(self):
                    x = "hello"
            >       assert hasattr(x, "check")
            E       AssertionError: assert False
            E        +  where False = hasattr('hello', 'check')

            test_class.py:8: AssertionError
            ========================= 测试摘要信息 ==========================
            FAILED test_class.py::TestClass::test_two - AssertionError: assert False
            1 failed, 1 passed in 0.12s

        第一个测试通过，第二个测试失败。您可以轻松查看断言中的中间值，以帮助您理解失败的原因。

        将测试分组到类中可以带来以下好处：

        * 测试组织
        * 仅在特定类中共享夹具
        * 在类级别应用标记，并使其隐式适用于所有测试

        在类中分组测试时需要注意的是，每个测试都有一个唯一的类实例。
        让每个测试共享相同的类实例将对测试隔离产生非常不利的影响，并会促进不良的测试实践。
        这一点在下面阐述：

        .. regendoc:wipe

        .. code-block:: python

            # test_class_demo.py 的内容
            class TestClassDemoInstance:
                value = 0

                def test_one(self):
                    self.value = 1
                    assert self.value == 1

                def test_two(self):
                    assert self.value == 1


        .. code-block:: pytest

            $ pytest -k TestClassDemoInstance -q
            .F                                                                   [100%]
            ================================= FAILURES =================================
            ______________________ TestClassDemoInstance.test_two ______________________

            self = <test_class_demo.TestClassDemoInstance object at 0xdeadbeef0002>

                def test_two(self):
            >       assert self.value == 1
            E       assert 0 == 1
            E        +  where 0 = <test_class_demo.TestClassDemoInstance object at 0xdeadbeef0002>.value

            test_class_demo.py:9: AssertionError
            ========================= 测试摘要信息 ==========================
            FAILED test_class_demo.py::TestClassDemoInstance::test_two - assert 0 == 1
            1 failed, 1 passed in 0.12s

        请注意，在类级别添加的属性是 *类属性(class attributes)*，因此它们将在测试之间共享。

    .. tab:: 英文

        .. regendoc:wipe

        Once you develop multiple tests, you may want to group them into a class. pytest makes it easy to create a class containing more than one test:

        .. code-block:: python

            # content of test_class.py
            class TestClass:
                def test_one(self):
                    x = "this"
                    assert "h" in x

                def test_two(self):
                    x = "hello"
                    assert hasattr(x, "check")

        ``pytest`` discovers all tests following its :ref:`Conventions for Python test discovery <test discovery>`, so it finds both ``test_`` prefixed functions. There is no need to subclass anything, but make sure to prefix your class with ``Test`` otherwise the class will be skipped. We can simply run the module by passing its filename:

        .. code-block:: pytest

            $ pytest -q test_class.py
            .F                                                                   [100%]
            ================================= FAILURES =================================
            ____________________________ TestClass.test_two ____________________________

            self = <test_class.TestClass object at 0xdeadbeef0001>

                def test_two(self):
                    x = "hello"
            >       assert hasattr(x, "check")
            E       AssertionError: assert False
            E        +  where False = hasattr('hello', 'check')

            test_class.py:8: AssertionError
            ========================= short test summary info ==========================
            FAILED test_class.py::TestClass::test_two - AssertionError: assert False
            1 failed, 1 passed in 0.12s

        The first test passed and the second failed. You can easily see the intermediate values in the assertion to help you understand the reason for the failure.

        Grouping tests in classes can be beneficial for the following reasons:

        * Test organization
        * Sharing fixtures for tests only in that particular class
        * Applying marks at the class level and having them implicitly apply to all tests

        Something to be aware of when grouping tests inside classes is that each test has a unique instance of the class.
        Having each test share the same class instance would be very detrimental to test isolation and would promote poor test practices.
        This is outlined below:

        .. regendoc:wipe

        .. code-block:: python

            # content of test_class_demo.py
            class TestClassDemoInstance:
                value = 0

                def test_one(self):
                    self.value = 1
                    assert self.value == 1

                def test_two(self):
                    assert self.value == 1


        .. code-block:: pytest

            $ pytest -k TestClassDemoInstance -q
            .F                                                                   [100%]
            ================================= FAILURES =================================
            ______________________ TestClassDemoInstance.test_two ______________________

            self = <test_class_demo.TestClassDemoInstance object at 0xdeadbeef0002>

                def test_two(self):
            >       assert self.value == 1
            E       assert 0 == 1
            E        +  where 0 = <test_class_demo.TestClassDemoInstance object at 0xdeadbeef0002>.value

            test_class_demo.py:9: AssertionError
            ========================= short test summary info ==========================
            FAILED test_class_demo.py::TestClassDemoInstance::test_two - assert 0 == 1
            1 failed, 1 passed in 0.12s

        Note that attributes added at class level are *class attributes*, so they will be shared between tests.

为功能测试请求唯一的临时目录
--------------------------------------------------------------

**Request a unique temporary directory for functional tests**

.. tabs::

    .. tab:: 中文

        ``pytest`` 提供了 :std:doc:`内置夹具/函数参数 <builtin>` 来请求任意资源，比如一个唯一的临时目录：

        .. code-block:: python

            # test_tmp_path.py 的内容
            def test_needsfiles(tmp_path):
                print(tmp_path)
                assert 0

        在测试函数签名中列出名称 ``tmp_path`` ， ``pytest`` 会查找并调用夹具工厂，在执行测试函数调用之前创建资源。在测试运行之前， ``pytest`` 会为每次测试调用创建一个唯一的临时目录：

        .. code-block:: pytest

            $ pytest -q test_tmp_path.py
            F                                                                    [100%]
            ================================= FAILURES =================================
            _____________________________ test_needsfiles ______________________________

            tmp_path = PosixPath('PYTEST_TMPDIR/test_needsfiles0')

                def test_needsfiles(tmp_path):
                    print(tmp_path)
            >       assert 0
            E       assert 0

            test_tmp_path.py:3: AssertionError
            --------------------------- Captured stdout call ---------------------------
            PYTEST_TMPDIR/test_needsfiles0
            ========================= short test summary info ==========================
            FAILED test_tmp_path.py::test_needsfiles - assert 0
            1 failed in 0.12s

        有关临时目录处理的更多信息，请参见 :ref:`临时目录和文件 <tmp_path handling>`。

        使用以下命令查找存在的内置 :ref:`pytest 夹具 <fixtures>` 的类型：

        .. code-block:: bash

            pytest --fixtures   # 显示内置和自定义夹具

        请注意，此命令省略以 ``_`` 开头的夹具，除非添加了 ``-v`` 选项。

    .. tab:: 英文

        ``pytest`` provides :std:doc:`Builtin fixtures/function arguments <builtin>` to request arbitrary resources, like a unique temporary directory:

        .. code-block:: python

            # content of test_tmp_path.py
            def test_needsfiles(tmp_path):
                print(tmp_path)
                assert 0

        List the name ``tmp_path`` in the test function signature and ``pytest`` will lookup and call a fixture factory to create the resource before performing the test function call. Before the test runs, ``pytest`` creates a unique-per-test-invocation temporary directory:

        .. code-block:: pytest

            $ pytest -q test_tmp_path.py
            F                                                                    [100%]
            ================================= FAILURES =================================
            _____________________________ test_needsfiles ______________________________

            tmp_path = PosixPath('PYTEST_TMPDIR/test_needsfiles0')

                def test_needsfiles(tmp_path):
                    print(tmp_path)
            >       assert 0
            E       assert 0

            test_tmp_path.py:3: AssertionError
            --------------------------- Captured stdout call ---------------------------
            PYTEST_TMPDIR/test_needsfiles0
            ========================= short test summary info ==========================
            FAILED test_tmp_path.py::test_needsfiles - assert 0
            1 failed in 0.12s

        More info on temporary directory handling is available at :ref:`Temporary directories and files <tmp_path handling>`.

        Find out what kind of builtin :ref:`pytest fixtures <fixtures>` exist with the command:

        .. code-block:: bash

            pytest --fixtures   # shows builtin and custom fixtures

        Note that this command omits fixtures with leading ``_`` unless the ``-v`` option is added.

继续阅读
-------------------------------------

**Continue reading**

.. tabs::

    .. tab:: 中文

        查看其他 pytest 资源，以帮助您根据自己的独特工作流程自定义测试：

        * ":ref:`usage`" 了解命令行调用示例
        * ":ref:`existingtestsuite`" 了解使用现有测试的情况
        * ":ref:`mark`" 了解 ``pytest.mark`` 机制的信息
        * ":ref:`fixtures`" 为您的测试提供功能基准
        * ":ref:`plugins`" 了解管理和编写插件的情况
        * ":ref:`goodpractices`" 了解虚拟环境和测试布局的情况

    .. tab:: 英文

        Check out additional pytest resources to help you customize tests for your unique workflow:

        * ":ref:`usage`" for command line invocation examples
        * ":ref:`existingtestsuite`" for working with preexisting tests
        * ":ref:`mark`" for information on the ``pytest.mark`` mechanism
        * ":ref:`fixtures`" for providing a functional baseline to your tests
        * ":ref:`plugins`" for managing and writing plugins
        * ":ref:`goodpractices`" for virtualenv and test layouts
