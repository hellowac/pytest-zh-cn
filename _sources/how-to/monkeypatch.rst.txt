.. _monkeypatching:

如何使用 猴子补丁/mock 模块和环境 
================================================================

**How to monkeypatch/mock modules and environments**

.. currentmodule:: pytest

.. tabs::

    .. tab:: 中文

        有时，测试需要调用依赖于全局设置的功能，或者调用一些无法轻易测试的代码，例如网络访问。 ``monkeypatch`` fixture 可以帮助您安全地设置/删除属性、字典项或环境变量，或修改 ``sys.path`` 以进行导入。

        ``monkeypatch`` fixture 提供了以下辅助方法，用于在测试中安全地修补和模拟功能：

        * :meth:`monkeypatch.setattr(obj, name, value, raising=True) <pytest.MonkeyPatch.setattr>`
        * :meth:`monkeypatch.delattr(obj, name, raising=True) <pytest.MonkeyPatch.delattr>`
        * :meth:`monkeypatch.setitem(mapping, name, value) <pytest.MonkeyPatch.setitem>`
        * :meth:`monkeypatch.delitem(obj, name, raising=True) <pytest.MonkeyPatch.delitem>`
        * :meth:`monkeypatch.setenv(name, value, prepend=None) <pytest.MonkeyPatch.setenv>`
        * :meth:`monkeypatch.delenv(name, raising=True) <pytest.MonkeyPatch.delenv>`
        * :meth:`monkeypatch.syspath_prepend(path) <pytest.MonkeyPatch.syspath_prepend>`
        * :meth:`monkeypatch.chdir(path) <pytest.MonkeyPatch.chdir>`
        * :meth:`monkeypatch.context() <pytest.MonkeyPatch.context>`

        所有修改将在请求的测试函数或fixture 完成后撤销。 ``raising`` 参数决定如果设置/删除操作的目标不存在时，是否会引发 ``KeyError`` 或 ``AttributeError``。

        考虑以下场景：

        1. 修改函数的行为或类的属性进行测试，例如，您不会为测试进行 API 调用或数据库连接，但您知道预期的输出应该是什么。使用 :py:meth:`monkeypatch.setattr <MonkeyPatch.setattr>` 来修补函数或属性以实现您所需的测试行为。这可以包括您自己的函数。使用 :py:meth:`monkeypatch.delattr <MonkeyPatch.delattr>` 在测试中删除该函数或属性。

        2. 修改字典的值，例如，您有一个全局配置，希望为某些测试用例进行修改。使用 :py:meth:`monkeypatch.setitem <MonkeyPatch.setitem>` 来修补字典以进行测试。可以使用 :py:meth:`monkeypatch.delitem <MonkeyPatch.delitem>` 来删除项目。

        3. 修改环境变量进行测试，例如，测试程序行为在缺少某个环境变量时，或者将多个值设置为已知变量。可以使用 :py:meth:`monkeypatch.setenv <MonkeyPatch.setenv>` 和 :py:meth:`monkeypatch.delenv <MonkeyPatch.delenv>` 进行这些修补。

        4. 使用 ``monkeypatch.setenv("PATH", value, prepend=os.pathsep)`` 来修改 ``$PATH``，并使用 :py:meth:`monkeypatch.chdir <MonkeyPatch.chdir>` 在测试期间更改当前工作目录的上下文。

        5. 使用 :py:meth:`monkeypatch.syspath_prepend <MonkeyPatch.syspath_prepend>` 来修改 ``sys.path``，这也将调用 ``pkg_resources.fixup_namespace_packages`` 和 :py:func:`importlib.invalidate_caches`。

        6. 使用 :py:meth:`monkeypatch.context <MonkeyPatch.context>` 仅在特定范围内应用修补，这可以帮助控制复杂fixture 或标准库的修补的拆卸。

        请参见 `monkeypatch blog post`_ 以获取一些介绍材料和其动机的讨论。

    .. tab:: 英文

        Sometimes tests need to invoke functionality which depends
        on global settings or which invokes code which cannot be easily
        tested such as network access.  The ``monkeypatch`` fixture
        helps you to safely set/delete an attribute, dictionary item or
        environment variable, or to modify ``sys.path`` for importing.

        The ``monkeypatch`` fixture provides these helper methods for safely patching and mocking
        functionality in tests:

        * :meth:`monkeypatch.setattr(obj, name, value, raising=True) <pytest.MonkeyPatch.setattr>`
        * :meth:`monkeypatch.delattr(obj, name, raising=True) <pytest.MonkeyPatch.delattr>`
        * :meth:`monkeypatch.setitem(mapping, name, value) <pytest.MonkeyPatch.setitem>`
        * :meth:`monkeypatch.delitem(obj, name, raising=True) <pytest.MonkeyPatch.delitem>`
        * :meth:`monkeypatch.setenv(name, value, prepend=None) <pytest.MonkeyPatch.setenv>`
        * :meth:`monkeypatch.delenv(name, raising=True) <pytest.MonkeyPatch.delenv>`
        * :meth:`monkeypatch.syspath_prepend(path) <pytest.MonkeyPatch.syspath_prepend>`
        * :meth:`monkeypatch.chdir(path) <pytest.MonkeyPatch.chdir>`
        * :meth:`monkeypatch.context() <pytest.MonkeyPatch.context>`


        All modifications will be undone after the requesting
        test function or fixture has finished. The ``raising``
        parameter determines if a ``KeyError`` or ``AttributeError``
        will be raised if the target of the set/deletion operation does not exist.

        Consider the following scenarios:

        1. Modifying the behavior of a function or the property of a class for a test e.g.
        there is an API call or database connection you will not make for a test but you know
        what the expected output should be. Use :py:meth:`monkeypatch.setattr <MonkeyPatch.setattr>` to patch the
        function or property with your desired testing behavior. This can include your own functions.
        Use :py:meth:`monkeypatch.delattr <MonkeyPatch.delattr>` to remove the function or property for the test.

        2. Modifying the values of dictionaries e.g. you have a global configuration that
        you want to modify for certain test cases. Use :py:meth:`monkeypatch.setitem <MonkeyPatch.setitem>` to patch the
        dictionary for the test. :py:meth:`monkeypatch.delitem <MonkeyPatch.delitem>` can be used to remove items.

        3. Modifying environment variables for a test e.g. to test program behavior if an
        environment variable is missing, or to set multiple values to a known variable.
        :py:meth:`monkeypatch.setenv <MonkeyPatch.setenv>` and :py:meth:`monkeypatch.delenv <MonkeyPatch.delenv>` can be used for
        these patches.

        4. Use ``monkeypatch.setenv("PATH", value, prepend=os.pathsep)`` to modify ``$PATH``, and
        :py:meth:`monkeypatch.chdir <MonkeyPatch.chdir>` to change the context of the current working directory
        during a test.

        5. Use :py:meth:`monkeypatch.syspath_prepend <MonkeyPatch.syspath_prepend>` to modify ``sys.path`` which will also
        call ``pkg_resources.fixup_namespace_packages`` and :py:func:`importlib.invalidate_caches`.

        6. Use :py:meth:`monkeypatch.context <MonkeyPatch.context>` to apply patches only in a specific scope, which can help
        control teardown of complex fixtures or patches to the stdlib.

        See the `monkeypatch blog post`_ for some introduction material
        and a discussion of its motivation.

