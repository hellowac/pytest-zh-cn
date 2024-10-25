.. _mark:

如何用属性标记测试函数
===========================================

**How to mark test functions with attributes**

.. tabs::

    .. tab:: 中文

        通过使用 ``pytest.mark`` 帮助器，您可以轻松地为测试函数设置元数据。  
        您可以在 :ref:`API Reference<marks ref>` 中找到内置标记的完整列表。  
        或者，您可以使用 CLI 列出所有标记，包括内置和自定义标记，使用命令 - :code:`pytest --markers`。

        以下是一些内置标记：

        * :ref:`usefixtures <usefixtures>` - 在测试函数或类上使用 fixtures
        * :ref:`filterwarnings <filterwarnings>` - 过滤测试函数的某些警告
        * :ref:`skip <skip>` - 始终跳过测试函数
        * :ref:`skipif <skipif>` - 如果满足某个条件，则跳过测试函数
        * :ref:`xfail <xfail>` - 如果满足某个条件，产生“预期失败”的结果
        * :ref:`parametrize <parametrizemark>` - 对同一个测试函数进行多次调用。

        创建自定义标记或将标记应用于整个测试类或模块都很简单。  
        这些标记可以被插件使用，并且通常用于在命令行中使用 ``-m`` 选项选择测试 :ref:`select tests <mark run>`。

        请参见 :ref:`mark examples`，其中包含示例，这些示例也作为文档。

        .. note::

            标记只能应用于测试，对 :ref:`fixtures <fixtures>` 没有影响。

    .. tab:: 英文

        By using the ``pytest.mark`` helper you can easily set
        metadata on your test functions. You can find the full list of builtin markers
        in the :ref:`API Reference<marks ref>`. Or you can list all the markers, including
        builtin and custom, using the CLI - :code:`pytest --markers`.

        Here are some of the builtin markers:

        * :ref:`usefixtures <usefixtures>` - use fixtures on a test function or class
        * :ref:`filterwarnings <filterwarnings>` - filter certain warnings of a test function
        * :ref:`skip <skip>` - always skip a test function
        * :ref:`skipif <skipif>` - skip a test function if a certain condition is met
        * :ref:`xfail <xfail>` - produce an "expected failure" outcome if a certain
        condition is met
        * :ref:`parametrize <parametrizemark>` - perform multiple calls
        to the same test function.

        It's easy to create custom markers or to apply markers
        to whole test classes or modules. Those markers can be used by plugins, and also
        are commonly used to :ref:`select tests <mark run>` on the command-line with the ``-m`` option.

        See :ref:`mark examples` for examples which also serve as documentation.

        .. note::

            Marks can only be applied to tests, having no effect on
            :ref:`fixtures <fixtures>`.


注册标记
-----------------

**Registering marks**

.. tabs::

    .. tab:: 中文

        您可以在 ``pytest.ini`` 文件中注册自定义标记，如下所示：

        .. code-block:: ini

            [pytest]
            markers =
                slow: 将测试标记为慢（使用 '-m "not slow"' 取消选择）
                serial

        或者在 ``pyproject.toml`` 文件中，如下所示：

        .. code-block:: toml

            [tool.pytest.ini_options]
            markers = [
                "slow: 将测试标记为慢（使用 '-m \"not slow\"' 取消选择）",
                "serial",
            ]

        请注意，标记名称后的 ``:`` 后的内容是可选描述。

        或者，您可以在 :ref:`pytest_configure <initialization-hooks>` 钩子中以编程方式注册新标记：

        .. code-block:: python

            def pytest_configure(config):
                config.addinivalue_line(
                    "markers", "env(name): 仅在命名环境中运行测试"
                )

        注册的标记会出现在 pytest 的帮助文本中，并且不会发出警告（请参见下一节）。  
        建议第三方插件始终 :ref:`注册他们的标记 <registering-markers>`。

    .. tab:: 英文

        You can register custom marks in your ``pytest.ini`` file like this:

        .. code-block:: ini

            [pytest]
            markers =
                slow: marks tests as slow (deselect with '-m "not slow"')
                serial

        or in your ``pyproject.toml`` file like this:

        .. code-block:: toml

            [tool.pytest.ini_options]
            markers = [
                "slow: marks tests as slow (deselect with '-m \"not slow\"')",
                "serial",
            ]

        Note that everything past the ``:`` after the mark name is an optional description.

        Alternatively, you can register new markers programmatically in a
        :ref:`pytest_configure <initialization-hooks>` hook:

        .. code-block:: python

            def pytest_configure(config):
                config.addinivalue_line(
                    "markers", "env(name): mark test to run only on named environment"
                )


        Registered marks appear in pytest's help text and do not emit warnings (see the next section). It
        is recommended that third-party plugins always :ref:`register their markers <registering-markers>`.

.. _unknown-marks:

对未知标记抛出错误
-------------------------------

**Raising errors on unknown marks**

.. tabs::

    .. tab:: 中文

        未注册的标记如果使用 ``@pytest.mark.name_of_the_mark`` 装饰器应用，将始终发出警告，以避免由于拼写错误而静默执行意外操作。  
        如上一节所述，您可以通过在 ``pytest.ini`` 文件中注册它们或使用自定义的 ``pytest_configure`` 钩子来禁用自定义标记的警告。

        当传递 ``--strict-markers`` 命令行标志时，任何使用 ``@pytest.mark.name_of_the_mark`` 装饰器应用的未知标记将触发错误。  
        您可以通过将 ``--strict-markers`` 添加到 ``addopts`` 来在项目中强制执行此验证：

        .. code-block:: ini

            [pytest]
            addopts = --strict-markers
            markers =
                slow: 将测试标记为慢（使用 '-m "not slow"' 取消选择）
                serial

    .. tab:: 英文

        Unregistered marks applied with the ``@pytest.mark.name_of_the_mark`` decorator
        will always emit a warning in order to avoid silently doing something
        surprising due to mistyped names. As described in the previous section, you can disable
        the warning for custom marks by registering them in your ``pytest.ini`` file or
        using a custom ``pytest_configure`` hook.

        When the ``--strict-markers`` command-line flag is passed, any unknown marks applied
        with the ``@pytest.mark.name_of_the_mark`` decorator will trigger an error. You can
        enforce this validation in your project by adding ``--strict-markers`` to ``addopts``:

        .. code-block:: ini

            [pytest]
            addopts = --strict-markers
            markers =
                slow: marks tests as slow (deselect with '-m "not slow"')
                serial
