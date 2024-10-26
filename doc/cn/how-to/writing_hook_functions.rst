.. _`writinghooks`:

编写钩子函数
======================

**Writing hook functions**

.. _validation:

钩子函数验证和执行
--------------------------------------

**hook function validation and execution**

.. tabs::

    .. tab:: 中文

        pytest 会根据给定的钩子规范调用注册插件中的钩子函数。让我们看看一个典型的钩子函数 ``pytest_collection_modifyitems(session, config, items)`` ，pytest 会在所有测试项收集完成后调用它。

        当我们在插件中实现 ``pytest_collection_modifyitems`` 函数时，pytest 会在注册过程中验证您使用的参数名称是否与规范匹配，如果不匹配则会退出。

        让我们看看一个可能的实现：

        .. code-block:: python

            def pytest_collection_modifyitems(config, items):
                # 收集完成后调用
                # 您可以修改 ``items`` 列表
                ...

        在这里， ``pytest`` 会传入 ``config`` （pytest 配置对象）和 ``items`` （收集到的测试项列表），但不会传入 ``session`` 参数，因为我们没有在函数签名中列出它。这种动态的“修剪”参数的方式使得 ``pytest`` 可以保持“未来兼容性”：我们可以引入新的钩子命名参数，而不会破坏现有钩子实现的签名。这是 pytest 插件具有长期兼容性的一大原因。

        请注意，除了 ``pytest_runtest_*`` 以外的钩子函数不允许抛出异常。这样做将会中断 pytest 的运行。

    .. tab:: 英文

        pytest calls hook functions from registered plugins for any
        given hook specification.  Let's look at a typical hook function
        for the ``pytest_collection_modifyitems(session, config,
        items)`` hook which pytest calls after collection of all test items is
        completed.

        When we implement a ``pytest_collection_modifyitems`` function in our plugin
        pytest will during registration verify that you use argument
        names which match the specification and bail out if not.

        Let's look at a possible implementation:

        .. code-block:: python

            def pytest_collection_modifyitems(config, items):
                # called after collection is completed
                # you can modify the ``items`` list
                ...

        Here, ``pytest`` will pass in ``config`` (the pytest config object)
        and ``items`` (the list of collected test items) but will not pass
        in the ``session`` argument because we didn't list it in the function
        signature.  This dynamic "pruning" of arguments allows ``pytest`` to
        be "future-compatible": we can introduce new hook named parameters without
        breaking the signatures of existing hook implementations.  It is one of
        the reasons for the general long-lived compatibility of pytest plugins.

        Note that hook functions other than ``pytest_runtest_*`` are not
        allowed to raise exceptions.  Doing so will break the pytest run.



.. _firstresult:

firstresult: 在第一个非 None 结果处停止
-------------------------------------------

**firstresult: stop at first non-None result**

.. tabs::

    .. tab:: 中文

        对 ``pytest`` 钩子的绝大多数调用会返回一个 **结果列表**，该列表包含所有被调用的钩子函数的非 None 结果。

        一些钩子规范使用了 ``firstresult=True`` 选项，这样钩子调用只会执行到 N 个注册函数中第一个返回非 None 结果的函数，此结果将被视为整体钩子调用的结果。在这种情况下，剩余的钩子函数将不会被调用。

    .. tab:: 英文

        Most calls to ``pytest`` hooks result in a **list of results** which contains
        all non-None results of the called hook functions.

        Some hook specifications use the ``firstresult=True`` option so that the hook
        call only executes until the first of N registered functions returns a
        non-None result which is then taken as result of the overall hook call.
        The remaining hook functions will not be called in this case.

.. _`hookwrapper`:

钩子包装器：围绕其他钩子执行
-------------------------------------------------

**hook wrappers: executing around other hooks**

