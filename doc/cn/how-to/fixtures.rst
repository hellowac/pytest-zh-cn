.. _how-to-fixtures:

如何使用 fixture
====================

**How to use fixtures**

.. seealso:: :ref:`about-fixtures`
.. seealso:: :ref:`Fixtures 参考 <reference-fixtures>`


"请求" fixtures
---------------------

**"Requesting" fixtures**

.. tabs::

    .. tab:: 中文

        基本上，测试函数通过将所需的 fixture 声明为参数来请求它们。

        当 pytest 准备运行测试时，它会查看该测试函数的参数签名，然后搜索与这些参数同名的 fixture。一旦 pytest 找到这些 fixture，它会运行它们，捕获它们的返回值（如果有的话），并将这些对象作为参数传递给测试函数。

    .. tab:: 英文

        At a basic level, test functions request fixtures they require by declaring
        them as arguments.

        When pytest goes to run a test, it looks at the parameters in that test
        function's signature, and then searches for fixtures that have the same names as
        those parameters. Once pytest finds them, it runs those fixtures, captures what
        they returned (if anything), and passes those objects into the test function as
        arguments.


快速示例
^^^^^^^^^^^^^

**Quick example**

.. tabs::

    .. tab:: 中文

        .. code-block:: python

            import pytest


            class Fruit:
                def __init__(self, name):
                    self.name = name
                    self.cubed = False

                def cube(self):
                    self.cubed = True


            class FruitSalad:
                def __init__(self, *fruit_bowl):
                    self.fruit = fruit_bowl
                    self._cube_fruit()

                def _cube_fruit(self):
                    for fruit in self.fruit:
                        fruit.cube()


            # Arrange
            @pytest.fixture
            def fruit_bowl():
                return [Fruit("apple"), Fruit("banana")]


            def test_fruit_salad(fruit_bowl):
                # Act
                fruit_salad = FruitSalad(*fruit_bowl)

                # Assert
                assert all(fruit.cubed for fruit in fruit_salad.fruit)

        在这个例子中， ``test_fruit_salad`` "**请求(requests)**" 了 ``fruit_bowl`` （即 ``def test_fruit_salad(fruit_bowl):`` ），当 pytest 看到这一点时，它会执行 ``fruit_bowl`` fixture 函数，并将其返回的对象作为 ``fruit_bowl`` 参数传递给 ``test_fruit_salad``。

        如果我们手动执行这个过程，以下是大致发生的事情：

        .. code-block:: python

            def fruit_bowl():
                return [Fruit("apple"), Fruit("banana")]


            def test_fruit_salad(fruit_bowl):
                # Act
                fruit_salad = FruitSalad(*fruit_bowl)

                # Assert
                assert all(fruit.cubed for fruit in fruit_salad.fruit)


            # Arrange
            bowl = fruit_bowl()
            test_fruit_salad(fruit_bowl=bowl)

    .. tab:: 英文

        .. code-block:: python

            import pytest


            class Fruit:
                def __init__(self, name):
                    self.name = name
                    self.cubed = False

                def cube(self):
                    self.cubed = True


            class FruitSalad:
                def __init__(self, *fruit_bowl):
                    self.fruit = fruit_bowl
                    self._cube_fruit()

                def _cube_fruit(self):
                    for fruit in self.fruit:
                        fruit.cube()


            # Arrange
            @pytest.fixture
            def fruit_bowl():
                return [Fruit("apple"), Fruit("banana")]


            def test_fruit_salad(fruit_bowl):
                # Act
                fruit_salad = FruitSalad(*fruit_bowl)

                # Assert
                assert all(fruit.cubed for fruit in fruit_salad.fruit)

        In this example, ``test_fruit_salad`` "**requests**" ``fruit_bowl`` (i.e.
        ``def test_fruit_salad(fruit_bowl):``), and when pytest sees this, it will
        execute the ``fruit_bowl`` fixture function and pass the object it returns into
        ``test_fruit_salad`` as the ``fruit_bowl`` argument.

        Here's roughly
        what's happening if we were to do it by hand:

        .. code-block:: python

            def fruit_bowl():
                return [Fruit("apple"), Fruit("banana")]


            def test_fruit_salad(fruit_bowl):
                # Act
                fruit_salad = FruitSalad(*fruit_bowl)

                # Assert
                assert all(fruit.cubed for fruit in fruit_salad.fruit)


            # Arrange
            bowl = fruit_bowl()
            test_fruit_salad(fruit_bowl=bowl)


fixture 可以 **请求** 其他 fixture
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Fixtures can **request** other fixtures

.. tabs::

    .. tab:: 中文

        pytest 的一个最大优势是其极其灵活的 fixture 系统。它允许我们将测试的复杂要求简化为更简单和更有组织的函数，我们只需让每个函数描述其依赖的内容。我们稍后会详细讨论这一点，但现在，这里有一个快速示例，演示 fixture 如何使用其他 fixture：

        .. code-block:: python

            # contents of test_append.py
            import pytest


            # Arrange
            @pytest.fixture
            def first_entry():
                return "a"


            # Arrange
            @pytest.fixture
            def order(first_entry):
                return [first_entry]


            def test_string(order):
                # Act
                order.append("b")

                # Assert
                assert order == ["a", "b"]


        注意，这与上面的例子相同，但变化很小。pytest 中的 fixtures **请求** fixtures，就像测试一样。所有相同的 **请求** 规则适用于 fixtures，就像适用于测试一样。以下是这个例子如果我们手动执行将如何进行：

        .. code-block:: python

            def first_entry():
                return "a"


            def order(first_entry):
                return [first_entry]


            def test_string(order):
                # Act
                order.append("b")

                # Assert
                assert order == ["a", "b"]


            entry = first_entry()
            the_list = order(first_entry=entry)
            test_string(order=the_list)

    .. tab:: 英文

        One of pytest's greatest strengths is its extremely flexible fixture system. It
        allows us to boil down complex requirements for tests into more simple and
        organized functions, where we only need to have each one describe the things
        they are dependent on. We'll get more into this further down, but for now,
        here's a quick example to demonstrate how fixtures can use other fixtures:

        .. code-block:: python

            # contents of test_append.py
            import pytest


            # Arrange
            @pytest.fixture
            def first_entry():
                return "a"


            # Arrange
            @pytest.fixture
            def order(first_entry):
                return [first_entry]


            def test_string(order):
                # Act
                order.append("b")

                # Assert
                assert order == ["a", "b"]


        Notice that this is the same example from above, but very little changed. The
        fixtures in pytest **request** fixtures just like tests. All the same
        **requesting** rules apply to fixtures that do for tests. Here's how this
        example would work if we did it by hand:

        .. code-block:: python

            def first_entry():
                return "a"


            def order(first_entry):
                return [first_entry]


            def test_string(order):
                # Act
                order.append("b")

                # Assert
                assert order == ["a", "b"]


            entry = first_entry()
            the_list = order(first_entry=entry)
            test_string(order=the_list)

Fixtures 可重复使用
^^^^^^^^^^^^^^^^^^^^^

**Fixtures are reusable**

.. tabs::

    .. tab:: 中文

        pytest 的 fixture 系统之所以强大，部分原因在于它允许我们定义一个通用的设置步骤，可以反复使用，就像普通函数一样。两个不同的测试可以请求相同的 fixture，而 pytest 会为每个测试提供该 fixture 的独立结果。

        这对于确保测试之间互不影响极为有用。我们可以利用这一系统确保每个测试都获得自己的一份新数据，并从干净的状态开始，以便提供一致、可重复的结果。

        以下是如何使用这一特性的一例：

        .. code-block:: python

            # contents of test_append.py
            import pytest


            # Arrange
            @pytest.fixture
            def first_entry():
                return "a"


            # Arrange
            @pytest.fixture
            def order(first_entry):
                return [first_entry]


            def test_string(order):
                # Act
                order.append("b")

                # Assert
                assert order == ["a", "b"]


            def test_int(order):
                # Act
                order.append(2)

                # Assert
                assert order == ["a", 2]

        在这里，每个测试都获得自己的一份 `list` 对象，这意味着 `order` fixture 被执行了两次（ `first_entry` fixture 也是如此）。如果我们手动执行，代码看起来会像这样：

        .. code-block:: python

            def first_entry():
                return "a"


            def order(first_entry):
                return [first_entry]


            def test_string(order):
                # Act
                order.append("b")

                # Assert
                assert order == ["a", "b"]


            def test_int(order):
                # Act
                order.append(2)

                # Assert
                assert order == ["a", 2]


            entry = first_entry()
            the_list = order(first_entry=entry)
            test_string(order=the_list)

            entry = first_entry()
            the_list = order(first_entry=entry)
            test_int(order=the_list)

    .. tab:: 英文

        One of the things that makes pytest's fixture system so powerful, is that it
        gives us the ability to define a generic setup step that can be reused over and
        over, just like a normal function would be used. Two different tests can request
        the same fixture and have pytest give each test their own result from that
        fixture.

        This is extremely useful for making sure tests aren't affected by each other. We
        can use this system to make sure each test gets its own fresh batch of data and
        is starting from a clean state so it can provide consistent, repeatable results.

        Here's an example of how this can come in handy:

        .. code-block:: python

            # contents of test_append.py
            import pytest


            # Arrange
            @pytest.fixture
            def first_entry():
                return "a"


            # Arrange
            @pytest.fixture
            def order(first_entry):
                return [first_entry]


            def test_string(order):
                # Act
                order.append("b")

                # Assert
                assert order == ["a", "b"]


            def test_int(order):
                # Act
                order.append(2)

                # Assert
                assert order == ["a", 2]


        Each test here is being given its own copy of that ``list`` object,
        which means the ``order`` fixture is getting executed twice (the same
        is true for the ``first_entry`` fixture). If we were to do this by hand as
        well, it would look something like this:

        .. code-block:: python

            def first_entry():
                return "a"


            def order(first_entry):
                return [first_entry]


            def test_string(order):
                # Act
                order.append("b")

                # Assert
                assert order == ["a", "b"]


            def test_int(order):
                # Act
                order.append(2)

                # Assert
                assert order == ["a", 2]


            entry = first_entry()
            the_list = order(first_entry=entry)
            test_string(order=the_list)

            entry = first_entry()
            the_list = order(first_entry=entry)
            test_int(order=the_list)

一个 测试/fixture 可以一次**请求**多个fixture
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

A test/fixture can **request** more than one fixture at a time

.. tabs::

    .. tab:: 中文

        测试和 fixtures 并不局限于一次 **请求** 一个 fixture。它们可以请求任意数量的 fixtures。以下是另一个简短的示例来演示这一点:

        .. code-block:: python

            # contents of test_append.py
            import pytest


            # Arrange
            @pytest.fixture
            def first_entry():
                return "a"


            # Arrange
            @pytest.fixture
            def second_entry():
                return 2


            # Arrange
            @pytest.fixture
            def order(first_entry, second_entry):
                return [first_entry, second_entry]


            # Arrange
            @pytest.fixture
            def expected_list():
                return ["a", 2, 3.0]


            def test_string(order, expected_list):
                # Act
                order.append(3.0)

                # Assert
                assert order == expected_list

    .. tab:: 英文

        Tests and fixtures aren't limited to **requesting** a single fixture at a time.
        They can request as many as they like. Here's another quick example to
        demonstrate:

        .. code-block:: python

            # contents of test_append.py
            import pytest


            # Arrange
            @pytest.fixture
            def first_entry():
                return "a"


            # Arrange
            @pytest.fixture
            def second_entry():
                return 2


            # Arrange
            @pytest.fixture
            def order(first_entry, second_entry):
                return [first_entry, second_entry]


            # Arrange
            @pytest.fixture
            def expected_list():
                return ["a", 2, 3.0]


            def test_string(order, expected_list):
                # Act
                order.append(3.0)

                # Assert
                assert order == expected_list

fixtures 可以在每个测试中被 **请求** 多次（返回值会被缓存）
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Fixtures can be **requested** more than once per test (return values are cached)

.. tabs::

    .. tab:: 中文

        fixtures 也可以在同一个测试中被 **请求** 多次，pytest 不会再次执行它们。这意味着我们可以在多个依赖于它们的 fixtures 中（甚至在测试本身中） **请求** fixtures，而这些 fixtures 不会被执行多次。

        .. code-block:: python

            # contents of test_append.py
            import pytest


            # Arrange
            @pytest.fixture
            def first_entry():
                return "a"


            # Arrange
            @pytest.fixture
            def order():
                return []


            # Act
            @pytest.fixture
            def append_first(order, first_entry):
                return order.append(first_entry)


            def test_string_only(append_first, order, first_entry):
                # Assert
                assert order == [first_entry]

        如果一个 **请求** 的 fixture 每次在测试中被 **请求** 时都会执行一次，那么这个测试将会失败，因为 ``append_first`` 和 ``test_string_only`` 都会将 ``order`` 视为一个空列表（即 ``[]``），但是由于 ``order`` 的返回值在第一次调用后被缓存（以及执行它可能产生的任何副作用），所以测试和 ``append_first`` 引用了同一个对象，因此测试看到了 ``append_first`` 对该对象所做的影响。

    .. tab:: 英文

        Fixtures can also be **requested** more than once during the same test, and
        pytest won't execute them again for that test. This means we can **request**
        fixtures in multiple fixtures that are dependent on them (and even again in the
        test itself) without those fixtures being executed more than once.

        .. code-block:: python

            # contents of test_append.py
            import pytest


            # Arrange
            @pytest.fixture
            def first_entry():
                return "a"


            # Arrange
            @pytest.fixture
            def order():
                return []


            # Act
            @pytest.fixture
            def append_first(order, first_entry):
                return order.append(first_entry)


            def test_string_only(append_first, order, first_entry):
                # Assert
                assert order == [first_entry]

        If a **requested** fixture was executed once for every time it was **requested**
        during a test, then this test would fail because both ``append_first`` and
        ``test_string_only`` would see ``order`` as an empty list (i.e. ``[]``), but
        since the return value of ``order`` was cached (along with any side effects
        executing it may have had) after the first time it was called, both the test and
        ``append_first`` were referencing the same object, and the test saw the effect
        ``append_first`` had on that object.