.. _`monkeypatch blog post`: https://tetamap.wordpress.com//2009/03/03/monkeypatching-in-unit-tests-done-right/

猴子补丁函数
------------------------

**Monkeypatching functions**

.. tabs::

    .. tab:: 中文

        考虑一个场景，您正在处理用户目录。在测试的上下文中，您不希望测试依赖于运行用户。 ``monkeypatch`` 可以用来修补依赖于用户的函数，以始终返回一个特定值。

        在这个例子中，使用 :py:meth:`monkeypatch.setattr <MonkeyPatch.setattr>` 来修补 ``Path.home``，使得在运行测试时，总是使用已知的测试路径 ``Path("/abc")``。这消除了测试目的上对运行用户的任何依赖。必须在将要使用被修补函数的函数被调用之前调用 :py:meth:`monkeypatch.setattr <MonkeyPatch.setattr>`。测试函数完成后，``Path.home`` 的修改将被撤销。

        .. code-block:: python

            # contents of test_module.py with source code and the test
            from pathlib import Path


            def getssh():
                """简单函数返回扩展的主目录 ssh 路径。"""
                return Path.home() / ".ssh"


            def test_getssh(monkeypatch):
                # 模拟的返回函数来替换 Path.home
                # 始终返回 '/abc'
                def mockreturn():
                    return Path("/abc")

                # 应用 monkeypatch 来将 Path.home 替换为
                # 上面定义的 mockreturn 的行为。
                monkeypatch.setattr(Path, "home", mockreturn)

                # 调用 getssh() 将在此测试中使用 mockreturn
                # 替代 Path.home。
                x = getssh()
                assert x == Path("/abc/.ssh")

    .. tab:: 英文

        Consider a scenario where you are working with user directories. In the context of
        testing, you do not want your test to depend on the running user. ``monkeypatch``
        can be used to patch functions dependent on the user to always return a
        specific value.

        In this example, :py:meth:`monkeypatch.setattr <MonkeyPatch.setattr>` is used to patch ``Path.home``
        so that the known testing path ``Path("/abc")`` is always used when the test is run.
        This removes any dependency on the running user for testing purposes.
        :py:meth:`monkeypatch.setattr <MonkeyPatch.setattr>` must be called before the function which will use
        the patched function is called.
        After the test function finishes the ``Path.home`` modification will be undone.

        .. code-block:: python

            # contents of test_module.py with source code and the test
            from pathlib import Path


            def getssh():
                """Simple function to return expanded homedir ssh path."""
                return Path.home() / ".ssh"


            def test_getssh(monkeypatch):
                # mocked return function to replace Path.home
                # always return '/abc'
                def mockreturn():
                    return Path("/abc")

                # Application of the monkeypatch to replace Path.home
                # with the behavior of mockreturn defined above.
                monkeypatch.setattr(Path, "home", mockreturn)

                # Calling getssh() will use mockreturn in place of Path.home
                # for this test with the monkeypatch.
                x = getssh()
                assert x == Path("/abc/.ssh")