.. tabs::

    .. tab:: 中文

        pytest 插件可以实现钩子包装器，这些包装器包裹其他钩子实现的执行。钩子包装器是一个生成器函数，它会精确地产生一次。当 pytest 调用钩子时，它会首先执行钩子包装器，并将与常规钩子相同的参数传递给它们。

        在钩子包装器的 yield 点，pytest 会执行下一个钩子实现并将它们的结果返回到 yield 点，或者如果它们引发了异常，则传播该异常。

        以下是钩子包装器的示例定义：

        .. code-block:: python

            import pytest


            @pytest.hookimpl(wrapper=True)
            def pytest_pyfunc_call(pyfuncitem):
                do_something_before_next_hook_executes()

                # 如果结果是异常，将引发该异常。
                res = yield

                new_res = post_process_result(res)

                # 将返回值覆盖到插件系统。
                return new_res

        钩子包装器需要返回钩子的结果，或者引发异常。

        在许多情况下，包装器只需要在实际的钩子实现周围执行跟踪或其他副作用，在这种情况下，它可以返回 ``yield`` 的结果值。最简单（尽管无用）的钩子包装器是 ``return (yield)``。

        在其他情况下，包装器希望调整或适应结果，在这种情况下它可以返回一个新值。如果底层钩子的结果是一个可变对象，包装器可以修改该结果，但最好避免这样做。

        如果钩子实现由于异常而失败，包装器可以使用 ``try-catch-finally`` 包裹 ``yield`` 来处理该异常，可以选择传播它、抑制它，或者引发一个完全不同的异常。

        有关更多信息，请参阅 :ref:`pluggy 文档中的钩子包装器 <pluggy:hookwrappers>`。

    .. tab:: 英文

        pytest plugins can implement hook wrappers which wrap the execution
        of other hook implementations.  A hook wrapper is a generator function
        which yields exactly once. When pytest invokes hooks it first executes
        hook wrappers and passes the same arguments as to the regular hooks.

        At the yield point of the hook wrapper pytest will execute the next hook
        implementations and return their result to the yield point, or will
        propagate an exception if they raised.

        Here is an example definition of a hook wrapper:

        .. code-block:: python

            import pytest


            @pytest.hookimpl(wrapper=True)
            def pytest_pyfunc_call(pyfuncitem):
                do_something_before_next_hook_executes()

                # If the outcome is an exception, will raise the exception.
                res = yield

                new_res = post_process_result(res)

                # Override the return value to the plugin system.
                return new_res

        The hook wrapper needs to return a result for the hook, or raise an exception.

        In many cases, the wrapper only needs to perform tracing or other side effects
        around the actual hook implementations, in which case it can return the result
        value of the ``yield``. The simplest (though useless) hook wrapper is
        ``return (yield)``.

        In other cases, the wrapper wants the adjust or adapt the result, in which case
        it can return a new value. If the result of the underlying hook is a mutable
        object, the wrapper may modify that result, but it's probably better to avoid it.

        If the hook implementation failed with an exception, the wrapper can handle that
        exception using a ``try-catch-finally`` around the ``yield``, by propagating it,
        suppressing it, or raising a different exception entirely.

        For more information, consult the
        :ref:`pluggy documentation about hook wrappers <pluggy:hookwrappers>`.

.. _plugin-hookorder:

钩子函数排序/调用示例
-------------------------------------

**Hook function ordering / call example**

.. tabs::

    .. tab:: 中文

        对于任何给定的钩子规范，可能有多个实现，因此我们通常将 ``hook`` 执行视为 ``1:N`` 函数调用，其中 ``N`` 是注册函数的数量。有多种方法可以影响钩子实现的执行顺序，即在 ``N`` 大小的函数列表中的位置：

        .. code-block:: python

            # 插件 1
            @pytest.hookimpl(tryfirst=True)
            def pytest_collection_modifyitems(items):
                # 尽可能早地执行
                ...


            # 插件 2
            @pytest.hookimpl(trylast=True)
            def pytest_collection_modifyitems(items):
                # 尽可能晚地执行
                ...


            # 插件 3
            @pytest.hookimpl(wrapper=True)
            def pytest_collection_modifyitems(items):
                # 甚至会在上面的 tryfirst 之前执行！
                try:
                    return (yield)
                finally:
                    # 在所有非包装器执行后执行
                    ...

        以下是执行顺序：

        1. 插件 3 的 pytest_collection_modifyitems 被调用直到 yield 点，因为它是一个钩子包装器。

        2. 插件 1 的 pytest_collection_modifyitems 被调用，因为它被标记为 ``tryfirst=True``。

        3. 插件 2 的 pytest_collection_modifyitems 被调用，因为它被标记为 ``trylast=True`` （即使没有这个标记，它也会在插件 1 之后执行）。

        4. 插件 3 的 pytest_collection_modifyitems 然后执行 yield 点后的代码。yield 接收来自调用非包装器的结果，或者如果非包装器引发异常，则引发该异常。

        也可以在钩子包装器上使用 ``tryfirst`` 和 ``trylast``，在这种情况下，它将影响钩子包装器之间的排序。

    .. tab:: 英文

        For any given hook specification there may be more than one
        implementation and we thus generally view ``hook`` execution as a
        ``1:N`` function call where ``N`` is the number of registered functions.
        There are ways to influence if a hook implementation comes before or
        after others, i.e.  the position in the ``N``-sized list of functions:

        .. code-block:: python

            # Plugin 1
            @pytest.hookimpl(tryfirst=True)
            def pytest_collection_modifyitems(items):
                # will execute as early as possible
                ...


            # Plugin 2
            @pytest.hookimpl(trylast=True)
            def pytest_collection_modifyitems(items):
                # will execute as late as possible
                ...


            # Plugin 3
            @pytest.hookimpl(wrapper=True)
            def pytest_collection_modifyitems(items):
                # will execute even before the tryfirst one above!
                try:
                    return (yield)
                finally:
                    # will execute after all non-wrappers executed
                    ...

        Here is the order of execution:

        1. Plugin3's pytest_collection_modifyitems called until the yield point
        because it is a hook wrapper.

        2. Plugin1's pytest_collection_modifyitems is called because it is marked
        with ``tryfirst=True``.

        3. Plugin2's pytest_collection_modifyitems is called because it is marked
        with ``trylast=True`` (but even without this mark it would come after
        Plugin1).

        4. Plugin3's pytest_collection_modifyitems then executing the code after the yield
        point.  The yield receives the result from calling the non-wrappers, or raises
        an exception if the non-wrappers raised.

        It's possible to use ``tryfirst`` and ``trylast`` also on hook wrappers
        in which case it will influence the ordering of hook wrappers among each other.