.. _`autouse`:
.. _`autouse fixtures`:

自动使用的 fixtures（不需要显式请求的 fixtures）
-----------------------------------------------------

**Autouse fixtures (fixtures you don't have to request)**

.. tabs::

    .. tab:: 中文

        有时您可能希望有一个（或多个）您知道所有测试都将依赖的 fixture。“自动使用” fixtures 是一种方便的方法，可以使所有测试自动 **请求** 它们。这可以减少许多冗余的 **请求**，甚至可以提供更高级的 fixture 使用方式（稍后会详细介绍）。

        我们可以通过在 fixture 装饰器中传递 ``autouse=True`` 来将 fixture 设为自动使用的 fixture。以下是它们如何使用的简单示例：

        .. code-block:: python

            # contents of test_append.py
            import pytest


            @pytest.fixture
            def first_entry():
                return "a"


            @pytest.fixture
            def order(first_entry):
                return []


            @pytest.fixture(autouse=True)
            def append_first(order, first_entry):
                return order.append(first_entry)


            def test_string_only(order, first_entry):
                assert order == [first_entry]


            def test_string_and_int(order, first_entry):
                order.append(2)
                assert order == [first_entry, 2]

        在这个例子中，``append_first`` fixture 是一个自动使用的 fixture。因为它是自动执行的，所以两个测试都受到影响，即使它们都没有显式 **请求** 它。这并不意味着它们 *不能* 被 **请求**；只是说这不是 *必要的*。

    .. tab:: 英文

        Sometimes you may want to have a fixture (or even several) that you know all
        your tests will depend on. "Autouse" fixtures are a convenient way to make all
        tests automatically **request** them. This can cut out a
        lot of redundant **requests**, and can even provide more advanced fixture usage
        (more on that further down).

        We can make a fixture an autouse fixture by passing in ``autouse=True`` to the
        fixture's decorator. Here's a simple example for how they can be used:

        .. code-block:: python

            # contents of test_append.py
            import pytest


            @pytest.fixture
            def first_entry():
                return "a"

        
            @pytest.fixture
            def order(first_entry):
                return []


            @pytest.fixture(autouse=True)
            def append_first(order, first_entry):
                return order.append(first_entry)


            def test_string_only(order, first_entry):
                assert order == [first_entry]


            def test_string_and_int(order, first_entry):
                order.append(2)
                assert order == [first_entry, 2]

        In this example, the ``append_first`` fixture is an autouse fixture. Because it
        happens automatically, both tests are affected by it, even though neither test
        **requested** it. That doesn't mean they *can't* be **requested** though; just
        that it isn't *necessary*.

.. _smtpshared:

Scope: 跨类、模块、包或会话共享fixture
--------------------------------------------------------------------

**Scope: sharing fixtures across classes, modules, packages or session**

.. tabs::

    .. tab:: 中文

        依赖网络访问的 fixtures 通常会受到连接性的影响，并且创建时通常会耗费较长时间。扩展前面的例子，我们可以在 :py:func:`@pytest.fixture <pytest.fixture>` 调用中添加 ``scope="module"`` 参数，以使 ``smtp_connection`` fixture 函数（负责创建到预先存在的 SMTP 服务器的连接）仅在每个测试 *模块* 中调用一次（默认情况下是每个测试 *函数* 调用一次）。这样，测试模块中的多个测试函数将各自接收相同的 ``smtp_connection`` fixture 实例，从而节省时间。 ``scope`` 的可选值包括： ``function``、``class``、``module``、``package`` 或 ``session``。

        下一个例子将 fixture 函数放入一个单独的 ``conftest.py`` 文件中，以便目录中多个测试模块的测试可以访问该 fixture 函数：

        .. code-block:: python

            # content of conftest.py
            import smtplib

            import pytest


            @pytest.fixture(scope="module")
            def smtp_connection():
                return smtplib.SMTP("smtp.gmail.com", 587, timeout=5)


        .. code-block:: python

            # content of test_module.py


            def test_ehlo(smtp_connection):
                response, msg = smtp_connection.ehlo()
                assert response == 250
                assert b"smtp.gmail.com" in msg
                assert 0  # for demo purposes


            def test_noop(smtp_connection):
                response, msg = smtp_connection.noop()
                assert response == 250
                assert 0  # for demo purposes

        在这里，``test_ehlo`` 需要 ``smtp_connection`` fixture 的值。pytest 将发现并调用标记为 :py:func:`@pytest.fixture <pytest.fixture>` 的 ``smtp_connection`` fixture 函数。运行测试的输出如下所示：

        .. code-block:: pytest

            $ pytest test_module.py
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 2 items

            test_module.py FF                                                    [100%]

            ================================= FAILURES =================================
            ________________________________ test_ehlo _________________________________

            smtp_connection = <smtplib.SMTP object at 0xdeadbeef0001>

                def test_ehlo(smtp_connection):
                    response, msg = smtp_connection.ehlo()
                    assert response == 250
                    assert b"smtp.gmail.com" in msg
            >       assert 0  # for demo purposes
            E       assert 0

            test_module.py:7: AssertionError
            ________________________________ test_noop _________________________________

            smtp_connection = <smtplib.SMTP object at 0xdeadbeef0001>

                def test_noop(smtp_connection):
                    response, msg = smtp_connection.noop()
                    assert response == 250
            >       assert 0  # for demo purposes
            E       assert 0

            test_module.py:13: AssertionError
            ========================= short test summary info ==========================
            FAILED test_module.py::test_ehlo - assert 0
            FAILED test_module.py::test_noop - assert 0
            ============================ 2 failed in 0.12s =============================

        您可以看到两个 ``assert 0`` 失败，更重要的是，您还可以看到 **完全相同** 的 ``smtp_connection`` 对象被传递到两个测试函数中，因为 pytest 在 traceback 中显示了传入参数的值。因此，使用 ``smtp_connection`` 的两个测试函数运行的速度与单个测试相当，因为它们重用了相同的实例。

        如果您决定希望具有会话范围的 ``smtp_connection`` 实例，只需声明它即可：

        .. code-block:: python

            @pytest.fixture(scope="session")
            def smtp_connection():
                # 返回的 fixture 值将被共享给
                # 所有请求它的测试
                ...

    .. tab:: 英文

        .. regendoc:wipe

        Fixtures requiring network access depend on connectivity and are
        usually time-expensive to create.  Extending the previous example, we
        can add a ``scope="module"`` parameter to the
        :py:func:`@pytest.fixture <pytest.fixture>` invocation
        to cause a ``smtp_connection`` fixture function, responsible to create a connection to a preexisting SMTP server, to only be invoked
        once per test *module* (the default is to invoke once per test *function*).
        Multiple test functions in a test module will thus
        each receive the same ``smtp_connection`` fixture instance, thus saving time.
        Possible values for ``scope`` are: ``function``, ``class``, ``module``, ``package`` or ``session``.

        The next example puts the fixture function into a separate ``conftest.py`` file
        so that tests from multiple test modules in the directory can
        access the fixture function:

        .. code-block:: python

            # content of conftest.py
            import smtplib

            import pytest


            @pytest.fixture(scope="module")
            def smtp_connection():
                return smtplib.SMTP("smtp.gmail.com", 587, timeout=5)


        .. code-block:: python

            # content of test_module.py


            def test_ehlo(smtp_connection):
                response, msg = smtp_connection.ehlo()
                assert response == 250
                assert b"smtp.gmail.com" in msg
                assert 0  # for demo purposes


            def test_noop(smtp_connection):
                response, msg = smtp_connection.noop()
                assert response == 250
                assert 0  # for demo purposes

        Here, the ``test_ehlo`` needs the ``smtp_connection`` fixture value.  pytest
        will discover and call the :py:func:`@pytest.fixture <pytest.fixture>`
        marked ``smtp_connection`` fixture function.  Running the test looks like this:

        .. code-block:: pytest

            $ pytest test_module.py
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 2 items

            test_module.py FF                                                    [100%]

            ================================= FAILURES =================================
            ________________________________ test_ehlo _________________________________

            smtp_connection = <smtplib.SMTP object at 0xdeadbeef0001>

                def test_ehlo(smtp_connection):
                    response, msg = smtp_connection.ehlo()
                    assert response == 250
                    assert b"smtp.gmail.com" in msg
            >       assert 0  # for demo purposes
            E       assert 0

            test_module.py:7: AssertionError
            ________________________________ test_noop _________________________________

            smtp_connection = <smtplib.SMTP object at 0xdeadbeef0001>

                def test_noop(smtp_connection):
                    response, msg = smtp_connection.noop()
                    assert response == 250
            >       assert 0  # for demo purposes
            E       assert 0

            test_module.py:13: AssertionError
            ========================= short test summary info ==========================
            FAILED test_module.py::test_ehlo - assert 0
            FAILED test_module.py::test_noop - assert 0
            ============================ 2 failed in 0.12s =============================

        You see the two ``assert 0`` failing and more importantly you can also see
        that the **exactly same** ``smtp_connection`` object was passed into the
        two test functions because pytest shows the incoming argument values in the
        traceback.  As a result, the two test functions using ``smtp_connection`` run
        as quick as a single one because they reuse the same instance.

        If you decide that you rather want to have a session-scoped ``smtp_connection``
        instance, you can simply declare it:

        .. code-block:: python

            @pytest.fixture(scope="session")
            def smtp_connection():
                # the returned fixture value will be shared for
                # all tests requesting it
                ...


Fixture 的 scope
^^^^^^^^^^^^^^^^^^

**Fixture scopes**

.. tabs::

    .. tab:: 中文

        Fixtures 在首次被测试请求时创建，并根据其 ``scope`` 进行销毁：

        * ``function``: 默认范围，fixture 在测试结束时被销毁。
        * ``class``: fixture 在类中最后一个测试的拆卸期间被销毁。
        * ``module``: fixture 在模块中最后一个测试的拆卸期间被销毁。
        * ``package``: fixture 在定义该 fixture 的包中最后一个测试的拆卸期间被销毁，包括其子包和子目录。
        * ``session``: fixture 在测试会话结束时被销毁。

        .. note::

            Pytest 仅缓存一个 fixture 的实例，这意味着在使用参数化 fixture 时，pytest 可能会在给定范围内多次调用该 fixture。

    .. tab:: 英文

        Fixtures are created when first requested by a test, and are destroyed based on their ``scope``:

        * ``function``: the default scope, the fixture is destroyed at the end of the test.
        * ``class``: the fixture is destroyed during teardown of the last test in the class.
        * ``module``: the fixture is destroyed during teardown of the last test in the module.
        * ``package``: the fixture is destroyed during teardown of the last test in the package where the fixture is defined, including sub-packages and sub-directories within it.
        * ``session``: the fixture is destroyed at the end of the test session.

        .. note::

            Pytest only caches one instance of a fixture at a time, which
            means that when using a parametrized fixture, pytest may invoke a fixture more than once in
            the given scope.

.. _dynamic scope:

动态 scope
^^^^^^^^^^^^^

**Dynamic scope**

.. tabs::

    .. tab:: 中文

        .. versionadded:: 5.2

        在某些情况下，您可能希望在不更改代码的情况下更改 fixture 的作用域。为此，可以将一个可调用对象传递给 ``scope``。该可调用对象必须返回一个有效范围的字符串，并且只会在 fixture 定义期间执行一次。它将接收两个关键字参数——``fixture_name`` （作为字符串）和 ``config`` （包含配置对象）。

        这在处理需要时间进行设置的 fixtures 时特别有用，例如启动 Docker 容器。您可以使用命令行参数来控制不同环境下生成的容器的作用域。请参见以下示例。

        .. code-block:: python

            def determine_scope(fixture_name, config):
                if config.getoption("--keep-containers", None):
                    return "session"
                return "function"


            @pytest.fixture(scope=determine_scope)
            def docker_container():
                yield spawn_container()

    .. tab:: 英文

        .. versionadded:: 5.2

        In some cases, you might want to change the scope of the fixture without changing the code.
        To do that, pass a callable to ``scope``. The callable must return a string with a valid scope
        and will be executed only once - during the fixture definition. It will be called with two
        keyword arguments - ``fixture_name`` as a string and ``config`` with a configuration object.

        This can be especially useful when dealing with fixtures that need time for setup, like spawning
        a docker container. You can use the command-line argument to control the scope of the spawned
        containers for different environments. See the example below.

        .. code-block:: python

            def determine_scope(fixture_name, config):
                if config.getoption("--keep-containers", None):
                    return "session"
                return "function"


            @pytest.fixture(scope=determine_scope)
            def docker_container():
                yield spawn_container()



.. _`finalization`:

清理(Cleanup)/收尾(Teardown)（即 Fixture 最终化）
-------------------------------------------

**Teardown/Cleanup (AKA Fixture finalization)**

.. tabs::

    .. tab:: 中文

        当我们运行测试时，我们希望确保它们能够自我清理，以免影响其他测试（同时也避免留下大量测试数据来膨胀系统）。pytest 中的 fixtures 提供了一个非常有用的清理系统，使我们能够定义每个 fixture 自我清理所需的具体步骤。

        该系统可以通过两种方式进行利用。

    .. tab:: 英文

        When we run our tests, we'll want to make sure they clean up after themselves so
        they don't mess with any other tests (and also so that we don't leave behind a
        mountain of test data to bloat the system). Fixtures in pytest offer a very
        useful teardown system, which allows us to define the specific steps necessary
        for each fixture to clean up after itself.

        This system can be leveraged in two ways.

.. _`yield fixtures`:

1. ``yield`` fixtures (推荐方式)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**1.** ``yield`` **fixtures (recommended)**

.. tabs::

    .. tab:: 中文

        .. regendoc: wipe

        “Yield” fixtures 使用 `yield` 而不是 `return`。通过这些 fixtures，我们可以运行一些代码并将对象传递回请求的 fixture/test，就像其他 fixtures 一样。唯一的区别是：

        1. `return` 被替换为 `yield`。
        2. 该 fixture 的任何清理代码都放在 `yield` 之后。

        一旦 pytest 确定了 fixtures 的线性顺序，它会运行每个 fixture，直到返回或 yield，然后继续处理列表中的下一个 fixture，执行相同的操作。

        测试完成后，pytest 会按 *相反的顺序* 回到 fixtures 列表，取出每个 yield 的 fixture，运行 `yield` 语句 *之后* 的代码。

        作为一个简单的例子，考虑这个基本的电子邮件模块：

        .. code-block:: python

            # content of emaillib.py
            class MailAdminClient:
                def create_user(self):
                    return MailUser()

                def delete_user(self, user):
                    # 执行一些清理
                    pass


            class MailUser:
                def __init__(self):
                    self.inbox = []

                def send_email(self, email, other):
                    other.inbox.append(email)

                def clear_mailbox(self):
                    self.inbox.clear()


            class Email:
                def __init__(self, subject, body):
                    self.subject = subject
                    self.body = body

        假设我们想测试从一个用户发送电子邮件到另一个用户。我们必须首先创建每个用户，然后从一个用户发送电子邮件到另一个用户，最后断言另一个用户的收件箱收到了该消息。如果我们希望在测试运行后进行清理，我们可能需要确保在删除该用户之前清空另一个用户的邮箱，否则系统可能会报错。

        这看起来像这样：

        .. code-block:: python

            # content of test_emaillib.py
            from emaillib import Email, MailAdminClient

            import pytest


            @pytest.fixture
            def mail_admin():
                return MailAdminClient()


            @pytest.fixture
            def sending_user(mail_admin):
                user = mail_admin.create_user()
                yield user
                mail_admin.delete_user(user)


            @pytest.fixture
            def receiving_user(mail_admin):
                user = mail_admin.create_user()
                yield user
                user.clear_mailbox()
                mail_admin.delete_user(user)


            def test_email_received(sending_user, receiving_user):
                email = Email(subject="Hey!", body="How's it going?")
                sending_user.send_email(email, receiving_user)
                assert email in receiving_user.inbox

        因为 `receiving_user` 是设置期间运行的最后一个 fixture，所以它是在清理期间第一个运行的。

        即使在清理方面的顺序正确，也不能保证安全清理。这在 :ref:`safe teardowns` 中有更详细的说明。

        .. code-block:: pytest

            $ pytest -q test_emaillib.py
            .                                                                    [100%]
            1 passed in 0.12s

    .. tab:: 英文

        .. regendoc: wipe

        "Yield" fixtures ``yield`` instead of ``return``. With these
        fixtures, we can run some code and pass an object back to the requesting
        fixture/test, just like with the other fixtures. The only differences are:

        1. ``return`` is swapped out for ``yield``.
        2. Any teardown code for that fixture is placed *after* the ``yield``.

        Once pytest figures out a linear order for the fixtures, it will run each one up
        until it returns or yields, and then move on to the next fixture in the list to
        do the same thing.

        Once the test is finished, pytest will go back down the list of fixtures, but in
        the *reverse order*, taking each one that yielded, and running the code inside
        it that was *after* the ``yield`` statement.

        As a simple example, consider this basic email module:

        .. code-block:: python

            # content of emaillib.py
            class MailAdminClient:
                def create_user(self):
                    return MailUser()

                def delete_user(self, user):
                    # do some cleanup
                    pass


            class MailUser:
                def __init__(self):
                    self.inbox = []

                def send_email(self, email, other):
                    other.inbox.append(email)

                def clear_mailbox(self):
                    self.inbox.clear()


            class Email:
                def __init__(self, subject, body):
                    self.subject = subject
                    self.body = body

        Let's say we want to test sending email from one user to another. We'll have to
        first make each user, then send the email from one user to the other, and
        finally assert that the other user received that message in their inbox. If we
        want to clean up after the test runs, we'll likely have to make sure the other
        user's mailbox is emptied before deleting that user, otherwise the system may
        complain.

        Here's what that might look like:

        .. code-block:: python

            # content of test_emaillib.py
            from emaillib import Email, MailAdminClient

            import pytest


            @pytest.fixture
            def mail_admin():
                return MailAdminClient()


            @pytest.fixture
            def sending_user(mail_admin):
                user = mail_admin.create_user()
                yield user
                mail_admin.delete_user(user)


            @pytest.fixture
            def receiving_user(mail_admin):
                user = mail_admin.create_user()
                yield user
                user.clear_mailbox()
                mail_admin.delete_user(user)


            def test_email_received(sending_user, receiving_user):
                email = Email(subject="Hey!", body="How's it going?")
                sending_user.send_email(email, receiving_user)
                assert email in receiving_user.inbox

        Because ``receiving_user`` is the last fixture to run during setup, it's the first to run
        during teardown.

        There is a risk that even having the order right on the teardown side of things
        doesn't guarantee a safe cleanup. That's covered in a bit more detail in
        :ref:`safe teardowns`.

        .. code-block:: pytest

            $ pytest -q test_emaillib.py
            .                                                                    [100%]
            1 passed in 0.12s

处理 yield fixture 的错误
"""""""""""""""""""""""""""""""""

**Handling errors for yield fixture**

.. tabs::

    .. tab:: 中文

        如果一个 yield fixture 在 yield 之前抛出异常，pytest 不会尝试在该 yield fixture 的 `yield` 语句之后运行清理代码。不过，对于已经成功运行的每个 fixture，pytest 仍会按正常方式尝试进行清理。

    .. tab:: 英文

        If a yield fixture raises an exception before yielding, pytest won't try to run
        the teardown code after that yield fixture's ``yield`` statement. But, for every
        fixture that has already run successfully for that test, pytest will still
        attempt to tear them down as it normally would.

2. 直接添加终结器
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**2. Adding finalizers directly**

.. tabs::

    .. tab:: 中文

        虽然 yield fixtures 被认为是更简洁和直接的选择，但还有另一种选择，即直接将“终结器”函数添加到测试的 `request-context`_ 对象中。它带来的效果与 yield fixtures 类似，但需要更多的冗长代码。

        为了使用这种方法，我们必须在需要添加清理代码的 fixture 中请求 `request-context`_ 对象（就像请求其他 fixture 一样），然后将包含清理代码的可调用对象传递给其 ``addfinalizer`` 方法。

        我们需要小心，因为 pytest 一旦添加了终结器，就会运行它，即使该 fixture 在添加终结器后抛出了异常。因此，为了确保在不需要时不运行终结器代码，我们应该仅在 fixture 做了需要清理的操作后添加终结器。

        以下是使用 ``addfinalizer`` 方法的前一个示例：

        .. code-block:: python

            # content of test_emaillib.py
            from emaillib import Email, MailAdminClient

            import pytest


            @pytest.fixture
            def mail_admin():
                return MailAdminClient()


            @pytest.fixture
            def sending_user(mail_admin):
                user = mail_admin.create_user()
                yield user
                mail_admin.delete_user(user)


            @pytest.fixture
            def receiving_user(mail_admin, request):
                user = mail_admin.create_user()

                def delete_user():
                    mail_admin.delete_user(user)

                request.addfinalizer(delete_user)
                return user


            @pytest.fixture
            def email(sending_user, receiving_user, request):
                _email = Email(subject="Hey!", body="How's it going?")
                sending_user.send_email(_email, receiving_user)

                def empty_mailbox():
                    receiving_user.clear_mailbox()

                request.addfinalizer(empty_mailbox)
                return _email


            def test_email_received(receiving_user, email):
                assert email in receiving_user.inbox

        这个示例比 yield fixtures 长一些，复杂一些，但在紧急情况下提供了一些细微的灵活性。

        .. code-block:: pytest

            $ pytest -q test_emaillib.py
            .                                                                    [100%]
            1 passed in 0.12s


    .. tab:: 英文

        While yield fixtures are considered to be the cleaner and more straightforward
        option, there is another choice, and that is to add "finalizer" functions
        directly to the test's `request-context`_ object. It brings a similar result as
        yield fixtures, but requires a bit more verbosity.

        In order to use this approach, we have to request the `request-context`_ object
        (just like we would request another fixture) in the fixture we need to add
        teardown code for, and then pass a callable, containing that teardown code, to
        its ``addfinalizer`` method.

        We have to be careful though, because pytest will run that finalizer once it's
        been added, even if that fixture raises an exception after adding the finalizer.
        So to make sure we don't run the finalizer code when we wouldn't need to, we
        would only add the finalizer once the fixture would have done something that
        we'd need to teardown.

        Here's how the previous example would look using the ``addfinalizer`` method:

        .. code-block:: python

            # content of test_emaillib.py
            from emaillib import Email, MailAdminClient

            import pytest


            @pytest.fixture
            def mail_admin():
                return MailAdminClient()


            @pytest.fixture
            def sending_user(mail_admin):
                user = mail_admin.create_user()
                yield user
                mail_admin.delete_user(user)


            @pytest.fixture
            def receiving_user(mail_admin, request):
                user = mail_admin.create_user()

                def delete_user():
                    mail_admin.delete_user(user)

                request.addfinalizer(delete_user)
                return user


            @pytest.fixture
            def email(sending_user, receiving_user, request):
                _email = Email(subject="Hey!", body="How's it going?")
                sending_user.send_email(_email, receiving_user)

                def empty_mailbox():
                    receiving_user.clear_mailbox()

                request.addfinalizer(empty_mailbox)
                return _email


            def test_email_received(receiving_user, email):
                assert email in receiving_user.inbox


        It's a bit longer than yield fixtures and a bit more complex, but it
        does offer some nuances for when you're in a pinch.

        .. code-block:: pytest

            $ pytest -q test_emaillib.py
            .                                                                    [100%]
            1 passed in 0.12s

注意终结器的顺序
""""""""""""""""""""""""

**Note on finalizer order**

.. tabs::

    .. tab:: 中文

        终结器以先进后出的顺序执行。对于 yield fixtures，首先运行的清理代码来自最右侧的 fixture，即最后一个测试参数。

        .. code-block:: python

            # content of test_finalizers.py
            import pytest


            def test_bar(fix_w_yield1, fix_w_yield2):
                print("test_bar")


            @pytest.fixture
            def fix_w_yield1():
                yield
                print("after_yield_1")


            @pytest.fixture
            def fix_w_yield2():
                yield
                print("after_yield_2")


        .. code-block:: pytest

            $ pytest -s test_finalizers.py
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 1 item

            test_finalizers.py test_bar
            .after_yield_2
            after_yield_1


            ============================ 1 passed in 0.12s =============================

        对于终结器，第一个运行的 fixture 是对 `request.addfinalizer` 的最后一次调用。

        .. code-block:: python

            # content of test_finalizers.py
            from functools import partial
            import pytest


            @pytest.fixture
            def fix_w_finalizers(request):
                request.addfinalizer(partial(print, "finalizer_2"))
                request.addfinalizer(partial(print, "finalizer_1"))


            def test_bar(fix_w_finalizers):
                print("test_bar")


        .. code-block:: pytest

            $ pytest -s test_finalizers.py
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 1 item

            test_finalizers.py test_bar
            .finalizer_1
            finalizer_2


            ============================ 1 passed in 0.12s =============================

        这是因为 yield fixtures 在幕后使用 `addfinalizer`: 当 fixture 执行时，`addfinalizer` 注册一个恢复生成器的函数，而这个函数会调用清理代码。

    .. tab:: 英文

        Finalizers are executed in a first-in-last-out order.
        For yield fixtures, the first teardown code to run is from the right-most fixture, i.e. the last test parameter.


        .. code-block:: python

            # content of test_finalizers.py
            import pytest


            def test_bar(fix_w_yield1, fix_w_yield2):
                print("test_bar")


            @pytest.fixture
            def fix_w_yield1():
                yield
                print("after_yield_1")


            @pytest.fixture
            def fix_w_yield2():
                yield
                print("after_yield_2")


        .. code-block:: pytest

            $ pytest -s test_finalizers.py
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 1 item

            test_finalizers.py test_bar
            .after_yield_2
            after_yield_1


            ============================ 1 passed in 0.12s =============================

        For finalizers, the first fixture to run is last call to `request.addfinalizer`.

        .. code-block:: python

            # content of test_finalizers.py
            from functools import partial
            import pytest


            @pytest.fixture
            def fix_w_finalizers(request):
                request.addfinalizer(partial(print, "finalizer_2"))
                request.addfinalizer(partial(print, "finalizer_1"))


            def test_bar(fix_w_finalizers):
                print("test_bar")


        .. code-block:: pytest

            $ pytest -s test_finalizers.py
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 1 item

            test_finalizers.py test_bar
            .finalizer_1
            finalizer_2


            ============================ 1 passed in 0.12s =============================

        This is so because yield fixtures use `addfinalizer` behind the scenes: when the fixture executes, `addfinalizer` registers a function that resumes the generator, which in turn calls the teardown code.


.. _`safe teardowns`:

安全的收尾
--------------

**Safe teardowns**

.. tabs::

    .. tab:: 中文

        pytest 的 fixture 系统是 *非常* 强大的，但它仍然是由计算机运行的，因此无法安全地拆解我们扔给它的所有内容。如果我们不小心，错误可能会在错误的位置留下测试数据，这可能会迅速引发进一步的问题。

        例如，考虑以下测试（基于上面的邮件示例）：

        .. code-block:: python

            # content of test_emaillib.py
            from emaillib import Email, MailAdminClient

            import pytest


            @pytest.fixture
            def setup():
                mail_admin = MailAdminClient()
                sending_user = mail_admin.create_user()
                receiving_user = mail_admin.create_user()
                email = Email(subject="Hey!", body="How's it going?")
                sending_user.send_email(email, receiving_user)
                yield receiving_user, email
                receiving_user.clear_mailbox()
                mail_admin.delete_user(sending_user)
                mail_admin.delete_user(receiving_user)


            def test_email_received(setup):
                receiving_user, email = setup
                assert email in receiving_user.inbox

        这个版本更加紧凑，但也更难以阅读，fixture 名称不够描述性，并且这些 fixtures 不易于重用。

        还有一个更严重的问题是，如果 setup 中的任何步骤引发异常，清理代码将不会运行。

        一种选择可能是使用 `addfinalizer` 方法，而不是 yield fixtures，但这可能会变得非常复杂且难以维护（而且不再紧凑）。

        .. code-block:: pytest

            $ pytest -q test_emaillib.py
            .                                                                    [100%]
            1 passed in 0.12s

    .. tab:: 英文

        The fixture system of pytest is *very* powerful, but it's still being run by a
        computer, so it isn't able to figure out how to safely teardown everything we
        throw at it. If we aren't careful, an error in the wrong spot might leave stuff
        from our tests behind, and that can cause further issues pretty quickly.

        For example, consider the following tests (based off of the mail example from
        above):

        .. code-block:: python

            # content of test_emaillib.py
            from emaillib import Email, MailAdminClient

            import pytest


            @pytest.fixture
            def setup():
                mail_admin = MailAdminClient()
                sending_user = mail_admin.create_user()
                receiving_user = mail_admin.create_user()
                email = Email(subject="Hey!", body="How's it going?")
                sending_user.send_email(email, receiving_user)
                yield receiving_user, email
                receiving_user.clear_mailbox()
                mail_admin.delete_user(sending_user)
                mail_admin.delete_user(receiving_user)


            def test_email_received(setup):
                receiving_user, email = setup
                assert email in receiving_user.inbox

        This version is a lot more compact, but it's also harder to read, doesn't have a
        very descriptive fixture name, and none of the fixtures can be reused easily.

        There's also a more serious issue, which is that if any of those steps in the
        setup raise an exception, none of the teardown code will run.

        One option might be to go with the ``addfinalizer`` method instead of yield
        fixtures, but that might get pretty complex and difficult to maintain (and it
        wouldn't be compact anymore).

        .. code-block:: pytest

            $ pytest -q test_emaillib.py
            .                                                                    [100%]
            1 passed in 0.12s

.. _`safe fixture structure`:

安全的 fixture 结构
^^^^^^^^^^^^^^^^^^^^^^

**Safe fixture structure**

.. tabs::

    .. tab:: 中文

        pytest 的安全且简单的 fixture 结构要求每个 fixture 仅执行一个状态更改操作，并将其与清理代码打包在一起，正如 :ref:`上面的电子邮件示例 <yield fixtures>` 所示。

        状态更改操作失败但仍然修改状态的机会是微不足道的，因为大多数操作通常是基于 `transaction <https://en.wikipedia.org/wiki/Transaction_processing>`_ 的（至少在可能留下状态的测试级别）。因此，如果我们确保任何成功的状态更改操作都通过将其移动到单独的 fixture 函数并将其与其他可能失败的状态更改操作分开来进行清理，那么我们的测试将有最佳的机会让测试环境保持原样。

        例如，假设我们有一个带登录页面的网站，并且可以访问一个管理 API 来生成用户。对于我们的测试，我们想要：

        1. 通过该管理 API 创建一个用户
        2. 使用 Selenium 启动浏览器
        3. 转到我们网站的登录页面
        4. 作为我们创建的用户登录
        5. 断言他们的名字在登录页面的标题中

        我们不希望将该用户留在系统中，也不希望留下浏览器会话在运行，因此我们需要确保创建这些内容的 fixtures 能够进行清理。

        以下是可能的实现方式：

        .. note::

            在这个例子中，某些 fixtures（例如 ``base_url`` 和
            ``admin_credentials``）假定在其他地方存在。因此现在，我们假设它们存在，暂时不对此进行关注。

        .. code-block:: python

            from uuid import uuid4
            from urllib.parse import urljoin

            from selenium.webdriver import Chrome
            import pytest

            from src.utils.pages import LoginPage, LandingPage
            from src.utils import AdminApiClient
            from src.utils.data_types import User


            @pytest.fixture
            def admin_client(base_url, admin_credentials):
                return AdminApiClient(base_url, **admin_credentials)


            @pytest.fixture
            def user(admin_client):
                _user = User(name="Susan", username=f"testuser-{uuid4()}", password="P4$$word")
                admin_client.create_user(_user)
                yield _user
                admin_client.delete_user(_user)


            @pytest.fixture
            def driver():
                _driver = Chrome()
                yield _driver
                _driver.quit()


            @pytest.fixture
            def login(driver, base_url, user):
                driver.get(urljoin(base_url, "/login"))
                page = LoginPage(driver)
                page.login(user)


            @pytest.fixture
            def landing_page(driver, login):
                return LandingPage(driver)


            def test_name_on_landing_page_after_login(landing_page, user):
                assert landing_page.header == f"Welcome, {user.name}!"

        依赖关系的布局方式使得不清楚 `user` fixture 是否会在 `driver` fixture 之前执行。但这没关系，因为这些是原子操作，因此无论哪个先运行都无关紧要，因为测试的事件序列仍然是 `linearizable <https://en.wikipedia.org/wiki/Linearizability>`_。重要的是，无论哪个先执行，如果一个在抛出异常而另一个没有，两个都不会留下任何东西。如果 `driver` 在 `user` 之前执行，而 `user` 抛出异常，驱动程序仍然会退出，并且用户从未被创建。而如果 `driver` 抛出了异常，那么驱动程序将永远不会启动，用户也不会被创建。

        .. note::

            另外，虽然 `user` fixture 并不 *实际* 需要在 `driver` fixture 之前发生，但如果我们让 `driver` 请求 `user`，在创建用户抛出异常的情况下，这可能会节省一些时间，因为它不会尝试启动驱动程序，而这是一项相对昂贵的操作。

    .. tab:: 英文

        The safest and simplest fixture structure requires limiting fixtures to only
        making one state-changing action each, and then bundling them together with
        their teardown code, as :ref:`the email examples above <yield fixtures>` showed.

        The chance that a state-changing operation can fail but still modify state is
        negligible, as most of these operations tend to be `transaction
        <https://en.wikipedia.org/wiki/Transaction_processing>`_-based (at least at the
        level of testing where state could be left behind). So if we make sure that any
        successful state-changing action gets torn down by moving it to a separate
        fixture function and separating it from other, potentially failing
        state-changing actions, then our tests will stand the best chance at leaving
        the test environment the way they found it.

        For an example, let's say we have a website with a login page, and we have
        access to an admin API where we can generate users. For our test, we want to:

        1. Create a user through that admin API
        2. Launch a browser using Selenium
        3. Go to the login page of our site
        4. Log in as the user we created
        5. Assert that their name is in the header of the landing page

        We wouldn't want to leave that user in the system, nor would we want to leave
        that browser session running, so we'll want to make sure the fixtures that
        create those things clean up after themselves.

        Here's what that might look like:

        .. note::

            For this example, certain fixtures (i.e. ``base_url`` and
            ``admin_credentials``) are implied to exist elsewhere. So for now, let's
            assume they exist, and we're just not looking at them.

        .. code-block:: python

            from uuid import uuid4
            from urllib.parse import urljoin

            from selenium.webdriver import Chrome
            import pytest

            from src.utils.pages import LoginPage, LandingPage
            from src.utils import AdminApiClient
            from src.utils.data_types import User


            @pytest.fixture
            def admin_client(base_url, admin_credentials):
                return AdminApiClient(base_url, **admin_credentials)


            @pytest.fixture
            def user(admin_client):
                _user = User(name="Susan", username=f"testuser-{uuid4()}", password="P4$$word")
                admin_client.create_user(_user)
                yield _user
                admin_client.delete_user(_user)


            @pytest.fixture
            def driver():
                _driver = Chrome()
                yield _driver
                _driver.quit()


            @pytest.fixture
            def login(driver, base_url, user):
                driver.get(urljoin(base_url, "/login"))
                page = LoginPage(driver)
                page.login(user)


            @pytest.fixture
            def landing_page(driver, login):
                return LandingPage(driver)


            def test_name_on_landing_page_after_login(landing_page, user):
                assert landing_page.header == f"Welcome, {user.name}!"

        The way the dependencies are laid out means it's unclear if the ``user``
        fixture would execute before the ``driver`` fixture. But that's ok, because
        those are atomic operations, and so it doesn't matter which one runs first
        because the sequence of events for the test is still `linearizable
        <https://en.wikipedia.org/wiki/Linearizability>`_. But what *does* matter is
        that, no matter which one runs first, if the one raises an exception while the
        other would not have, neither will have left anything behind. If ``driver``
        executes before ``user``, and ``user`` raises an exception, the driver will
        still quit, and the user was never made. And if ``driver`` was the one to raise
        the exception, then the driver would never have been started and the user would
        never have been made.

        .. note::

            While the ``user`` fixture doesn't *actually* need to happen before the
            ``driver`` fixture, if we made ``driver`` request ``user``, it might save
            some time in the event that making the user raises an exception, since it
            won't bother trying to start the driver, which is a fairly expensive
            operation.


安全地运行多个 ``assert`` 语句
---------------------------------------------

**Running multiple** ``assert`` **statements safely**

.. tabs::

    .. tab:: 中文

        有时您可能希望在完成所有设置后运行多个断言，这很有意义，因为在更复杂的系统中，单个操作可能会引发多个行为。pytest 提供了一种方便的方法来处理这种情况，它结合了我们迄今为止讨论的许多内容。

        所需的操作是提升到更大的作用域，然后将 **act** 步骤定义为一个自动使用的 fixture，最后确保所有 fixtures 都针对更高的作用域。

        让我们从 :ref:`上面的例子 <safe fixture structure>` 中提取一个例子，并稍作修改。假设除了检查标题中的欢迎消息外，我们还想检查登出按钮和指向用户个人资料的链接。

        让我们看看如何构建这个结构，以便我们可以运行多个断言，而不必重复所有步骤。

        .. note::

            在这个例子中，某些 fixtures（例如 ``base_url`` 和
            ``admin_credentials``）假定在其他地方存在。因此现在，我们假设它们存在，暂时不对此进行关注。

        .. code-block:: python

            # contents of tests/end_to_end/test_login.py
            from uuid import uuid4
            from urllib.parse import urljoin

            from selenium.webdriver import Chrome
            import pytest

            from src.utils.pages import LoginPage, LandingPage
            from src.utils import AdminApiClient
            from src.utils.data_types import User


            @pytest.fixture(scope="class")
            def admin_client(base_url, admin_credentials):
                return AdminApiClient(base_url, **admin_credentials)


            @pytest.fixture(scope="class")
            def user(admin_client):
                _user = User(name="Susan", username=f"testuser-{uuid4()}", password="P4$$word")
                admin_client.create_user(_user)
                yield _user
                admin_client.delete_user(_user)


            @pytest.fixture(scope="class")
            def driver():
                _driver = Chrome()
                yield _driver
                _driver.quit()


            @pytest.fixture(scope="class")
            def landing_page(driver, login):
                return LandingPage(driver)


            class TestLandingPageSuccess:
                @pytest.fixture(scope="class", autouse=True)
                def login(self, driver, base_url, user):
                    driver.get(urljoin(base_url, "/login"))
                    page = LoginPage(driver)
                    page.login(user)

                def test_name_in_header(self, landing_page, user):
                    assert landing_page.header == f"Welcome, {user.name}!"

                def test_sign_out_button(self, landing_page):
                    assert landing_page.sign_out_button.is_displayed()

                def test_profile_link(self, landing_page, user):
                    profile_href = urljoin(base_url, f"/profile?id={user.profile_id}")
                    assert landing_page.profile_link.get_attribute("href") == profile_href

        请注意，方法的签名中只引用 ``self`` 作为一种形式。没有状态与实际的测试类相关联，就像在 ``unittest.TestCase`` 框架中那样。所有内容都由 pytest fixture 系统管理。

        每个方法只需请求实际需要的 fixtures，而不必担心顺序。这是因为 **act** fixture 是一个自动使用的 fixture，并且确保了所有其他 fixtures 在它之前执行。没有更多的状态变化需要发生，因此测试可以自由进行任意数量的非状态变化查询，而不必担心干扰其他测试。

        ``login`` fixture 也在类中定义，因为并非模块中的每个其他测试都期望成功登录，并且 **act** 可能需要针对另一个测试类以不同方式处理。例如，如果我们想编写另一个关于提交错误凭据的测试场景，我们可以通过在测试文件中添加类似以下内容来处理：

        .. note::

            假设此页面对象（即 ``LoginPage``）在识别到登录表单上的文本表明错误凭据后会引发一个自定义异常 ``BadCredentialsException``。

        .. code-block:: python

            class TestLandingPageBadCredentials:
                @pytest.fixture(scope="class")
                def faux_user(self, user):
                    _user = deepcopy(user)
                    _user.password = "badpass"
                    return _user

                def test_raises_bad_credentials_exception(self, login_page, faux_user):
                    with pytest.raises(BadCredentialsException):
                        login_page.login(faux_user)

    .. tab:: 英文

        Sometimes you may want to run multiple asserts after doing all that setup, which
        makes sense as, in more complex systems, a single action can kick off multiple
        behaviors. pytest has a convenient way of handling this and it combines a bunch
        of what we've gone over so far.

        All that's needed is stepping up to a larger scope, then having the **act**
        step defined as an autouse fixture, and finally, making sure all the fixtures
        are targeting that higher level scope.

        Let's pull :ref:`an example from above <safe fixture structure>`, and tweak it a
        bit. Let's say that in addition to checking for a welcome message in the header,
        we also want to check for a sign out button, and a link to the user's profile.

        Let's take a look at how we can structure that so we can run multiple asserts
        without having to repeat all those steps again.

        .. note::

            For this example, certain fixtures (i.e. ``base_url`` and
            ``admin_credentials``) are implied to exist elsewhere. So for now, let's
            assume they exist, and we're just not looking at them.

        .. code-block:: python

            # contents of tests/end_to_end/test_login.py
            from uuid import uuid4
            from urllib.parse import urljoin

            from selenium.webdriver import Chrome
            import pytest

            from src.utils.pages import LoginPage, LandingPage
            from src.utils import AdminApiClient
            from src.utils.data_types import User


            @pytest.fixture(scope="class")
            def admin_client(base_url, admin_credentials):
                return AdminApiClient(base_url, **admin_credentials)


            @pytest.fixture(scope="class")
            def user(admin_client):
                _user = User(name="Susan", username=f"testuser-{uuid4()}", password="P4$$word")
                admin_client.create_user(_user)
                yield _user
                admin_client.delete_user(_user)


            @pytest.fixture(scope="class")
            def driver():
                _driver = Chrome()
                yield _driver
                _driver.quit()


            @pytest.fixture(scope="class")
            def landing_page(driver, login):
                return LandingPage(driver)


            class TestLandingPageSuccess:
                @pytest.fixture(scope="class", autouse=True)
                def login(self, driver, base_url, user):
                    driver.get(urljoin(base_url, "/login"))
                    page = LoginPage(driver)
                    page.login(user)

                def test_name_in_header(self, landing_page, user):
                    assert landing_page.header == f"Welcome, {user.name}!"

                def test_sign_out_button(self, landing_page):
                    assert landing_page.sign_out_button.is_displayed()

                def test_profile_link(self, landing_page, user):
                    profile_href = urljoin(base_url, f"/profile?id={user.profile_id}")
                    assert landing_page.profile_link.get_attribute("href") == profile_href

        Notice that the methods are only referencing ``self`` in the signature as a
        formality. No state is tied to the actual test class as it might be in the
        ``unittest.TestCase`` framework. Everything is managed by the pytest fixture
        system.

        Each method only has to request the fixtures that it actually needs without
        worrying about order. This is because the **act** fixture is an autouse fixture,
        and it made sure all the other fixtures executed before it. There's no more
        changes of state that need to take place, so the tests are free to make as many
        non-state-changing queries as they want without risking stepping on the toes of
        the other tests.

        The ``login`` fixture is defined inside the class as well, because not every one
        of the other tests in the module will be expecting a successful login, and the **act** may need to
        be handled a little differently for another test class. For example, if we
        wanted to write another test scenario around submitting bad credentials, we
        could handle it by adding something like this to the test file:

        .. note::

            It's assumed that the page object for this (i.e. ``LoginPage``) raises a
            custom exception, ``BadCredentialsException``, when it recognizes text
            signifying that on the login form after attempting to log in.

        .. code-block:: python

            class TestLandingPageBadCredentials:
                @pytest.fixture(scope="class")
                def faux_user(self, user):
                    _user = deepcopy(user)
                    _user.password = "badpass"
                    return _user

                def test_raises_bad_credentials_exception(self, login_page, faux_user):
                    with pytest.raises(BadCredentialsException):
                        login_page.login(faux_user)


.. _`request-context`:

Fixtures 可以检查请求的测试上下文
-------------------------------------------------------------

**Fixtures can introspect the requesting test context**

.. tabs::

    .. tab:: 中文

        Fixture 函数可以接受 :py:class:`request <_pytest.fixtures.FixtureRequest>` 对象，以 introspect "请求" 测试函数、类或模块的上下文。进一步扩展之前的 ``smtp_connection`` fixture 示例，让我们从使用我们 fixture 的测试模块中读取一个可选的服务器 URL：

        .. code-block:: python

            # content of conftest.py
            import smtplib

            import pytest


            @pytest.fixture(scope="module")
            def smtp_connection(request):
                server = getattr(request.module, "smtpserver", "smtp.gmail.com")
                smtp_connection = smtplib.SMTP(server, 587, timeout=5)
                yield smtp_connection
                print(f"finalizing {smtp_connection} ({server})")
                smtp_connection.close()

        我们使用 ``request.module`` 属性可选地从测试模块中获取 ``smtpserver`` 属性。如果我们再次执行，基本上没有改变：

        .. code-block:: pytest

            $ pytest -s -q --tb=no test_module.py
            FFfinalizing <smtplib.SMTP object at 0xdeadbeef0002> (smtp.gmail.com)

            ========================= short test summary info ==========================
            FAILED test_module.py::test_ehlo - assert 0
            FAILED test_module.py::test_noop - assert 0
            2 failed in 0.12s

        让我们快速创建另一个测试模块，实际上在其模块命名空间中设置服务器 URL:

        .. code-block:: python

            # content of test_anothersmtp.py

            smtpserver = "mail.python.org"  # 将由 smtp fixture 读取


            def test_showhelo(smtp_connection):
                assert 0, smtp_connection.helo()

        运行它：

        .. code-block:: pytest

            $ pytest -qq --tb=short test_anothersmtp.py
            F                                                                    [100%]
            ================================= FAILURES =================================
            ______________________________ test_showhelo _______________________________
            test_anothersmtp.py:6: in test_showhelo
                assert 0, smtp_connection.helo()
            E   AssertionError: (250, b'mail.python.org')
            E   assert 0
            ------------------------- Captured stdout teardown -------------------------
            finalizing <smtplib.SMTP object at 0xdeadbeef0003> (mail.python.org)
            ========================= short test summary info ==========================
            FAILED test_anothersmtp.py::test_showhelo - AssertionError: (250, b'mail....

        瞧！ ``smtp_connection`` fixture 函数从模块命名空间中获取了我们的邮件服务器名称。

    .. tab:: 英文

        Fixture functions can accept the :py:class:`request <_pytest.fixtures.FixtureRequest>` object
        to introspect the "requesting" test function, class or module context.
        Further extending the previous ``smtp_connection`` fixture example, let's
        read an optional server URL from the test module which uses our fixture:

        .. code-block:: python

            # content of conftest.py
            import smtplib

            import pytest


            @pytest.fixture(scope="module")
            def smtp_connection(request):
                server = getattr(request.module, "smtpserver", "smtp.gmail.com")
                smtp_connection = smtplib.SMTP(server, 587, timeout=5)
                yield smtp_connection
                print(f"finalizing {smtp_connection} ({server})")
                smtp_connection.close()

        We use the ``request.module`` attribute to optionally obtain an
        ``smtpserver`` attribute from the test module.  If we just execute
        again, nothing much has changed:

        .. code-block:: pytest

            $ pytest -s -q --tb=no test_module.py
            FFfinalizing <smtplib.SMTP object at 0xdeadbeef0002> (smtp.gmail.com)

            ========================= short test summary info ==========================
            FAILED test_module.py::test_ehlo - assert 0
            FAILED test_module.py::test_noop - assert 0
            2 failed in 0.12s

        Let's quickly create another test module that actually sets the
        server URL in its module namespace:

        .. code-block:: python

            # content of test_anothersmtp.py

            smtpserver = "mail.python.org"  # will be read by smtp fixture


            def test_showhelo(smtp_connection):
                assert 0, smtp_connection.helo()

        Running it:

        .. code-block:: pytest

            $ pytest -qq --tb=short test_anothersmtp.py
            F                                                                    [100%]
            ================================= FAILURES =================================
            ______________________________ test_showhelo _______________________________
            test_anothersmtp.py:6: in test_showhelo
                assert 0, smtp_connection.helo()
            E   AssertionError: (250, b'mail.python.org')
            E   assert 0
            ------------------------- Captured stdout teardown -------------------------
            finalizing <smtplib.SMTP object at 0xdeadbeef0003> (mail.python.org)
            ========================= short test summary info ==========================
            FAILED test_anothersmtp.py::test_showhelo - AssertionError: (250, b'mail....

        voila! The ``smtp_connection`` fixture function picked up our mail server name
        from the module namespace.

.. _`using-markers`:

使用标记将数据传递给fixture
-------------------------------------------------------------

**Using markers to pass data to fixtures**

.. tabs::

    .. tab:: 中文

        使用 :py:class:`request <_pytest.fixtures.FixtureRequest>` 对象，fixture 还可以访问应用于测试函数的标记。这对于从测试向 fixture 传递数据非常有用：

        .. code-block:: python

            import pytest


            @pytest.fixture
            def fixt(request):
                marker = request.node.get_closest_marker("fixt_data")
                if marker is None:
                    # 以某种方式处理缺失的标记...
                    data = None
                else:
                    data = marker.args[0]

                # 使用数据做一些事情
                return data


            @pytest.mark.fixt_data(42)
            def test_fixt(fixt):
                assert fixt == 42

    .. tab:: 英文

        Using the :py:class:`request <_pytest.fixtures.FixtureRequest>` object, a fixture can also access
        markers which are applied to a test function. This can be useful to pass data
        into a fixture from a test:

        .. code-block:: python

            import pytest


            @pytest.fixture
            def fixt(request):
                marker = request.node.get_closest_marker("fixt_data")
                if marker is None:
                    # Handle missing marker in some way...
                    data = None
                else:
                    data = marker.args[0]

                # Do something with the data
                return data


            @pytest.mark.fixt_data(42)
            def test_fixt(fixt):
                assert fixt == 42

.. _`fixture-factory`:

fixture 工厂
-------------------------------------------------------------

**Factories as fixtures**

.. tabs::

    .. tab:: 中文

        “fixture 工厂” 模式可以帮助在单个测试中多次需要 fixture 结果的情况下使用。fixture 不直接返回数据，而是返回一个生成数据的函数。这个函数可以在测试中多次调用。

        工厂可以根据需要带有参数：

        .. code-block:: python

            @pytest.fixture
            def make_customer_record():
                def _make_customer_record(name):
                    return {"name": name, "orders": []}

                return _make_customer_record


            def test_customer_records(make_customer_record):
                customer_1 = make_customer_record("Lisa")
                customer_2 = make_customer_record("Mike")
                customer_3 = make_customer_record("Meredith")

        如果工厂创建的数据需要管理，fixture 可以处理这些：

        .. code-block:: python

            @pytest.fixture
            def make_customer_record():
                created_records = []

                def _make_customer_record(name):
                    record = models.Customer(name=name, orders=[])
                    created_records.append(record)
                    return record

                yield _make_customer_record

                for record in created_records:
                    record.destroy()


            def test_customer_records(make_customer_record):
                customer_1 = make_customer_record("Lisa")
                customer_2 = make_customer_record("Mike")
                customer_3 = make_customer_record("Meredith")

    .. tab:: 英文

        The "factory as fixture" pattern can help in situations where the result
        of a fixture is needed multiple times in a single test. Instead of returning
        data directly, the fixture instead returns a function which generates the data.
        This function can then be called multiple times in the test.

        Factories can have parameters as needed:

        .. code-block:: python

            @pytest.fixture
            def make_customer_record():
                def _make_customer_record(name):
                    return {"name": name, "orders": []}

                return _make_customer_record


            def test_customer_records(make_customer_record):
                customer_1 = make_customer_record("Lisa")
                customer_2 = make_customer_record("Mike")
                customer_3 = make_customer_record("Meredith")

        If the data created by the factory requires managing, the fixture can take care of that:

        .. code-block:: python

            @pytest.fixture
            def make_customer_record():
                created_records = []

                def _make_customer_record(name):
                    record = models.Customer(name=name, orders=[])
                    created_records.append(record)
                    return record

                yield _make_customer_record

                for record in created_records:
                    record.destroy()


            def test_customer_records(make_customer_record):
                customer_1 = make_customer_record("Lisa")
                customer_2 = make_customer_record("Mike")
                customer_3 = make_customer_record("Meredith")


.. _`fixture-parametrize`:

参数化 fixture
-----------------------------------------------------------------

**Parametrizing fixtures**

.. tabs::

    .. tab:: 中文

        Fixture 函数可以被参数化，这样它们将被多次调用，每次执行依赖于此 fixture 的测试集合。测试函数通常不需要意识到它们正在重新运行。Fixture 参数化有助于为可以以多种方式配置的组件编写详尽的功能测试。

        扩展之前的示例，我们可以标记 fixture 创建两个 ``smtp_connection`` fixture 实例，这将导致使用该 fixture 的所有测试运行两次。fixture 函数通过特殊的 :py:class:`request <pytest.FixtureRequest>` 对象访问每个参数：

        .. code-block:: python

            # content of conftest.py
            import smtplib

            import pytest


            @pytest.fixture(scope="module", params=["smtp.gmail.com", "mail.python.org"])
            def smtp_connection(request):
                smtp_connection = smtplib.SMTP(request.param, 587, timeout=5)
                yield smtp_connection
                print(f"finalizing {smtp_connection}")
                smtp_connection.close()

        主要变化是使用 :py:func:`@pytest.fixture <pytest.fixture>` 声明 ``params``，这是一个值的列表，fixture 函数将为每个值执行并可以通过 ``request.param`` 访问该值。测试函数代码不需要更改。因此，让我们再运行一次：

        .. code-block:: pytest

            $ pytest -q test_module.py
            FFFF                                                                 [100%]
            ================================= FAILURES =================================
            ________________________ test_ehlo[smtp.gmail.com] _________________________

            smtp_connection = <smtplib.SMTP object at 0xdeadbeef0004>

                def test_ehlo(smtp_connection):
                    response, msg = smtp_connection.ehlo()
                    assert response == 250
                    assert b"smtp.gmail.com" in msg
            >       assert 0  # for demo purposes
            E       assert 0

            test_module.py:7: AssertionError
            ________________________ test_noop[smtp.gmail.com] _________________________

            smtp_connection = <smtplib.SMTP object at 0xdeadbeef0004>

                def test_noop(smtp_connection):
                    response, msg = smtp_connection.noop()
                    assert response == 250
            >       assert 0  # for demo purposes
            E       assert 0

            test_module.py:13: AssertionError
            ________________________ test_ehlo[mail.python.org] ________________________

            smtp_connection = <smtplib.SMTP object at 0xdeadbeef0005>

                def test_ehlo(smtp_connection):
                    response, msg = smtp_connection.ehlo()
                    assert response == 250
            >       assert b"smtp.gmail.com" in msg
            E       AssertionError: assert b'smtp.gmail.com' in b'mail.python.org\nPIPELINING\nSIZE 51200000\nETRN\nSTARTTLS\nAUTH DIGEST-MD5 NTLM CRAM-MD5\nENHANCEDSTATUSCODES\n8BITMIME\nDSN\nSMTPUTF8\nCHUNKING'

            test_module.py:6: AssertionError
            -------------------------- Captured stdout setup ---------------------------
            finalizing <smtplib.SMTP object at 0xdeadbeef0004>
            ________________________ test_noop[mail.python.org] ________________________

            smtp_connection = <smtplib.SMTP object at 0xdeadbeef0005>

                def test_noop(smtp_connection):
                    response, msg = smtp_connection.noop()
                    assert response == 250
            >       assert 0  # for demo purposes
            E       assert 0

            test_module.py:13: AssertionError
            ------------------------- Captured stdout teardown -------------------------
            finalizing <smtplib.SMTP object at 0xdeadbeef0005>
            ========================= short test summary info ==========================
            FAILED test_module.py::test_ehlo[smtp.gmail.com] - assert 0
            FAILED test_module.py::test_noop[smtp.gmail.com] - assert 0
            FAILED test_module.py::test_ehlo[mail.python.org] - AssertionError: asser...
            FAILED test_module.py::test_noop[mail.python.org] - assert 0
            4 failed in 0.12s

        我们看到我们的两个测试函数都运行了两次，针对不同的 ``smtp_connection`` 实例。还要注意，使用 ``mail.python.org`` 连接时，第二个测试在 ``test_ehlo`` 中失败，因为期望的服务器字符串与实际到达的不同。

        pytest 将为参数化 fixture 中的每个 fixture 值构建一个字符串作为测试 ID，例如上述示例中的 ``test_ehlo[smtp.gmail.com]`` 和 ``test_ehlo[mail.python.org]`` 。这些 ID 可以与 ``-k`` 一起使用，以选择要运行的特定用例，并且在某个用例失败时，它们也会标识出特定的用例。使用 ``--collect-only`` 运行 pytest 将显示生成的 ID。

        数字、字符串、布尔值和 ``None`` 将在测试 ID 中使用其常规字符串表示形式。对于其他对象，pytest 将基于参数名称生成字符串。可以通过使用 ``ids`` 关键字参数自定义特定 fixture 值的测试 ID 中使用的字符串：

        .. code-block:: python

            # content of test_ids.py
            import pytest


            @pytest.fixture(params=[0, 1], ids=["spam", "ham"])
            def a(request):
                return request.param


            def test_a(a):
                pass


            def idfn(fixture_value):
                if fixture_value == 0:
                    return "eggs"
                else:
                    return None


            @pytest.fixture(params=[0, 1], ids=idfn)
            def b(request):
                return request.param


            def test_b(b):
                pass

        上述内容显示了 ``ids`` 可以是要使用的字符串列表，也可以是一个函数，该函数将使用 fixture 值调用，并返回要使用的字符串。在后者情况下，如果函数返回 ``None`` ，则将使用 pytest 的自动生成 ID。

        运行上述测试将生成以下测试 ID:

        .. code-block:: pytest

            $ pytest --collect-only
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 12 items

            <Dir fixtures.rst-224>
                <Module test_anothersmtp.py>
                <Function test_showhelo[smtp.gmail.com]>
                <Function test_showhelo[mail.python.org]>
                <Module test_emaillib.py>
                <Function test_email_received>
                <Module test_finalizers.py>
                <Function test_bar>
                <Module test_ids.py>
                <Function test_a[spam]>
                <Function test_a[ham]>
                <Function test_b[eggs]>
                <Function test_b[1]>
                <Module test_module.py>
                <Function test_ehlo[smtp.gmail.com]>
                <Function test_noop[smtp.gmail.com]>
                <Function test_ehlo[mail.python.org]>
                <Function test_noop[mail.python.org]>

            ======================= 12 tests collected in 0.12s ========================

    .. tab:: 英文

        Fixture functions can be parametrized in which case they will be called
        multiple times, each time executing the set of dependent tests, i.e. the
        tests that depend on this fixture.  Test functions usually do not need
        to be aware of their re-running.  Fixture parametrization helps to
        write exhaustive functional tests for components which themselves can be
        configured in multiple ways.

        Extending the previous example, we can flag the fixture to create two
        ``smtp_connection`` fixture instances which will cause all tests using the fixture
        to run twice.  The fixture function gets access to each parameter
        through the special :py:class:`request <pytest.FixtureRequest>` object:

        .. code-block:: python

            # content of conftest.py
            import smtplib

            import pytest


            @pytest.fixture(scope="module", params=["smtp.gmail.com", "mail.python.org"])
            def smtp_connection(request):
                smtp_connection = smtplib.SMTP(request.param, 587, timeout=5)
                yield smtp_connection
                print(f"finalizing {smtp_connection}")
                smtp_connection.close()

        The main change is the declaration of ``params`` with
        :py:func:`@pytest.fixture <pytest.fixture>`, a list of values
        for each of which the fixture function will execute and can access
        a value via ``request.param``.  No test function code needs to change.
        So let's just do another run:

        .. code-block:: pytest

            $ pytest -q test_module.py
            FFFF                                                                 [100%]
            ================================= FAILURES =================================
            ________________________ test_ehlo[smtp.gmail.com] _________________________

            smtp_connection = <smtplib.SMTP object at 0xdeadbeef0004>

                def test_ehlo(smtp_connection):
                    response, msg = smtp_connection.ehlo()
                    assert response == 250
                    assert b"smtp.gmail.com" in msg
            >       assert 0  # for demo purposes
            E       assert 0

            test_module.py:7: AssertionError
            ________________________ test_noop[smtp.gmail.com] _________________________

            smtp_connection = <smtplib.SMTP object at 0xdeadbeef0004>

                def test_noop(smtp_connection):
                    response, msg = smtp_connection.noop()
                    assert response == 250
            >       assert 0  # for demo purposes
            E       assert 0

            test_module.py:13: AssertionError
            ________________________ test_ehlo[mail.python.org] ________________________

            smtp_connection = <smtplib.SMTP object at 0xdeadbeef0005>

                def test_ehlo(smtp_connection):
                    response, msg = smtp_connection.ehlo()
                    assert response == 250
            >       assert b"smtp.gmail.com" in msg
            E       AssertionError: assert b'smtp.gmail.com' in b'mail.python.org\nPIPELINING\nSIZE 51200000\nETRN\nSTARTTLS\nAUTH DIGEST-MD5 NTLM CRAM-MD5\nENHANCEDSTATUSCODES\n8BITMIME\nDSN\nSMTPUTF8\nCHUNKING'

            test_module.py:6: AssertionError
            -------------------------- Captured stdout setup ---------------------------
            finalizing <smtplib.SMTP object at 0xdeadbeef0004>
            ________________________ test_noop[mail.python.org] ________________________

            smtp_connection = <smtplib.SMTP object at 0xdeadbeef0005>

                def test_noop(smtp_connection):
                    response, msg = smtp_connection.noop()
                    assert response == 250
            >       assert 0  # for demo purposes
            E       assert 0

            test_module.py:13: AssertionError
            ------------------------- Captured stdout teardown -------------------------
            finalizing <smtplib.SMTP object at 0xdeadbeef0005>
            ========================= short test summary info ==========================
            FAILED test_module.py::test_ehlo[smtp.gmail.com] - assert 0
            FAILED test_module.py::test_noop[smtp.gmail.com] - assert 0
            FAILED test_module.py::test_ehlo[mail.python.org] - AssertionError: asser...
            FAILED test_module.py::test_noop[mail.python.org] - assert 0
            4 failed in 0.12s

        We see that our two test functions each ran twice, against the different
        ``smtp_connection`` instances.  Note also, that with the ``mail.python.org``
        connection the second test fails in ``test_ehlo`` because a
        different server string is expected than what arrived.

        pytest will build a string that is the test ID for each fixture value
        in a parametrized fixture, e.g. ``test_ehlo[smtp.gmail.com]`` and
        ``test_ehlo[mail.python.org]`` in the above examples.  These IDs can
        be used with ``-k`` to select specific cases to run, and they will
        also identify the specific case when one is failing.  Running pytest
        with ``--collect-only`` will show the generated IDs.

        Numbers, strings, booleans and ``None`` will have their usual string
        representation used in the test ID. For other objects, pytest will
        make a string based on the argument name.  It is possible to customise
        the string used in a test ID for a certain fixture value by using the
        ``ids`` keyword argument:

        .. code-block:: python

            # content of test_ids.py
            import pytest


            @pytest.fixture(params=[0, 1], ids=["spam", "ham"])
            def a(request):
                return request.param


            def test_a(a):
                pass


            def idfn(fixture_value):
                if fixture_value == 0:
                    return "eggs"
                else:
                    return None


            @pytest.fixture(params=[0, 1], ids=idfn)
            def b(request):
                return request.param


            def test_b(b):
                pass

        The above shows how ``ids`` can be either a list of strings to use or
        a function which will be called with the fixture value and then
        has to return a string to use.  In the latter case if the function
        returns ``None`` then pytest's auto-generated ID will be used.

        Running the above tests results in the following test IDs being used:

        .. code-block:: pytest

            $ pytest --collect-only
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 12 items

            <Dir fixtures.rst-224>
                <Module test_anothersmtp.py>
                <Function test_showhelo[smtp.gmail.com]>
                <Function test_showhelo[mail.python.org]>
                <Module test_emaillib.py>
                <Function test_email_received>
                <Module test_finalizers.py>
                <Function test_bar>
                <Module test_ids.py>
                <Function test_a[spam]>
                <Function test_a[ham]>
                <Function test_b[eggs]>
                <Function test_b[1]>
                <Module test_module.py>
                <Function test_ehlo[smtp.gmail.com]>
                <Function test_noop[smtp.gmail.com]>
                <Function test_ehlo[mail.python.org]>
                <Function test_noop[mail.python.org]>

            ======================= 12 tests collected in 0.12s ========================

.. _`fixture-parametrize-marks`:

与标记一起的参数化 fixture
--------------------------------------

**Using marks with parametrized fixtures**

.. tabs::

    .. tab:: 中文

        :func:`pytest.param` 可以用于在参数化 fixture 的值集合中应用标记，方式与 :ref:`@pytest.mark.parametrize <@pytest.mark.parametrize>` 一致。

        示例：

        .. code-block:: python

            # content of test_fixture_marks.py
            import pytest


            @pytest.fixture(params=[0, 1, pytest.param(2, marks=pytest.mark.skip)])
            def data_set(request):
                return request.param


            def test_data(data_set):
                pass

        运行此测试将 *跳过* 对 ``data_set`` 值为 ``2`` 的调用：

        .. code-block:: pytest

            $ pytest test_fixture_marks.py -v
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y -- $PYTHON_PREFIX/bin/python
            cachedir: .pytest_cache
            rootdir: /home/sweet/project
            collecting ... collected 3 items

            test_fixture_marks.py::test_data[0] PASSED                           [ 33%]
            test_fixture_marks.py::test_data[1] PASSED                           [ 66%]
            test_fixture_marks.py::test_data[2] SKIPPED (unconditional skip)     [100%]

            ======================= 2 passed, 1 skipped in 0.12s =======================
            
    .. tab:: 英文

        :func:`pytest.param` can be used to apply marks in values sets of parametrized fixtures in the same way
        that they can be used with :ref:`@pytest.mark.parametrize <@pytest.mark.parametrize>`.

        Example:

        .. code-block:: python

            # content of test_fixture_marks.py
            import pytest


            @pytest.fixture(params=[0, 1, pytest.param(2, marks=pytest.mark.skip)])
            def data_set(request):
                return request.param


            def test_data(data_set):
                pass

        Running this test will *skip* the invocation of ``data_set`` with value ``2``:

        .. code-block:: pytest

            $ pytest test_fixture_marks.py -v
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y -- $PYTHON_PREFIX/bin/python
            cachedir: .pytest_cache
            rootdir: /home/sweet/project
            collecting ... collected 3 items

            test_fixture_marks.py::test_data[0] PASSED                           [ 33%]
            test_fixture_marks.py::test_data[1] PASSED                           [ 66%]
            test_fixture_marks.py::test_data[2] SKIPPED (unconditional skip)     [100%]

            ======================= 2 passed, 1 skipped in 0.12s =======================

.. _`interdependent fixtures`:

模块化：使用 Fixture 函数中的 Fixture
----------------------------------------------------------

**Modularity: using fixtures from a fixture function**

.. tabs::

    .. tab:: 中文

        除了在测试函数中使用 fixtures，fixture 函数本身也可以使用其他 fixtures。这有助于创建模块化的 fixture 设计，并允许在多个项目中重用框架特定的 fixtures。作为一个简单的例子，我们可以扩展之前的示例，并实例化一个对象 ``app``，在其中插入已经定义的 ``smtp_connection`` 资源：

        .. code-block:: python

            # content of test_appsetup.py

            import pytest


            class App:
                def __init__(self, smtp_connection):
                    self.smtp_connection = smtp_connection


            @pytest.fixture(scope="module")
            def app(smtp_connection):
                return App(smtp_connection)


            def test_smtp_connection_exists(app):
                assert app.smtp_connection

        在这里，我们声明了一个 ``app`` fixture，它接收之前定义的 ``smtp_connection`` fixture，并用它实例化一个 ``App`` 对象。让我们运行它：

        .. code-block:: pytest

            $ pytest -v test_appsetup.py
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y -- $PYTHON_PREFIX/bin/python
            cachedir: .pytest_cache
            rootdir: /home/sweet/project
            collecting ... collected 2 items

            test_appsetup.py::test_smtp_connection_exists[smtp.gmail.com] PASSED [ 50%]
            test_appsetup.py::test_smtp_connection_exists[mail.python.org] PASSED [100%]

            ============================ 2 passed in 0.12s =============================

        由于 ``smtp_connection`` 的参数化，测试将使用两个不同的 ``App`` 实例和各自的 smtp 服务器运行两次。``app`` fixture 不需要关注 ``smtp_connection`` 的参数化，因为 pytest 将完全分析 fixture 依赖图。

        请注意，``app`` fixture 的作用域是 ``module``，并使用了一个模块作用域的 ``smtp_connection`` fixture。如果 ``smtp_connection`` 在 ``session`` 范围内缓存，这个示例仍然可以工作：使用“更广泛”作用域的 fixtures 是可以的，但反之则不行：一个会话范围的 fixture 不能以有意义的方式使用一个模块范围的 fixture。

    .. tab:: 英文

        In addition to using fixtures in test functions, fixture functions
        can use other fixtures themselves.  This contributes to a modular design
        of your fixtures and allows reuse of framework-specific fixtures across
        many projects.  As a simple example, we can extend the previous example
        and instantiate an object ``app`` where we stick the already defined
        ``smtp_connection`` resource into it:

        .. code-block:: python

            # content of test_appsetup.py

            import pytest


            class App:
                def __init__(self, smtp_connection):
                    self.smtp_connection = smtp_connection


            @pytest.fixture(scope="module")
            def app(smtp_connection):
                return App(smtp_connection)


            def test_smtp_connection_exists(app):
                assert app.smtp_connection

        Here we declare an ``app`` fixture which receives the previously defined
        ``smtp_connection`` fixture and instantiates an ``App`` object with it.  Let's run it:

        .. code-block:: pytest

            $ pytest -v test_appsetup.py
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y -- $PYTHON_PREFIX/bin/python
            cachedir: .pytest_cache
            rootdir: /home/sweet/project
            collecting ... collected 2 items

            test_appsetup.py::test_smtp_connection_exists[smtp.gmail.com] PASSED [ 50%]
            test_appsetup.py::test_smtp_connection_exists[mail.python.org] PASSED [100%]

            ============================ 2 passed in 0.12s =============================

        Due to the parametrization of ``smtp_connection``, the test will run twice with two
        different ``App`` instances and respective smtp servers.  There is no
        need for the ``app`` fixture to be aware of the ``smtp_connection``
        parametrization because pytest will fully analyse the fixture dependency graph.

        Note that the ``app`` fixture has a scope of ``module`` and uses a
        module-scoped ``smtp_connection`` fixture.  The example would still work if
        ``smtp_connection`` was cached on a ``session`` scope: it is fine for fixtures to use
        "broader" scoped fixtures but not the other way round:
        A session-scoped fixture could not use a module-scoped one in a
        meaningful way.


.. _`automatic per-resource grouping`:

根据fixture实例自动对测试进行分组
----------------------------------------------------------

**Automatic grouping of tests by fixture instances**

.. tabs::

    .. tab:: 中文

        .. regendoc: wipe

        pytest 在测试运行期间最小化活跃 fixtures 的数量。如果你有一个参数化的 fixture，那么所有使用它的测试将首先使用一个实例执行，然后在创建下一个 fixture 实例之前调用清理函数。除此之外，这也简化了测试创建和使用全局状态的应用程序。

        以下示例使用两个参数化的 fixtures，其中一个以模块为基础作用域，所有函数都执行 ``print`` 调用以展示设置/清理流程：

        .. code-block:: python

            # content of test_module.py
            import pytest


            @pytest.fixture(scope="module", params=["mod1", "mod2"])
            def modarg(request):
                param = request.param
                print("  SETUP modarg", param)
                yield param
                print("  TEARDOWN modarg", param)


            @pytest.fixture(scope="function", params=[1, 2])
            def otherarg(request):
                param = request.param
                print("  SETUP otherarg", param)
                yield param
                print("  TEARDOWN otherarg", param)


            def test_0(otherarg):
                print("  RUN test0 with otherarg", otherarg)


            def test_1(modarg):
                print("  RUN test1 with modarg", modarg)


            def test_2(otherarg, modarg):
                print(f"  RUN test2 with otherarg {otherarg} and modarg {modarg}")


        让我们在详细模式下运行测试，并查看打印输出：

        .. code-block:: pytest

            $ pytest -v -s test_module.py
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y -- $PYTHON_PREFIX/bin/python
            cachedir: .pytest_cache
            rootdir: /home/sweet/project
            collecting ... collected 8 items

            test_module.py::test_0[1]   SETUP otherarg 1
            RUN test0 with otherarg 1
            PASSED  TEARDOWN otherarg 1

            test_module.py::test_0[2]   SETUP otherarg 2
            RUN test0 with otherarg 2
            PASSED  TEARDOWN otherarg 2

            test_module.py::test_1[mod1]   SETUP modarg mod1
            RUN test1 with modarg mod1
            PASSED
            test_module.py::test_2[mod1-1]   SETUP otherarg 1
            RUN test2 with otherarg 1 and modarg mod1
            PASSED  TEARDOWN otherarg 1

            test_module.py::test_2[mod1-2]   SETUP otherarg 2
            RUN test2 with otherarg 2 and modarg mod1
            PASSED  TEARDOWN otherarg 2

            test_module.py::test_1[mod2]   TEARDOWN modarg mod1
            SETUP modarg mod2
            RUN test1 with modarg mod2
            PASSED
            test_module.py::test_2[mod2-1]   SETUP otherarg 1
            RUN test2 with otherarg 1 and modarg mod2
            PASSED  TEARDOWN otherarg 1

            test_module.py::test_2[mod2-2]   SETUP otherarg 2
            RUN test2 with otherarg 2 and modarg mod2
            PASSED  TEARDOWN otherarg 2
            TEARDOWN modarg mod2


            ============================ 8 passed in 0.12s =============================

        可以看到，参数化的模块作用域 ``modarg`` 资源导致了测试执行的顺序，从而使“活跃”资源的数量最少。 ``mod1`` 参数化资源的清理函数在设置 ``mod2`` 资源之前执行。

        特别要注意的是，test_0 完全独立并最先完成。然后执行 test_1 使用 ``mod1``，接着是 test_2 使用 ``mod1``，然后是 test_1 使用 ``mod2``，最后是 test_2 使用 ``mod2``。

        ``otherarg`` 参数化资源（具有函数作用域）在使用它的每个测试之前设置，并在之后清理。

    .. tab:: 英文

        .. regendoc: wipe

        pytest minimizes the number of active fixtures during test runs.
        If you have a parametrized fixture, then all the tests using it will
        first execute with one instance and then finalizers are called
        before the next fixture instance is created.  Among other things,
        this eases testing of applications which create and use global state.

        The following example uses two parametrized fixtures, one of which is
        scoped on a per-module basis, and all the functions perform ``print`` calls
        to show the setup/teardown flow:

        .. code-block:: python

            # content of test_module.py
            import pytest


            @pytest.fixture(scope="module", params=["mod1", "mod2"])
            def modarg(request):
                param = request.param
                print("  SETUP modarg", param)
                yield param
                print("  TEARDOWN modarg", param)


            @pytest.fixture(scope="function", params=[1, 2])
            def otherarg(request):
                param = request.param
                print("  SETUP otherarg", param)
                yield param
                print("  TEARDOWN otherarg", param)


            def test_0(otherarg):
                print("  RUN test0 with otherarg", otherarg)


            def test_1(modarg):
                print("  RUN test1 with modarg", modarg)


            def test_2(otherarg, modarg):
                print(f"  RUN test2 with otherarg {otherarg} and modarg {modarg}")


        Let's run the tests in verbose mode and with looking at the print-output:

        .. code-block:: pytest

            $ pytest -v -s test_module.py
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y -- $PYTHON_PREFIX/bin/python
            cachedir: .pytest_cache
            rootdir: /home/sweet/project
            collecting ... collected 8 items

            test_module.py::test_0[1]   SETUP otherarg 1
            RUN test0 with otherarg 1
            PASSED  TEARDOWN otherarg 1

            test_module.py::test_0[2]   SETUP otherarg 2
            RUN test0 with otherarg 2
            PASSED  TEARDOWN otherarg 2

            test_module.py::test_1[mod1]   SETUP modarg mod1
            RUN test1 with modarg mod1
            PASSED
            test_module.py::test_2[mod1-1]   SETUP otherarg 1
            RUN test2 with otherarg 1 and modarg mod1
            PASSED  TEARDOWN otherarg 1

            test_module.py::test_2[mod1-2]   SETUP otherarg 2
            RUN test2 with otherarg 2 and modarg mod1
            PASSED  TEARDOWN otherarg 2

            test_module.py::test_1[mod2]   TEARDOWN modarg mod1
            SETUP modarg mod2
            RUN test1 with modarg mod2
            PASSED
            test_module.py::test_2[mod2-1]   SETUP otherarg 1
            RUN test2 with otherarg 1 and modarg mod2
            PASSED  TEARDOWN otherarg 1

            test_module.py::test_2[mod2-2]   SETUP otherarg 2
            RUN test2 with otherarg 2 and modarg mod2
            PASSED  TEARDOWN otherarg 2
            TEARDOWN modarg mod2


            ============================ 8 passed in 0.12s =============================

        You can see that the parametrized module-scoped ``modarg`` resource caused an
        ordering of test execution that lead to the fewest possible "active" resources.
        The finalizer for the ``mod1`` parametrized resource was executed before the
        ``mod2`` resource was setup.

        In particular notice that test_0 is completely independent and finishes first.
        Then test_1 is executed with ``mod1``, then test_2 with ``mod1``, then test_1
        with ``mod2`` and finally test_2 with ``mod2``.

        The ``otherarg`` parametrized resource (having function scope) was set up before
        and teared down after every test that used it.


.. _`usefixtures`:

在类和模块中通过 ``usefixtures`` 使用 fixture
--------------------------------------------------------

**Use fixtures in classes and modules with** ``usefixtures``

.. tabs::

    .. tab:: 中文

        .. regendoc:wipe

        有时测试函数不需要直接访问一个 fixture 对象。  
        例如，测试可能需要以一个空目录作为当前工作目录，但不关心具体的目录。  
        以下是如何使用标准的 :mod:`tempfile` 和 pytest fixtures 来实现这一点。  
        我们将 fixture 的创建分离到一个 :file:`conftest.py` 文件中：

        .. code-block:: python

            # conftest.py 的内容

            import os
            import tempfile

            import pytest


            @pytest.fixture
            def cleandir():
                with tempfile.TemporaryDirectory() as newpath:
                    old_cwd = os.getcwd()
                    os.chdir(newpath)
                    yield
                    os.chdir(old_cwd)

        并通过 ``usefixtures`` 标记在测试模块中声明其使用：

        .. code-block:: python

            # test_setenv.py 的内容
            import os

            import pytest


            @pytest.mark.usefixtures("cleandir")
            class TestDirectoryInit:
                def test_cwd_starts_empty(self):
                    assert os.listdir(os.getcwd()) == []
                    with open("myfile", "w", encoding="utf-8") as f:
                        f.write("hello")

                def test_cwd_again_starts_empty(self):
                    assert os.listdir(os.getcwd()) == []

        由于 ``usefixtures`` 标记，``cleandir`` fixture 将在每个测试方法的执行中被要求，就像你为它们每个都指定了一个 "cleandir" 函数参数。  
        让我们运行它以验证我们的 fixture 是否被激活，并且测试通过：

        .. code-block:: pytest

            $ pytest -q
            ..                                                                   [100%]
            2 passed in 0.12s

        你可以这样指定多个 fixtures:

        .. code-block:: python

            @pytest.mark.usefixtures("cleandir", "anotherfixture")
            def test(): ...

        你还可以在测试模块级别使用 :globalvar:`pytestmark` 指定 fixture 的使用：

        .. code-block:: python

            pytestmark = pytest.mark.usefixtures("cleandir")


        将项目中所有测试所需的 fixtures 放入 ini 文件中也是可行的：

        .. code-block:: ini

            # pytest.ini 的内容
            [pytest]
            usefixtures = cleandir


        .. warning::

            请注意，这个标记在 **fixture 函数** 中没有效果。  
            例如，这个 **不会按预期工作**:

            .. code-block:: python

                @pytest.mark.usefixtures("my_other_fixture")
                @pytest.fixture
                def my_fixture_that_sadly_wont_use_my_other_fixture(): ...

            这将生成一个弃用警告，并将在 Pytest 8 中变为错误。

    .. tab:: 英文

        .. regendoc:wipe

        Sometimes test functions do not directly need access to a fixture object.
        For example, tests may require to operate with an empty directory as the
        current working directory but otherwise do not care for the concrete
        directory.  Here is how you can use the standard :mod:`tempfile`
        and pytest fixtures to
        achieve it.  We separate the creation of the fixture into a :file:`conftest.py`
        file:

        .. code-block:: python

            # content of conftest.py

            import os
            import tempfile

            import pytest


            @pytest.fixture
            def cleandir():
                with tempfile.TemporaryDirectory() as newpath:
                    old_cwd = os.getcwd()
                    os.chdir(newpath)
                    yield
                    os.chdir(old_cwd)

        and declare its use in a test module via a ``usefixtures`` marker:

        .. code-block:: python

            # content of test_setenv.py
            import os

            import pytest


            @pytest.mark.usefixtures("cleandir")
            class TestDirectoryInit:
                def test_cwd_starts_empty(self):
                    assert os.listdir(os.getcwd()) == []
                    with open("myfile", "w", encoding="utf-8") as f:
                        f.write("hello")

                def test_cwd_again_starts_empty(self):
                    assert os.listdir(os.getcwd()) == []

        Due to the ``usefixtures`` marker, the ``cleandir`` fixture
        will be required for the execution of each test method, just as if
        you specified a "cleandir" function argument to each of them.  Let's run it
        to verify our fixture is activated and the tests pass:

        .. code-block:: pytest

            $ pytest -q
            ..                                                                   [100%]
            2 passed in 0.12s

        You can specify multiple fixtures like this:

        .. code-block:: python

            @pytest.mark.usefixtures("cleandir", "anotherfixture")
            def test(): ...

        and you may specify fixture usage at the test module level using :globalvar:`pytestmark`:

        .. code-block:: python

            pytestmark = pytest.mark.usefixtures("cleandir")


        It is also possible to put fixtures required by all tests in your project
        into an ini-file:

        .. code-block:: ini

            # content of pytest.ini
            [pytest]
            usefixtures = cleandir


        .. warning::

            Note this mark has no effect in **fixture functions**. For example,
            this **will not work as expected**:

            .. code-block:: python

                @pytest.mark.usefixtures("my_other_fixture")
                @pytest.fixture
                def my_fixture_that_sadly_wont_use_my_other_fixture(): ...

            This generates a deprecation warning, and will become an error in Pytest 8.

.. _`override fixtures`:

覆盖各个级别的fixtures
-------------------------------------

**Overriding fixtures on various levels**

.. tabs::

    .. tab:: 中文

        在相对较大的测试套件中，您很可能需要用一个 ``locally`` 定义的 fixture 来 ``override`` 一个 ``global`` 或 ``root`` fixture，以保持测试代码的可读性和可维护性。

    .. tab:: 英文

        In relatively large test suite, you most likely need to ``override`` a ``global`` or ``root`` fixture with a ``locally``
        defined one, keeping the test code readable and maintainable.

覆盖文件夹（conftest）级别的fixture
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**Override a fixture on a folder (conftest) level**

.. tabs::

    .. tab:: 中文

        给定的测试文件结构为：

        ::

            tests/
                conftest.py
                    # tests/conftest.py 的内容
                    import pytest

                    @pytest.fixture
                    def username():
                        return 'username'

                test_something.py
                    # tests/test_something.py 的内容
                    def test_username(username):
                        assert username == 'username'

                subfolder/
                    conftest.py
                        # tests/subfolder/conftest.py 的内容
                        import pytest

                        @pytest.fixture
                        def username(username):
                            return 'overridden-' + username

                    test_something_else.py
                        # tests/subfolder/test_something_else.py 的内容
                        def test_username(username):
                            assert username == 'overridden-username'

        如您所见，可以在特定的测试文件夹级别覆盖同名的 fixture。  
        请注意，可以轻松访问 ``overriding`` fixture 中的 ``base`` 或 ``super`` fixture —— 如上例所示。

    .. tab:: 英文

        Given the tests file structure is:

        ::

            tests/
                conftest.py
                    # content of tests/conftest.py
                    import pytest

                    @pytest.fixture
                    def username():
                        return 'username'

                test_something.py
                    # content of tests/test_something.py
                    def test_username(username):
                        assert username == 'username'

                subfolder/
                    conftest.py
                        # content of tests/subfolder/conftest.py
                        import pytest

                        @pytest.fixture
                        def username(username):
                            return 'overridden-' + username

                    test_something_else.py
                        # content of tests/subfolder/test_something_else.py
                        def test_username(username):
                            assert username == 'overridden-username'

        As you can see, a fixture with the same name can be overridden for certain test folder level.
        Note that the ``base`` or ``super`` fixture can be accessed from the ``overriding``
        fixture easily - used in the example above.

在测试模块级别覆盖一个fixture
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**Override a fixture on a test module level**

.. tabs::

    .. tab:: 中文

        给定的测试文件结构为：

        ::

            tests/
                conftest.py
                    # tests/conftest.py 的内容
                    import pytest

                    @pytest.fixture
                    def username():
                        return 'username'

                test_something.py
                    # tests/test_something.py 的内容
                    import pytest

                    @pytest.fixture
                    def username(username):
                        return 'overridden-' + username

                    def test_username(username):
                        assert username == 'overridden-username'

                test_something_else.py
                    # tests/test_something_else.py 的内容
                    import pytest

                    @pytest.fixture
                    def username(username):
                        return 'overridden-else-' + username

                    def test_username(username):
                        assert username == 'overridden-else-username'

        在上述示例中，可以在特定测试模块中覆盖同名的 fixture。

    .. tab:: 英文

        Given the tests file structure is:

        ::

            tests/
                conftest.py
                    # content of tests/conftest.py
                    import pytest

                    @pytest.fixture
                    def username():
                        return 'username'

                test_something.py
                    # content of tests/test_something.py
                    import pytest

                    @pytest.fixture
                    def username(username):
                        return 'overridden-' + username

                    def test_username(username):
                        assert username == 'overridden-username'

                test_something_else.py
                    # content of tests/test_something_else.py
                    import pytest

                    @pytest.fixture
                    def username(username):
                        return 'overridden-else-' + username

                    def test_username(username):
                        assert username == 'overridden-else-username'

        In the example above, a fixture with the same name can be overridden for certain test module.


使用直接测试参数化覆盖一个fixture
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**Override a fixture with direct test parametrization**

.. tabs::

    .. tab:: 中文

        给定的测试文件结构为：

        ::

            tests/
                conftest.py
                    # tests/conftest.py 的内容
                    import pytest

                    @pytest.fixture
                    def username():
                        return 'username'

                    @pytest.fixture
                    def other_username(username):
                        return 'other-' + username

                test_something.py
                    # tests/test_something.py 的内容
                    import pytest

                    @pytest.mark.parametrize('username', ['directly-overridden-username'])
                    def test_username(username):
                        assert username == 'directly-overridden-username'

                    @pytest.mark.parametrize('username', ['directly-overridden-username-other'])
                    def test_username_other(other_username):
                        assert other_username == 'other-directly-overridden-username-other'

        在上述示例中，fixture 的值被测试参数值覆盖。请注意，即使测试没有直接使用该 fixture（在函数原型中未提及），fixture 的值仍可以通过这种方式被覆盖。

    .. tab:: 英文

        Given the tests file structure is:

        ::

            tests/
                conftest.py
                    # content of tests/conftest.py
                    import pytest

                    @pytest.fixture
                    def username():
                        return 'username'

                    @pytest.fixture
                    def other_username(username):
                        return 'other-' + username

                test_something.py
                    # content of tests/test_something.py
                    import pytest

                    @pytest.mark.parametrize('username', ['directly-overridden-username'])
                    def test_username(username):
                        assert username == 'directly-overridden-username'

                    @pytest.mark.parametrize('username', ['directly-overridden-username-other'])
                    def test_username_other(other_username):
                        assert other_username == 'other-directly-overridden-username-other'

        In the example above, a fixture value is overridden by the test parameter value. Note that the value of the fixture
        can be overridden this way even if the test doesn't use it directly (doesn't mention it in the function prototype).


使用非参数化的fixture覆盖参数化的fixture，反之亦然
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**Override a parametrized fixture with non-parametrized one and vice versa**

.. tabs::

    .. tab:: 中文

        给定的测试文件结构为：

        ::

            tests/
                conftest.py
                    # tests/conftest.py 的内容
                    import pytest

                    @pytest.fixture(params=['one', 'two', 'three'])
                    def parametrized_username(request):
                        return request.param

                    @pytest.fixture
                    def non_parametrized_username(request):
                        return 'username'

                test_something.py
                    # tests/test_something.py 的内容
                    import pytest

                    @pytest.fixture
                    def parametrized_username():
                        return 'overridden-username'

                    @pytest.fixture(params=['one', 'two', 'three'])
                    def non_parametrized_username(request):
                        return request.param

                    def test_username(parametrized_username):
                        assert parametrized_username == 'overridden-username'

                    def test_parametrized_username(non_parametrized_username):
                        assert non_parametrized_username in ['one', 'two', 'three']

                test_something_else.py
                    # tests/test_something_else.py 的内容
                    def test_username(parametrized_username):
                        assert parametrized_username in ['one', 'two', 'three']

                    def test_username(non_parametrized_username):
                        assert non_parametrized_username == 'username'

        在上述示例中，一个参数化的 fixture 被非参数化版本覆盖，  
        而一个非参数化的 fixture 则在特定测试模块中被参数化版本覆盖。  
        显然，这同样适用于测试文件夹级别。

    .. tab:: 英文

        Given the tests file structure is:

        ::

            tests/
                conftest.py
                    # content of tests/conftest.py
                    import pytest

                    @pytest.fixture(params=['one', 'two', 'three'])
                    def parametrized_username(request):
                        return request.param

                    @pytest.fixture
                    def non_parametrized_username(request):
                        return 'username'

                test_something.py
                    # content of tests/test_something.py
                    import pytest

                    @pytest.fixture
                    def parametrized_username():
                        return 'overridden-username'

                    @pytest.fixture(params=['one', 'two', 'three'])
                    def non_parametrized_username(request):
                        return request.param

                    def test_username(parametrized_username):
                        assert parametrized_username == 'overridden-username'

                    def test_parametrized_username(non_parametrized_username):
                        assert non_parametrized_username in ['one', 'two', 'three']

                test_something_else.py
                    # content of tests/test_something_else.py
                    def test_username(parametrized_username):
                        assert parametrized_username in ['one', 'two', 'three']

                    def test_username(non_parametrized_username):
                        assert non_parametrized_username == 'username'

        In the example above, a parametrized fixture is overridden with a non-parametrized version, and
        a non-parametrized fixture is overridden with a parametrized version for certain test module.
        The same applies for the test folder level obviously.


使用其他项目的fixtures
----------------------------------

**Using fixtures from other projects**

.. tabs::

    .. tab:: 中文

        通常，提供 pytest 支持的项目将使用 :ref:`entry points <pip-installable plugins>`，  
        因此，只需将这些项目安装到环境中，即可使这些 fixtures 可用。

        如果您想使用来自不使用 entry points 的项目的 fixtures，您可以在顶层 ``conftest.py`` 文件中定义 :globalvar:`pytest_plugins`，  
        以将该模块注册为插件。

        假设您在 ``mylibrary.fixtures`` 中有一些 fixtures，并且想将它们重用到您的 ``app/tests`` 目录中。

        您只需在 ``app/tests/conftest.py`` 中定义 :globalvar:`pytest_plugins` 指向该模块即可。

        .. code-block:: python

            pytest_plugins = "mylibrary.fixtures"

        这有效地将 ``mylibrary.fixtures`` 注册为插件，使其所有的 fixtures 和钩子可用于 ``app/tests`` 中的测试。

        .. note::

            有时用户会从其他项目 *import* fixtures 以供使用，但这并不推荐：将 fixtures 导入模块会将它们在 pytest 中注册为 *defined* 在该模块中。

            这会有一些小影响，比如在 ``pytest --help`` 中多次出现，但这并不 **推荐**，因为此行为可能在未来版本中发生变化或停止工作。

    .. tab:: 英文

        Usually projects that provide pytest support will use :ref:`entry points <pip-installable plugins>`,
        so just installing those projects into an environment will make those fixtures available for use.

        In case you want to use fixtures from a project that does not use entry points, you can
        define :globalvar:`pytest_plugins` in your top ``conftest.py`` file to register that module
        as a plugin.

        Suppose you have some fixtures in ``mylibrary.fixtures`` and you want to reuse them into your
        ``app/tests`` directory.

        All you need to do is to define :globalvar:`pytest_plugins` in ``app/tests/conftest.py``
        pointing to that module.

        .. code-block:: python

            pytest_plugins = "mylibrary.fixtures"

        This effectively registers ``mylibrary.fixtures`` as a plugin, making all its fixtures and
        hooks available to tests in ``app/tests``.

        .. note::

            Sometimes users will *import* fixtures from other projects for use, however this is not
            recommended: importing fixtures into a module will register them in pytest
            as *defined* in that module.

            This has minor consequences, such as appearing multiple times in ``pytest --help``,
            but it is not **recommended** because this behavior might change/stop working
            in future versions.