猴子补丁返回对象: 构建mock类
------------------------------------------------------

**Monkeypatching returned objects: building mock classes**

.. tabs::

    .. tab:: 中文

        :py:meth:`monkeypatch.setattr <MonkeyPatch.setattr>` 可以与类一起使用，以模拟函数返回的对象，而不是值。想象一个简单的函数，它接受一个 API URL 并返回 JSON 响应。

        .. code-block:: python

            # contents of app.py, a simple API retrieval example
            import requests


            def get_json(url):
                """接受一个 URL，并返回 JSON。"""
                r = requests.get(url)
                return r.json()

        我们需要模拟 ``r``，即返回的响应对象，以便于测试。对 ``r`` 的模拟需要一个 ``.json()`` 方法，返回一个字典。这可以在我们的测试文件中通过定义一个类来表示 ``r``。

        .. code-block:: python

            # contents of test_app.py, a simple test for our API retrieval
            # 为了进行 monkeypatching 导入 requests
            import requests

            # 包含 get_json() 函数的 app.py
            # 这是之前代码块的示例
            import app


            # 自定义类作为模拟返回值
            # 将覆盖 requests.get 返回的 requests.Response
            class MockResponse:
                # 模拟的 json() 方法始终返回一个特定的测试字典
                @staticmethod
                def json():
                    return {"mock_key": "mock_response"}


            def test_get_json(monkeypatch):
                # 任何参数都可以传入，mock_get() 将始终返回我们的
                # 模拟对象，该对象仅具有 .json() 方法。
                def mock_get(*args, **kwargs):
                    return MockResponse()

                # 将 monkeypatch 应用于 requests.get，替换为 mock_get
                monkeypatch.setattr(requests, "get", mock_get)

                # app.get_json，其中包含 requests.get，使用了 monkeypatch
                result = app.get_json("https://fakeurl")
                assert result["mock_key"] == "mock_response"


        ``monkeypatch`` 用我们的 ``mock_get`` 函数对 ``requests.get`` 进行了模拟。
        ``mock_get`` 函数返回 ``MockResponse`` 类的一个实例，该类定义了一个 ``json()`` 方法，以返回一个已知的测试字典，并且不需要任何外部 API 连接。

        您可以根据要测试的场景构建适当复杂度的 ``MockResponse`` 类。例如，它可以包含一个始终返回 ``True`` 的 ``ok`` 属性，或者根据输入字符串从模拟的 ``json()`` 方法返回不同的值。

        这个模拟可以通过 ``fixture`` 在多个测试之间共享：

        .. code-block:: python

            # contents of test_app.py, a simple test for our API retrieval
            import pytest
            import requests

            # 包含 get_json() 函数的 app.py
            import app


            # 自定义类作为 requests.get() 的模拟返回值
            class MockResponse:
                @staticmethod
                def json():
                    return {"mock_key": "mock_response"}


            # monkeypatched 的 requests.get 移动到 fixture
            @pytest.fixture
            def mock_response(monkeypatch):
                """Requests.get() 模拟返回 {'mock_key':'mock_response'}。"""

                def mock_get(*args, **kwargs):
                    return MockResponse()

                monkeypatch.setattr(requests, "get", mock_get)


            # 注意我们的测试使用了自定义 fixture，而不是直接使用 monkeypatch
            def test_get_json(mock_response):
                result = app.get_json("https://fakeurl")
                assert result["mock_key"] == "mock_response"


        此外，如果模拟被设计为应用于所有测试，则可以将 ``fixture`` 移动到 ``conftest.py`` 文件中，并使用 ``autouse=True`` 选项。

    .. tab:: 英文

        :py:meth:`monkeypatch.setattr <MonkeyPatch.setattr>` can be used in conjunction with classes to mock returned
        objects from functions instead of values.
        Imagine a simple function to take an API url and return the json response.

        .. code-block:: python

            # contents of app.py, a simple API retrieval example
            import requests


            def get_json(url):
                """Takes a URL, and returns the JSON."""
                r = requests.get(url)
                return r.json()

        We need to mock ``r``, the returned response object for testing purposes.
        The mock of ``r`` needs a ``.json()`` method which returns a dictionary.
        This can be done in our test file by defining a class to represent ``r``.

        .. code-block:: python

            # contents of test_app.py, a simple test for our API retrieval
            # import requests for the purposes of monkeypatching
            import requests

            # our app.py that includes the get_json() function
            # this is the previous code block example
            import app


            # custom class to be the mock return value
            # will override the requests.Response returned from requests.get
            class MockResponse:
                # mock json() method always returns a specific testing dictionary
                @staticmethod
                def json():
                    return {"mock_key": "mock_response"}


            def test_get_json(monkeypatch):
                # Any arguments may be passed and mock_get() will always return our
                # mocked object, which only has the .json() method.
                def mock_get(*args, **kwargs):
                    return MockResponse()

                # apply the monkeypatch for requests.get to mock_get
                monkeypatch.setattr(requests, "get", mock_get)

                # app.get_json, which contains requests.get, uses the monkeypatch
                result = app.get_json("https://fakeurl")
                assert result["mock_key"] == "mock_response"


        ``monkeypatch`` applies the mock for ``requests.get`` with our ``mock_get`` function.
        The ``mock_get`` function returns an instance of the ``MockResponse`` class, which
        has a ``json()`` method defined to return a known testing dictionary and does not
        require any outside API connection.

        You can build the ``MockResponse`` class with the appropriate degree of complexity for
        the scenario you are testing. For instance, it could include an ``ok`` property that
        always returns ``True``, or return different values from the ``json()`` mocked method
        based on input strings.

        This mock can be shared across tests using a ``fixture``:

        .. code-block:: python

            # contents of test_app.py, a simple test for our API retrieval
            import pytest
            import requests

            # app.py that includes the get_json() function
            import app


            # custom class to be the mock return value of requests.get()
            class MockResponse:
                @staticmethod
                def json():
                    return {"mock_key": "mock_response"}


            # monkeypatched requests.get moved to a fixture
            @pytest.fixture
            def mock_response(monkeypatch):
                """Requests.get() mocked to return {'mock_key':'mock_response'}."""

                def mock_get(*args, **kwargs):
                    return MockResponse()

                monkeypatch.setattr(requests, "get", mock_get)


            # notice our test uses the custom fixture instead of monkeypatch directly
            def test_get_json(mock_response):
                result = app.get_json("https://fakeurl")
                assert result["mock_key"] == "mock_response"


        Furthermore, if the mock was designed to be applied to all tests, the ``fixture`` could
        be moved to a ``conftest.py`` file and use the with ``autouse=True`` option.