.. _`declaringhooks`:

声明新钩子
------------------------

**Declaring new hooks**

.. tabs::

    .. tab:: 中文

        .. note::

            这是关于如何添加新钩子以及它们一般工作原理的快速概述，但更完整的概述可以在 `pluggy 文档 <https://pluggy.readthedocs.io/en/latest/>`__ 中找到。

        插件和 ``conftest.py`` 文件可以声明新的钩子，这些钩子可以由其他插件实现，以改变行为或与新插件交互：

        .. autofunction:: _pytest.hookspec.pytest_addhooks
            :noindex:

        钩子通常声明为不执行任何操作的函数，仅包含文档描述钩子何时被调用以及预期返回值是什么。函数的名称必须以 `pytest_` 开头，否则 pytest 将无法识别它们。

        以下是一个示例。假设此代码位于 ``sample_hook.py`` 模块中。

        .. code-block:: python

            def pytest_my_hook(config):
                """
                接收 pytest 配置并对其进行处理
                """

        要将钩子注册到 pytest，它们需要在自己的模块或类中进行结构化。然后可以使用 ``pytest_addhooks`` 函数（它本身是 pytest 暴露的钩子）将此类或模块传递给 ``pluginmanager``。

        .. code-block:: python

            def pytest_addhooks(pluginmanager):
                """这个示例假设钩子被分组在 'sample_hook' 模块中。"""
                from my_app.tests import sample_hook

                pluginmanager.add_hookspecs(sample_hook)

        有关现实世界示例，请参见来自 `xdist <https://github.com/pytest-dev/pytest-xdist>`_ 的 `newhooks.py`_。

        .. _`newhooks.py`: https://github.com/pytest-dev/pytest-xdist/blob/974bd566c599dc6a9ea291838c6f226197208b46/xdist/newhooks.py

        钩子可以从夹具或其他钩子中调用。在这两种情况下，钩子都是通过 ``config`` 对象中可用的 ``hook`` 对象调用的。大多数钩子直接接收 ``config`` 对象，而夹具可以使用提供相同对象的 ``pytestconfig`` 夹具。

        .. code-block:: python

            @pytest.fixture()
            def my_fixture(pytestconfig):
                # 调用名为 "pytest_my_hook" 的钩子
                # 'result' 将是所有注册函数的返回值列表。
                result = pytestconfig.hook.pytest_my_hook(config=pytestconfig)

        .. note::
            钩子仅使用关键字参数接收参数。

        现在您的钩子已准备好使用。要在钩子处注册函数，其他插件或用户现在只需在其 ``conftest.py`` 中定义具有正确签名的函数 ``pytest_my_hook``。

        示例：

        .. code-block:: python

            def pytest_my_hook(config):
                """
                将所有活动钩子打印到屏幕上。
                """
                print(config.hook)

    .. tab:: 英文

        .. note::

            This is a quick overview on how to add new hooks and how they work in general, but a more complete
            overview can be found in `the pluggy documentation <https://pluggy.readthedocs.io/en/latest/>`__.

        Plugins and ``conftest.py`` files may declare new hooks that can then be
        implemented by other plugins in order to alter behaviour or interact with
        the new plugin:

        .. autofunction:: _pytest.hookspec.pytest_addhooks
            :noindex:

        Hooks are usually declared as do-nothing functions that contain only
        documentation describing when the hook will be called and what return values
        are expected. The names of the functions must start with `pytest_` otherwise pytest won't recognize them.

        Here's an example. Let's assume this code is in the ``sample_hook.py`` module.

        .. code-block:: python

            def pytest_my_hook(config):
                """
                Receives the pytest config and does things with it
                """

        To register the hooks with pytest they need to be structured in their own module or class. This
        class or module can then be passed to the ``pluginmanager`` using the ``pytest_addhooks`` function
        (which itself is a hook exposed by pytest).

        .. code-block:: python

            def pytest_addhooks(pluginmanager):
                """This example assumes the hooks are grouped in the 'sample_hook' module."""
                from my_app.tests import sample_hook

                pluginmanager.add_hookspecs(sample_hook)

        For a real world example, see `newhooks.py`_ from `xdist <https://github.com/pytest-dev/pytest-xdist>`_.

        .. _`newhooks.py`: https://github.com/pytest-dev/pytest-xdist/blob/974bd566c599dc6a9ea291838c6f226197208b46/xdist/newhooks.py

        Hooks may be called both from fixtures or from other hooks. In both cases, hooks are called
        through the ``hook`` object, available in the ``config`` object. Most hooks receive a
        ``config`` object directly, while fixtures may use the ``pytestconfig`` fixture which provides the same object.

        .. code-block:: python

            @pytest.fixture()
            def my_fixture(pytestconfig):
                # call the hook called "pytest_my_hook"
                # 'result' will be a list of return values from all registered functions.
                result = pytestconfig.hook.pytest_my_hook(config=pytestconfig)

        .. note::
            Hooks receive parameters using only keyword arguments.

        Now your hook is ready to be used. To register a function at the hook, other plugins or users must
        now simply define the function ``pytest_my_hook`` with the correct signature in their ``conftest.py``.

        Example:

        .. code-block:: python

            def pytest_my_hook(config):
                """
                Print all active hooks to the screen.
                """
                print(config.hook)


.. _`addoptionhooks`:


在 pytest_addoption 中使用钩子
-------------------------------

**Using hooks in pytest_addoption**

.. tabs::

    .. tab:: 中文

        偶尔，需要根据另一个插件中的钩子更改一个插件定义命令行选项的方式。例如，一个插件可能会暴露一个命令行选项，而另一个插件需要为该选项定义默认值。可以使用 pluginmanager 来安装和使用钩子来实现这一点。插件将定义和添加钩子，并使用 pytest_addoption，如下所示：

        .. code-block:: python

            # hooks.py 的内容


            # 使用 firstresult=True，因为我们只希望一个插件定义这个
            # 默认值
            @hookspec(firstresult=True)
            def pytest_config_file_default_value():
                """返回配置文件命令行选项的默认值."""


            # myplugin.py 的内容


            def pytest_addhooks(pluginmanager):
                """这个示例假设钩子被分组在 'hooks' 模块中。"""
                from . import hooks

                pluginmanager.add_hookspecs(hooks)


            def pytest_addoption(parser, pluginmanager):
                default_value = pluginmanager.hook.pytest_config_file_default_value()
                parser.addoption(
                    "--config-file",
                    help="要使用的配置文件，默认值为 %(default)s",
                    default=default_value,
                )

        使用 myplugin 的 conftest.py 只需简单地定义钩子，如下所示：

        .. code-block:: python

            def pytest_config_file_default_value():
                return "config.yaml"

    .. tab:: 英文

        Occasionally, it is necessary to change the way in which command line options
        are defined by one plugin based on hooks in another plugin. For example,
        a plugin may expose a command line option for which another plugin needs
        to define the default value. The pluginmanager can be used to install and
        use hooks to accomplish this. The plugin would define and add the hooks
        and use pytest_addoption as follows:

        .. code-block:: python

        # contents of hooks.py


        # Use firstresult=True because we only want one plugin to define this
        # default value
        @hookspec(firstresult=True)
        def pytest_config_file_default_value():
            """Return the default value for the config file command line option."""


        # contents of myplugin.py


        def pytest_addhooks(pluginmanager):
            """This example assumes the hooks are grouped in the 'hooks' module."""
            from . import hooks

            pluginmanager.add_hookspecs(hooks)


        def pytest_addoption(parser, pluginmanager):
            default_value = pluginmanager.hook.pytest_config_file_default_value()
            parser.addoption(
                "--config-file",
                help="Config file to use, defaults to %(default)s",
                default=default_value,
            )

        The conftest.py that is using myplugin would simply define the hook as follows:

        .. code-block:: python

            def pytest_config_file_default_value():
                return "config.yaml"


