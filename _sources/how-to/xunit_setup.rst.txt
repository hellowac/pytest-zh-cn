
.. _`classic xunit`:
.. _xunitsetup:

如何实现 xunit 样式的设置
========================================

**How to implement xunit-style set-up**

.. tabs::

    .. tab:: 中文

        本节描述了一种经典且流行的方式，您可以在每个模块/类/函数的基础上实现 fixtures（设置和拆卸测试状态）。

        .. note::

            虽然这些设置/拆卸方法对于来自 ``unittest`` 或 ``nose`` 背景的人来说简单且熟悉，但您也可以考虑使用 pytest 更强大的 :ref:`fixture 机制 <fixture>`，该机制利用依赖注入的概念，允许以更模块化和可扩展的方式管理测试状态，尤其适用于较大项目和功能测试。您可以在同一个文件中混合使用这两种 fixture 机制，但 ``unittest.TestCase`` 子类的测试方法不能接收 fixture 参数。

    .. tab:: 英文

        This section describes a classic and popular way how you can implement
        fixtures (setup and teardown test state) on a per-module/class/function basis.


        .. note::

            While these setup/teardown methods are simple and familiar to those
            coming from a ``unittest`` or ``nose`` background, you may also consider
            using pytest's more powerful :ref:`fixture mechanism
            <fixture>` which leverages the concept of dependency injection, allowing
            for a more modular and more scalable approach for managing test state,
            especially for larger projects and for functional testing.  You can
            mix both fixture mechanisms in the same file but
            test methods of ``unittest.TestCase`` subclasses
            cannot receive fixture arguments.


模块级别设置/拆卸
--------------------------------------

**Module level setup/teardown**

.. tabs::

    .. tab:: 中文

        如果您在单个模块中有多个测试函数和测试类，您可以选择实现以下 fixture 方法，这些方法通常会为所有函数调用一次：

        .. code-block:: python

            def setup_module(module):
                """设置与给定模块的执行相关的任何状态。"""


            def teardown_module(module):
                """拆卸之前通过 setup_module 方法设置的任何状态。
                """

        从 pytest-3.0 开始，``module`` 参数是可选的。

    .. tab:: 英文

        If you have multiple test functions and test classes in a single
        module you can optionally implement the following fixture methods
        which will usually be called once for all the functions:

        .. code-block:: python

            def setup_module(module):
                """setup any state specific to the execution of the given module."""


            def teardown_module(module):
                """teardown any state that was previously setup with a setup_module
                method.
                """

        As of pytest-3.0, the ``module`` parameter is optional.

类级别设置/拆卸
----------------------------------

**Class level setup/teardown**

.. tabs::

    .. tab:: 中文

        类似地，以下方法在所有测试方法调用之前和之后在类级别被调用：

        .. code-block:: python

            @classmethod
            def setup_class(cls):
                """设置与给定类的执行相关的任何状态（通常包含测试）。
                """


            @classmethod
            def teardown_class(cls):
                """拆卸之前通过调用 setup_class 设置的任何状态。
                """

    .. tab:: 英文

        Similarly, the following methods are called at class level before
        and after all test methods of the class are called:

        .. code-block:: python

            @classmethod
            def setup_class(cls):
                """setup any state specific to the execution of the given class (which
                usually contains tests).
                """


            @classmethod
            def teardown_class(cls):
                """teardown any state that was previously setup with a call to
                setup_class.
                """

.. _xunit-method-setup:

方法和函数级别设置/拆卸
-----------------------------------------------

**Method and function level setup/teardown**

.. tabs::

    .. tab:: 中文

        类似地，以下方法在每个方法调用周围被调用：

        .. code-block:: python

            def setup_method(self, method):
                """设置与给定方法在类中的执行相关的任何状态。
                setup_method 对类的每个测试方法都会被调用。
                """


            def teardown_method(self, method):
                """拆卸之前通过调用 setup_method 设置的任何状态。
                """

        从 pytest-3.0 开始， ``method`` 参数是可选的。

        如果你更希望在模块级别直接定义测试函数，你也可以使用以下函数来实现 fixtures：

        .. code-block:: python

            def setup_function(function):
                """设置与给定函数执行相关的任何状态。
                对模块中的每个测试函数调用。
                """


            def teardown_function(function):
                """拆卸之前通过调用 setup_function 设置的任何状态。
                """

        从 pytest-3.0 开始，``function`` 参数是可选的。

        备注：

        * 每个测试进程可以多次调用 setup/teardown 配对。

        * 如果对应的 setup 函数存在并且失败/被跳过，则不会调用 teardown 函数。

        * 在 pytest-4.2 之前，xunit 风格的函数不遵循 fixtures 的作用域规则，因此，例如，可以在会话作用域的 autouse fixture 之前调用 ``setup_method``。

          现在，xunit 风格的函数已与 fixture 机制集成，并遵循调用中涉及的 fixtures 的正确作用域规则。

    .. tab:: 英文

        Similarly, the following methods are called around each method invocation:

        .. code-block:: python

            def setup_method(self, method):
                """setup any state tied to the execution of the given method in a
                class.  setup_method is invoked for every test method of a class.
                """


            def teardown_method(self, method):
                """teardown any state that was previously setup with a setup_method
                call.
                """

        As of pytest-3.0, the ``method`` parameter is optional.

        If you would rather define test functions directly at module level
        you can also use the following functions to implement fixtures:

        .. code-block:: python

            def setup_function(function):
                """setup any state tied to the execution of the given function.
                Invoked for every test function in the module.
                """


            def teardown_function(function):
                """teardown any state that was previously setup with a setup_function
                call.
                """

        As of pytest-3.0, the ``function`` parameter is optional.

        Remarks:

        * It is possible for setup/teardown pairs to be invoked multiple times per testing process.

        * teardown functions are not called if the corresponding setup function existed and failed/was skipped.

        * Prior to pytest-4.2, xunit-style functions did not obey the scope rules of fixtures, so it was possible, for example, for a ``setup_method`` to be called before a session-scoped autouse fixture.

        Now the xunit-style functions are integrated with the fixture mechanism and obey the proper scope rules of fixtures involved in the call.