全局补丁示例：阻止远程操作的“请求”
------------------------------------------------------------------

**Global patch example: preventing "requests" from remote operations**

.. tabs::

    .. tab:: 中文

        如果您想防止 "requests" 库在所有测试中执行 HTTP 请求，可以这样做：

        .. code-block:: python

            # contents of conftest.py
            import pytest


            @pytest.fixture(autouse=True)
            def no_requests(monkeypatch):
                """为所有测试移除 requests.sessions.Session.request。"""
                monkeypatch.delattr("requests.sessions.Session.request")

        这个自动使用的 fixture 将在每个测试函数中执行，它将删除方法 ``request.session.Session.request``，因此测试中任何尝试创建 HTTP 请求的操作都将失败。

        .. note::

            请注意，不建议修改内置函数，例如 ``open``、``compile`` 等，因为这可能会破坏 pytest 的内部功能。如果这是不可避免的，传递 ``--tb=native``、``--assert=plain`` 和 ``--capture=no`` 可能会有所帮助，但不能保证有效。

        .. note::

            请注意，修改 ``stdlib`` 函数和 pytest 使用的一些第三方库可能会破坏 pytest 本身，因此在这些情况下，建议使用 :meth:`MonkeyPatch.context` 将补丁限制在您想要测试的块中：

            .. code-block:: python

                import functools


                def test_partial(monkeypatch):
                    with monkeypatch.context() as m:
                        m.setattr(functools, "partial", 3)
                        assert functools.partial == 3

            有关详细信息，请参见 :issue:`3290`。

    .. tab:: 英文

        If you want to prevent the "requests" library from performing http
        requests in all your tests, you can do:

        .. code-block:: python

            # contents of conftest.py
            import pytest


            @pytest.fixture(autouse=True)
            def no_requests(monkeypatch):
                """Remove requests.sessions.Session.request for all tests."""
                monkeypatch.delattr("requests.sessions.Session.request")

        This autouse fixture will be executed for each test function and it
        will delete the method ``request.session.Session.request``
        so that any attempts within tests to create http requests will fail.


        .. note::

            Be advised that it is not recommended to patch builtin functions such as ``open``,
            ``compile``, etc., because it might break pytest's internals. If that's
            unavoidable, passing ``--tb=native``, ``--assert=plain`` and ``--capture=no`` might
            help although there's no guarantee.

        .. note::

            Mind that patching ``stdlib`` functions and some third-party libraries used by pytest
            might break pytest itself, therefore in those cases it is recommended to use
            :meth:`MonkeyPatch.context` to limit the patching to the block you want tested:

            .. code-block:: python

                import functools


                def test_partial(monkeypatch):
                    with monkeypatch.context() as m:
                        m.setattr(functools, "partial", 3)
                        assert functools.partial == 3

            See :issue:`3290` for details.


