.. _`external plugins`:
.. _`extplugins`:
.. _`using plugins`:

如何安装和使用插件
===============================

**How to install and use plugins**

.. tabs::

    .. tab:: 中文

        本节介绍了安装和使用第三方插件的内容。要编写自己的插件，请参阅 :ref:`writing-plugins`。

        可以使用 ``pip`` 轻松安装第三方插件：

        .. code-block:: bash

            pip install pytest-NAME
            pip uninstall pytest-NAME

        如果插件已安装，``pytest`` 会自动找到并集成它，无需手动激活。

        以下是一些流行插件的简短注释列表：

        * :pypi:`pytest-django`：为 `django <https://docs.djangoproject.com/>`_ 应用程序编写测试，并集成 pytest。

        * :pypi:`pytest-twisted`：为 `twisted <https://twistedmatrix.com/>`_ 应用程序编写测试，启动反应器并处理测试函数中的延迟。

        * :pypi:`pytest-cov`：提供测试覆盖率报告，兼容分布式测试。

        * :pypi:`pytest-xdist`：分发测试到多个 CPU 和远程主机，支持分箱模式以应对段错误，并可在文件变更时自动重跑失败测试。

        * :pypi:`pytest-instafail`：在测试运行时即时报告失败。

        * :pypi:`pytest-bdd`：使用行为驱动的测试编写测试。

        * :pypi:`pytest-timeout`：根据函数标记或全局定义设置测试超时时间。

        * :pypi:`pytest-pep8`：提供 ``--pep8`` 选项以启用 PEP8 合规性检查。

        * :pypi:`pytest-flakes`：使用 pyflakes 检查源代码。

        * :pypi:`allure-pytest`：通过 `allure-framework <https://github.com/allure-framework/>`_ 报告测试结果。

        要查看所有插件的完整列表及其在不同 pytest 和 Python 版本下的最新测试状态，请访问 :ref:`plugin-list`。您还可以通过 `pytest- pypi.org search`_ 发现更多插件。

    .. tab:: 英文

        This section talks about installing and using third party plugins.
        For writing your own plugins, please refer to :ref:`writing-plugins`.

        Installing a third party plugin can be easily done with ``pip``:

        .. code-block:: bash

            pip install pytest-NAME
            pip uninstall pytest-NAME

        If a plugin is installed, ``pytest`` automatically finds and integrates it, there is no need to activate it.

        Here is a little annotated list for some popular plugins:

        * :pypi:`pytest-django`: write tests for `django <https://docs.djangoproject.com/>`_ apps, using pytest integration.

        * :pypi:`pytest-twisted`: write tests for `twisted <https://twistedmatrix.com/>`_ apps, starting a reactor and processing deferreds from test functions.

        * :pypi:`pytest-cov`: coverage reporting, compatible with distributed testing

        * :pypi:`pytest-xdist`: to distribute tests to CPUs and remote hosts, to run in boxed mode which allows to survive segmentation faults, to run in looponfailing mode, automatically re-running failing tests on file changes.

        * :pypi:`pytest-instafail`: to report failures while the test run is happening.

        * :pypi:`pytest-bdd`: to write tests using behaviour-driven testing.

        * :pypi:`pytest-timeout`: to timeout tests based on function marks or global definitions.

        * :pypi:`pytest-pep8`: a ``--pep8`` option to enable PEP8 compliance checking.

        * :pypi:`pytest-flakes`: check source code with pyflakes.

        * :pypi:`allure-pytest`: report test results via `allure-framework <https://github.com/allure-framework/>`_.

        To see a complete list of all plugins with their latest testing status against different pytest and Python versions, please visit :ref:`plugin-list`.

        You may also discover more plugins through a `pytest- pypi.org search`_.

.. _`pytest- pypi.org search`: https://pypi.org/search/?q=pytest-


.. _`available installable plugins`:

在测试模块或 conftest 文件中请求/加载插件
-----------------------------------------------------------

**Requiring/Loading plugins in a test module or conftest file**

.. tabs::

    .. tab:: 中文

        可以在测试模块或 `conftest` 文件中使用 :globalvar:`pytest_plugins` 来要求插件：

        .. code-block:: python

            pytest_plugins = ("myapp.testsupport.myplugin",)

        当加载该测试模块或 `conftest` 插件时，指定的插件也会被加载。

        .. note::

            在非根目录的 `conftest.py` 文件中使用 ``pytest_plugins`` 变量来要求插件的做法已被弃用。请参阅编写插件部分的
            :ref:`详细解释 <requiring plugins in non-root conftests>`。

        .. note::
        
            ``pytest_plugins`` 名称已被保留，不应将其用作自定义插件模块的名称。

    .. tab:: 英文

        You can require plugins in a test module or a conftest file using :globalvar:`pytest_plugins`:

        .. code-block:: python

            pytest_plugins = ("myapp.testsupport.myplugin",)

        When the test module or conftest plugin is loaded the specified plugins
        will be loaded as well.

        .. note::

            Requiring plugins using a ``pytest_plugins`` variable in non-root
            ``conftest.py`` files is deprecated. See
            :ref:`full explanation <requiring plugins in non-root conftests>`
            in the Writing plugins section.

        .. note::
        The name ``pytest_plugins`` is reserved and should not be used as a
        name for a custom plugin module.


.. _`findpluginname`:

找出哪些插件处于活动状态
------------------------------------

**Finding out which plugins are active**

.. tabs::

    .. tab:: 中文

        如果您想查看环境中哪些插件是活跃的，可以输入：

        .. code-block:: bash

            pytest --trace-config

        这将显示一个扩展的测试头，其中列出了激活的插件及其名称。同时还会在加载时打印出本地插件，也就是 :ref:`conftest.py <conftest.py plugins>` 文件。

    .. tab:: 英文

        If you want to find out which plugins are active in your
        environment you can type:

        .. code-block:: bash

            pytest --trace-config

        and will get an extended test header which shows activated plugins
        and their names. It will also print local plugins aka
        :ref:`conftest.py <conftest.py plugins>` files when they are loaded.

.. _`cmdunregister`:

按名称停用/取消注册插件
---------------------------------------------

**Deactivating / unregistering a plugin by name**

.. tabs::

    .. tab:: 中文

        您可以阻止插件加载或注销它们：

        .. code-block:: bash

            pytest -p no:NAME

        这表示任何后续激活/加载指定插件的尝试都将无效。

        如果您想在项目中无条件禁用某个插件，可以将以下选项添加到 ``pytest.ini`` 文件中：

        .. code-block:: ini

            [pytest]
            addopts = -p no:NAME

        或者，您也可以仅在特定环境中禁用插件（例如在 CI 服务器中），为此可以将环境变量 ``PYTEST_ADDOPTS`` 设置为 ``-p no:name``。

        关于如何获取插件名称，请参见 :ref:`findpluginname`。

    .. tab:: 英文

        You can prevent plugins from loading or unregister them:

        .. code-block:: bash

            pytest -p no:NAME

        This means that any subsequent try to activate/load the named
        plugin will not work.

        If you want to unconditionally disable a plugin for a project, you can add
        this option to your ``pytest.ini`` file:

        .. code-block:: ini

            [pytest]
            addopts = -p no:NAME

        Alternatively to disable it only in certain environments (for example in a
        CI server), you can set ``PYTEST_ADDOPTS`` environment variable to
        ``-p no:name``.

        See :ref:`findpluginname` for how to obtain the name of a plugin.

.. _`builtin plugins`:
