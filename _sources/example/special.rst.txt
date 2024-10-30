可以查看所有收集到的测试的会话fixture
----------------------------------------------------------------

**A session-fixture which can look at all collected tests**

.. tabs::

    .. tab:: 中文

        A session-scoped fixture 有效地访问所有收集的测试项。下面是一个示例的 fixture 函数，它遍历所有收集的测试，并查看它们的测试类是否定义了 ``callme`` 方法并调用它：

        .. code-block:: python

            # content of conftest.py

            import pytest


            @pytest.fixture(scope="session", autouse=True)
            def callattr_ahead_of_alltests(request):
                print("callattr_ahead_of_alltests called")
                seen = {None}
                session = request.node
                for item in session.items:
                    cls = item.getparent(pytest.Class)
                    if cls not in seen:
                        if hasattr(cls.obj, "callme"):
                            cls.obj.callme()
                        seen.add(cls)

        测试类现在可以定义 ``callme`` 方法，该方法将在运行任何测试之前被调用：

        .. code-block:: python

            # content of test_module.py


            class TestHello:
                @classmethod
                def callme(cls):
                    print("callme called!")

                def test_method1(self):
                    print("test_method1 called")

                def test_method2(self):
                    print("test_method2 called")


            class TestOther:
                @classmethod
                def callme(cls):
                    print("callme other called")

                def test_other(self):
                    print("test other")


            # 也适用于 unittest ...
            import unittest


            class SomeTest(unittest.TestCase):
                @classmethod
                def callme(self):
                    print("SomeTest callme called")

                def test_unit1(self):
                    print("test_unit1 method called")

        如果你在没有输出捕获的情况下运行：

        .. code-block:: pytest

            $ pytest -q -s test_module.py
            callattr_ahead_of_alltests called
            callme called!
            callme other called
            SomeTest callme called
            test_method1 called
            .test_method2 called
            .test other
            .test_unit1 method called
            .
            4 passed in 0.12s

    .. tab:: 英文

        A session-scoped fixture effectively has access to all
        collected test items.  Here is an example of a fixture
        function which walks all collected tests and looks
        if their test class defines a ``callme`` method and
        calls it:

        .. code-block:: python

            # content of conftest.py

            import pytest


            @pytest.fixture(scope="session", autouse=True)
            def callattr_ahead_of_alltests(request):
                print("callattr_ahead_of_alltests called")
                seen = {None}
                session = request.node
                for item in session.items:
                    cls = item.getparent(pytest.Class)
                    if cls not in seen:
                        if hasattr(cls.obj, "callme"):
                            cls.obj.callme()
                        seen.add(cls)

        test classes may now define a ``callme`` method which
        will be called ahead of running any tests:

        .. code-block:: python

            # content of test_module.py


            class TestHello:
                @classmethod
                def callme(cls):
                    print("callme called!")

                def test_method1(self):
                    print("test_method1 called")

                def test_method2(self):
                    print("test_method2 called")


            class TestOther:
                @classmethod
                def callme(cls):
                    print("callme other called")

                def test_other(self):
                    print("test other")


            # works with unittest as well ...
            import unittest


            class SomeTest(unittest.TestCase):
                @classmethod
                def callme(self):
                    print("SomeTest callme called")

                def test_unit1(self):
                    print("test_unit1 method called")

        If you run this without output capturing:

        .. code-block:: pytest

            $ pytest -q -s test_module.py
            callattr_ahead_of_alltests called
            callme called!
            callme other called
            SomeTest callme called
            test_method1 called
            .test_method2 called
            .test other
            .test_unit1 method called
            .
            4 passed in 0.12s