猴子补丁环境变量
------------------------------------

**Monkeypatching environment variables**

.. tabs::

    .. tab:: 中文

        如果您正在处理环境变量，通常需要安全地更改值或从系统中删除它们以进行测试。``monkeypatch`` 提供了一种机制来实现这一点，使用 ``setenv`` 和 ``delenv`` 方法。我们的示例代码如下：

        .. code-block:: python

            # contents of our original code file e.g. code.py
            import os


            def get_os_user_lower():
                """简单的检索函数。
                返回小写的 USER，或引发 OSError。"""
                username = os.getenv("USER")

                if username is None:
                    raise OSError("USER 环境变量未设置。")

                return username.lower()

        这里有两个潜在的路径。首先，``USER`` 环境变量被设置为一个值。其次，``USER`` 环境变量不存在。使用 ``monkeypatch`` 可以安全地测试这两条路径，而不会影响运行环境：

        .. code-block:: python

            # contents of our test file e.g. test_code.py
            import pytest


            def test_upper_to_lower(monkeypatch):
                """设置 USER 环境变量以断言行为。"""
                monkeypatch.setenv("USER", "TestingUser")
                assert get_os_user_lower() == "testinguser"


            def test_raise_exception(monkeypatch):
                """移除 USER 环境变量并断言引发 OSError。"""
                monkeypatch.delenv("USER", raising=False)

                with pytest.raises(OSError):
                    _ = get_os_user_lower()

        这种行为可以移动到 ``fixture`` 结构中，并在测试之间共享：

        .. code-block:: python

            # contents of our test file e.g. test_code.py
            import pytest


            @pytest.fixture
            def mock_env_user(monkeypatch):
                monkeypatch.setenv("USER", "TestingUser")


            @pytest.fixture
            def mock_env_missing(monkeypatch):
                monkeypatch.delenv("USER", raising=False)


            # 注意测试引用了用于模拟的 fixtures
            def test_upper_to_lower(mock_env_user):
                assert get_os_user_lower() == "testinguser"


            def test_raise_exception(mock_env_missing):
                with pytest.raises(OSError):
                    _ = get_os_user_lower()

    .. tab:: 英文

        If you are working with environment variables you often need to safely change the values
        or delete them from the system for testing purposes. ``monkeypatch`` provides a mechanism
        to do this using the ``setenv`` and ``delenv`` method. Our example code to test:

        .. code-block:: python

            # contents of our original code file e.g. code.py
            import os


            def get_os_user_lower():
                """Simple retrieval function.
                Returns lowercase USER or raises OSError."""
                username = os.getenv("USER")

                if username is None:
                    raise OSError("USER environment is not set.")

                return username.lower()

        There are two potential paths. First, the ``USER`` environment variable is set to a
        value. Second, the ``USER`` environment variable does not exist. Using ``monkeypatch``
        both paths can be safely tested without impacting the running environment:

        .. code-block:: python

            # contents of our test file e.g. test_code.py
            import pytest


            def test_upper_to_lower(monkeypatch):
                """Set the USER env var to assert the behavior."""
                monkeypatch.setenv("USER", "TestingUser")
                assert get_os_user_lower() == "testinguser"


            def test_raise_exception(monkeypatch):
                """Remove the USER env var and assert OSError is raised."""
                monkeypatch.delenv("USER", raising=False)

                with pytest.raises(OSError):
                    _ = get_os_user_lower()

        This behavior can be moved into ``fixture`` structures and shared across tests:

        .. code-block:: python

            # contents of our test file e.g. test_code.py
            import pytest


            @pytest.fixture
            def mock_env_user(monkeypatch):
                monkeypatch.setenv("USER", "TestingUser")


            @pytest.fixture
            def mock_env_missing(monkeypatch):
                monkeypatch.delenv("USER", raising=False)


            # notice the tests reference the fixtures for mocks
            def test_upper_to_lower(mock_env_user):
                assert get_os_user_lower() == "testinguser"


            def test_raise_exception(mock_env_missing):
                with pytest.raises(OSError):
                    _ = get_os_user_lower()