可选择使用来自第三方插件的钩子
---------------------------------------------

**Optionally using hooks from 3rd party plugins**

.. tabs::

    .. tab:: 中文

        如上所述，使用插件的新钩子可能会有点棘手，因为标准的 :ref:`验证机制 <validation>`：如果您依赖于未安装的插件，验证将失败，错误消息对您的用户来说并没有多大意义。

        一种方法是将钩子实现推迟到一个新插件，而不是直接在您的插件模块中声明钩子函数，例如：

        .. code-block:: python

            # myplugin.py 的内容


            class DeferPlugin:
                """简单插件，用于推迟 pytest-xdist 钩子函数。"""

                def pytest_testnodedown(self, node, error):
                    """标准 xdist 钩子函数。"""


            def pytest_configure(config):
                if config.pluginmanager.hasplugin("xdist"):
                    config.pluginmanager.register(DeferPlugin())

        这样做的额外好处是允许您根据安装的插件有条件地安装钩子。

    .. tab:: 英文

        Using new hooks from plugins as explained above might be a little tricky
        because of the standard :ref:`validation mechanism <validation>`:
        if you depend on a plugin that is not installed, validation will fail and
        the error message will not make much sense to your users.

        One approach is to defer the hook implementation to a new plugin instead of
        declaring the hook functions directly in your plugin module, for example:

        .. code-block:: python

            # contents of myplugin.py


            class DeferPlugin:
                """Simple plugin to defer pytest-xdist hook functions."""

                def pytest_testnodedown(self, node, error):
                    """standard xdist hook function."""


            def pytest_configure(config):
                if config.pluginmanager.hasplugin("xdist"):
                    config.pluginmanager.register(DeferPlugin())

        This has the added benefit of allowing you to conditionally install hooks
        depending on which plugins are installed.

.. _plugin-stash:

跨钩子函数存储项目数据
-------------------------------------------

**Storing data on items across hook functions**

.. tabs::

    .. tab:: 中文

        插件通常需要在一个钩子实现中存储有关 :class:`~pytest.Item` 的数据，并在另一个钩子实现中访问这些数据。一种常见的解决方案是直接在项上分配某个私有属性，但类型检查器如 mypy 对此表示反对，这也可能导致与其他插件的冲突。因此，pytest 提供了一种更好的方法来实现这个功能，即 :attr:`item.stash <_pytest.nodes.Node.stash>` 。

        要在您的插件中使用“stash”，首先在插件的顶层创建“stash keys”：

        .. code-block:: python

            been_there_key = pytest.StashKey[bool]()
            done_that_key = pytest.StashKey[str]()

        然后在某个时刻使用这些键来存储您的数据：

        .. code-block:: python

            def pytest_runtest_setup(item: pytest.Item) -> None:
                item.stash[been_there_key] = True
                item.stash[done_that_key] = "no"

        在另一个时刻检索它们：

        .. code-block:: python

            def pytest_runtest_teardown(item: pytest.Item) -> None:
                if not item.stash[been_there_key]:
                    print("哦？")
                item.stash[done_that_key] = "yes!"

        如果需要，所有节点类型（如 :class:`~pytest.Class` 、:class:`~pytest.Session`）以及 :class:`~pytest.Config` 上都可以使用 stash。

    .. tab:: 英文

        Plugins often need to store data on :class:`~pytest.Item`\s in one hook
        implementation, and access it in another. One common solution is to just
        assign some private attribute directly on the item, but type-checkers like
        mypy frown upon this, and it may also cause conflicts with other plugins.
        So pytest offers a better way to do this, :attr:`item.stash <_pytest.nodes.Node.stash>`.

        To use the "stash" in your plugins, first create "stash keys" somewhere at the
        top level of your plugin:

        .. code-block:: python

            been_there_key = pytest.StashKey[bool]()
            done_that_key = pytest.StashKey[str]()

        then use the keys to stash your data at some point:

        .. code-block:: python

            def pytest_runtest_setup(item: pytest.Item) -> None:
                item.stash[been_there_key] = True
                item.stash[done_that_key] = "no"

        and retrieve them at another point:

        .. code-block:: python

            def pytest_runtest_teardown(item: pytest.Item) -> None:
                if not item.stash[been_there_key]:
                    print("Oh?")
                item.stash[done_that_key] = "yes!"

        Stashes are available on all node types (like :class:`~pytest.Class`,
        :class:`~pytest.Session`) and also on :class:`~pytest.Config`, if needed.