猴子补丁参考
---------------------------

**Monkeypatching dictionaries**

.. tabs::

    .. tab:: 中文

        :py:meth:`monkeypatch.setitem <MonkeyPatch.setitem>` 可以在测试期间安全地设置字典的值为特定值。以下是一个简化的连接字符串示例：

        .. code-block:: python

            # contents of app.py to generate a simple connection string
            DEFAULT_CONFIG = {"user": "user1", "database": "db1"}


            def create_connection_string(config=None):
                """根据输入或默认值创建连接字符串。"""
                config = config or DEFAULT_CONFIG
                return f"User Id={config['user']}; Location={config['database']};"

        出于测试目的，我们可以将 ``DEFAULT_CONFIG`` 字典打补丁，设置为特定值。

        .. code-block:: python

            # contents of test_app.py
            # app.py with the connection string function (prior code block)
            import app


            def test_connection(monkeypatch):
                # 将 DEFAULT_CONFIG 的值打补丁为特定的
                # 测试值，仅在此测试中有效。
                monkeypatch.setitem(app.DEFAULT_CONFIG, "user", "test_user")
                monkeypatch.setitem(app.DEFAULT_CONFIG, "database", "test_db")

                # 基于模拟的预期结果
                expected = "User Id=test_user; Location=test_db;"

                # 测试使用了猴子补丁的字典设置
                result = app.create_connection_string()
                assert result == expected

        您可以使用 :py:meth:`monkeypatch.delitem <MonkeyPatch.delitem>` 来删除值。

        .. code-block:: python

            # contents of test_app.py
            import pytest

            # app.py with the connection string function
            import app


            def test_missing_user(monkeypatch):
                # 打补丁使 DEFAULT_CONFIG 缺少 'user' 键
                monkeypatch.delitem(app.DEFAULT_CONFIG, "user", raising=False)

                # 预期引发 KeyError，因为未传递配置，且
                # 默认值现在缺少 'user' 条目。
                with pytest.raises(KeyError):
                    _ = app.create_connection_string()


        fixture 的模块化为您提供了定义
        每个潜在模拟的独立 fixture 的灵活性，并在所需的测试中引用它们。

        .. code-block:: python

            # contents of test_app.py
            import pytest

            # app.py with the connection string function
            import app


            # 所有模拟均移入独立的 fixture 中
            @pytest.fixture
            def mock_test_user(monkeypatch):
                """将 DEFAULT_CONFIG 的用户设置为 test_user。"""
                monkeypatch.setitem(app.DEFAULT_CONFIG, "user", "test_user")


            @pytest.fixture
            def mock_test_database(monkeypatch):
                """将 DEFAULT_CONFIG 的数据库设置为 test_db。"""
                monkeypatch.setitem(app.DEFAULT_CONFIG, "database", "test_db")


            @pytest.fixture
            def mock_missing_default_user(monkeypatch):
                """从 DEFAULT_CONFIG 中移除用户键。"""
                monkeypatch.delitem(app.DEFAULT_CONFIG, "user", raising=False)


            # 测试仅引用所需的 fixture 模拟
            def test_connection(mock_test_user, mock_test_database):
                expected = "User Id=test_user; Location=test_db;"

                result = app.create_connection_string()
                assert result == expected


            def test_missing_user(mock_missing_default_user):
                with pytest.raises(KeyError):
                    _ = app.create_connection_string()

    .. tab:: 英文

        :py:meth:`monkeypatch.setitem <MonkeyPatch.setitem>` can be used to safely set the values of dictionaries
        to specific values during tests. Take this simplified connection string example:

        .. code-block:: python

            # contents of app.py to generate a simple connection string
            DEFAULT_CONFIG = {"user": "user1", "database": "db1"}


            def create_connection_string(config=None):
                """Creates a connection string from input or defaults."""
                config = config or DEFAULT_CONFIG
                return f"User Id={config['user']}; Location={config['database']};"

        For testing purposes we can patch the ``DEFAULT_CONFIG`` dictionary to specific values.

        .. code-block:: python

            # contents of test_app.py
            # app.py with the connection string function (prior code block)
            import app


            def test_connection(monkeypatch):
                # Patch the values of DEFAULT_CONFIG to specific
                # testing values only for this test.
                monkeypatch.setitem(app.DEFAULT_CONFIG, "user", "test_user")
                monkeypatch.setitem(app.DEFAULT_CONFIG, "database", "test_db")

                # expected result based on the mocks
                expected = "User Id=test_user; Location=test_db;"

                # the test uses the monkeypatched dictionary settings
                result = app.create_connection_string()
                assert result == expected

        You can use the :py:meth:`monkeypatch.delitem <MonkeyPatch.delitem>` to remove values.

        .. code-block:: python

            # contents of test_app.py
            import pytest

            # app.py with the connection string function
            import app


            def test_missing_user(monkeypatch):
                # patch the DEFAULT_CONFIG t be missing the 'user' key
                monkeypatch.delitem(app.DEFAULT_CONFIG, "user", raising=False)

                # Key error expected because a config is not passed, and the
                # default is now missing the 'user' entry.
                with pytest.raises(KeyError):
                    _ = app.create_connection_string()


        The modularity of fixtures gives you the flexibility to define
        separate fixtures for each potential mock and reference them in the needed tests.

        .. code-block:: python

            # contents of test_app.py
            import pytest

            # app.py with the connection string function
            import app


            # all of the mocks are moved into separated fixtures
            @pytest.fixture
            def mock_test_user(monkeypatch):
                """Set the DEFAULT_CONFIG user to test_user."""
                monkeypatch.setitem(app.DEFAULT_CONFIG, "user", "test_user")


            @pytest.fixture
            def mock_test_database(monkeypatch):
                """Set the DEFAULT_CONFIG database to test_db."""
                monkeypatch.setitem(app.DEFAULT_CONFIG, "database", "test_db")


            @pytest.fixture
            def mock_missing_default_user(monkeypatch):
                """Remove the user key from DEFAULT_CONFIG"""
                monkeypatch.delitem(app.DEFAULT_CONFIG, "user", raising=False)


            # tests reference only the fixture mocks that are needed
            def test_connection(mock_test_user, mock_test_database):
                expected = "User Id=test_user; Location=test_db;"

                result = app.create_connection_string()
                assert result == expected


            def test_missing_user(mock_missing_default_user):
                with pytest.raises(KeyError):
                    _ = app.create_connection_string()


.. currentmodule:: pytest

API 参考
-------------

**API Reference**

.. tabs::

    .. tab:: 中文

        查阅 :class:`MonkeyPatch` 类的文档。

    .. tab:: 英文

        Consult the docs for the :class:`MonkeyPatch` class.
