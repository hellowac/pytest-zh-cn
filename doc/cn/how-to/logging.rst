.. _logging:

如何管理日志记录
---------------------

*How to manage logging*

.. tabs::

    .. tab:: 中文

        pytest 会自动捕获级别为 ``WARNING`` 或以上的日志消息，并以与捕获的 stdout 和 stderr 相同的方式，在每个失败测试的专门部分中显示它们。

        在没有选项的情况下运行：

        .. code-block:: bash

            pytest

        显示失败的测试如下：

        .. code-block:: pytest

            ----------------------- Captured stdlog call ----------------------
            test_reporting.py    26 WARNING  text going to logger
            ----------------------- Captured stdout call ----------------------
            text going to stdout
            ----------------------- Captured stderr call ----------------------
            text going to stderr
            ==================== 2 failed in 0.02 seconds =====================

        默认情况下，每个捕获的日志消息会显示模块、行号、日志级别和消息。

        如果需要，可以通过传递特定的格式选项，将日志和日期格式指定为日志模块支持的任何格式：

        .. code-block:: bash

            pytest --log-format="%(asctime)s %(levelname)s %(message)s" \
                    --log-date-format="%Y-%m-%d %H:%M:%S"

        显示失败的测试如下：

        .. code-block:: pytest

            ----------------------- Captured stdlog call ----------------------
            2010-04-10 14:48:44 WARNING text going to logger
            ----------------------- Captured stdout call ----------------------
            text going to stdout
            ----------------------- Captured stderr call ----------------------
            text going to stderr
            ==================== 2 failed in 0.02 seconds =====================

        这些选项也可以通过 ``pytest.ini`` 文件进行自定义：

        .. code-block:: ini

            [pytest]
            log_format = %(asctime)s %(levelname)s %(message)s
            log_date_format = %Y-%m-%d %H:%M:%S

        可以通过 ``--log-disable={logger_name}`` 禁用特定的日志记录器。此参数可以多次传递：

        .. code-block:: bash

            pytest --log-disable=main --log-disable=testing

        此外，还可以通过以下方式完全禁用失败测试中捕获内容（stdout、stderr 和日志）的报告：

        .. code-block:: bash

            pytest --show-capture=no

    .. tab:: 英文

        pytest captures log messages of level ``WARNING`` or above automatically and displays them in their own section for each failed test in the same manner as captured stdout and stderr.

        Running without options:

        .. code-block:: bash

            pytest

        Shows failed tests like so:

        .. code-block:: pytest

            ----------------------- Captured stdlog call ----------------------
            test_reporting.py    26 WARNING  text going to logger
            ----------------------- Captured stdout call ----------------------
            text going to stdout
            ----------------------- Captured stderr call ----------------------
            text going to stderr
            ==================== 2 failed in 0.02 seconds =====================

        By default each captured log message shows the module, line number, log level and message.

        If desired the log and date format can be specified to anything that the logging module supports by passing specific formatting options:

        .. code-block:: bash

            pytest --log-format="%(asctime)s %(levelname)s %(message)s" \
                    --log-date-format="%Y-%m-%d %H:%M:%S"

        Shows failed tests like so:

        .. code-block:: pytest

            ----------------------- Captured stdlog call ----------------------
            2010-04-10 14:48:44 WARNING text going to logger
            ----------------------- Captured stdout call ----------------------
            text going to stdout
            ----------------------- Captured stderr call ----------------------
            text going to stderr
            ==================== 2 failed in 0.02 seconds =====================

        These options can also be customized through ``pytest.ini`` file:

        .. code-block:: ini

            [pytest]
            log_format = %(asctime)s %(levelname)s %(message)s
            log_date_format = %Y-%m-%d %H:%M:%S

        Specific loggers can be disabled via ``--log-disable={logger_name}``. This argument can be passed multiple times:

        .. code-block:: bash

            pytest --log-disable=main --log-disable=testing

        Further it is possible to disable reporting of captured content (stdout, stderr and logs) on failed tests completely with:

        .. code-block:: bash

            pytest --show-capture=no


caplog fixture
^^^^^^^^^^^^^^

*caplog fixture*

.. tabs::

    .. tab:: 中文

        在测试中，可以更改捕获的日志消息的日志级别。这是通过 ``caplog`` 固件支持的：

        .. code-block:: python

            def test_foo(caplog):
                caplog.set_level(logging.INFO)

        默认情况下，级别设置在根日志记录器上，但为了方便，也可以设置任何日志记录器的日志级别：

        .. code-block:: python

            def test_foo(caplog):
                caplog.set_level(logging.CRITICAL, logger="root.baz")

        设置的日志级别会在测试结束时自动恢复。

        还可以使用上下文管理器在 ``with`` 块内临时更改日志级别：

        .. code-block:: python

            def test_bar(caplog):
                with caplog.at_level(logging.INFO):
                    pass

        同样，默认情况下，影响的是根日志记录器的级别，但也可以使用以下方式更改任何日志记录器的级别：

        .. code-block:: python

            def test_bar(caplog):
                with caplog.at_level(logging.CRITICAL, logger="root.baz"):
                    pass

        最后，在测试运行期间发送到日志记录器的所有日志都以 ``logging.LogRecord`` 实例和最终日志文本的形式提供在固件中。这在您想要断言消息内容时非常有用：

        .. code-block:: python

            def test_baz(caplog):
                func_under_test()
                for record in caplog.records:
                    assert record.levelname != "CRITICAL"
                assert "wally" not in caplog.text

        有关日志记录的所有可用属性，请参见 ``logging.LogRecord`` 类。

        如果您只是想确保在给定日志记录器名称下以给定严重性和消息记录了特定消息，也可以使用 ``record_tuples``：

        .. code-block:: python

            def test_foo(caplog):
                logging.getLogger().info("boo %s", "arg")

                assert caplog.record_tuples == [("root", logging.INFO, "boo arg")]

        您可以调用 ``caplog.clear()`` 来重置测试中的捕获日志记录：

        .. code-block:: python

            def test_something_with_clearing_records(caplog):
                some_method_that_creates_log_records()
                caplog.clear()
                your_test_method()
                assert ["Foo"] == [rec.message for rec in caplog.records]

        ``caplog.records`` 属性仅包含当前阶段的记录，因此在 ``setup`` 阶段中，它仅包含设置日志， ``call`` 和 ``teardown`` 阶段也是如此。

        要访问其他阶段的日志，请使用 ``caplog.get_records(when)`` 方法。例如，如果您想确保使用某个固件的测试永远不会记录任何警告，您可以在拆卸期间检查 ``setup`` 和 ``call`` 阶段的记录，如下所示：

        .. code-block:: python

            @pytest.fixture
            def window(caplog):
                window = create_window()
                yield window
                for when in ("setup", "call"):
                    messages = [
                        x.message for x in caplog.get_records(when) if x.levelno == logging.WARNING
                    ]
                    if messages:
                        pytest.fail(f"warning messages encountered during testing: {messages}")

        完整的 API 可在 :class:`pytest.LogCaptureFixture` 中找到。

        .. warning::

            ``caplog`` 固件向根日志记录器添加了一个处理程序以捕获日志。如果在测试期间修改了根日志记录器，例如使用 ``logging.config.dictConfig``，则可能会删除此处理程序并导致无法捕获日志。为避免此情况，请确保任何根日志记录器配置仅添加到现有处理程序中。

    .. tab:: 英文

        Inside tests it is possible to change the log level for the captured log messages.  This is supported by the ``caplog`` fixture:

        .. code-block:: python

            def test_foo(caplog):
                caplog.set_level(logging.INFO)

        By default the level is set on the root logger, however as a convenience it is also possible to set the log level of any logger:

        .. code-block:: python

            def test_foo(caplog):
                caplog.set_level(logging.CRITICAL, logger="root.baz")

        The log levels set are restored automatically at the end of the test.

        It is also possible to use a context manager to temporarily change the log level inside a ``with`` block:

        .. code-block:: python

            def test_bar(caplog):
                with caplog.at_level(logging.INFO):
                    pass

        Again, by default the level of the root logger is affected but the level of any logger can be changed instead with:

        .. code-block:: python

            def test_bar(caplog):
                with caplog.at_level(logging.CRITICAL, logger="root.baz"):
                    pass

        Lastly all the logs sent to the logger during the test run are made available on the fixture in the form of both the ``logging.LogRecord`` instances and the final log text. This is useful for when you want to assert on the contents of a message:

        .. code-block:: python

            def test_baz(caplog):
                func_under_test()
                for record in caplog.records:
                    assert record.levelname != "CRITICAL"
                assert "wally" not in caplog.text

        For all the available attributes of the log records see the ``logging.LogRecord`` class.

        You can also resort to ``record_tuples`` if all you want to do is to ensure, that certain messages have been logged under a given logger name with a given severity and message:

        .. code-block:: python

            def test_foo(caplog):
                logging.getLogger().info("boo %s", "arg")

                assert caplog.record_tuples == [("root", logging.INFO, "boo arg")]

        You can call ``caplog.clear()`` to reset the captured log records in a test:

        .. code-block:: python

            def test_something_with_clearing_records(caplog):
                some_method_that_creates_log_records()
                caplog.clear()
                your_test_method()
                assert ["Foo"] == [rec.message for rec in caplog.records]


        The ``caplog.records`` attribute contains records from the current stage only, so inside the ``setup`` phase it contains only setup logs, same with the ``call`` and
        ``teardown`` phases.

        To access logs from other stages, use the ``caplog.get_records(when)`` method. As an example, if you want to make sure that tests which use a certain fixture never log any warnings, you can inspect the records for the ``setup`` and ``call`` stages during teardown like so:

        .. code-block:: python

            @pytest.fixture
            def window(caplog):
                window = create_window()
                yield window
                for when in ("setup", "call"):
                    messages = [
                        x.message for x in caplog.get_records(when) if x.levelno == logging.WARNING
                    ]
                    if messages:
                        pytest.fail(f"warning messages encountered during testing: {messages}")



        The full API is available at :class:`pytest.LogCaptureFixture`.

        .. warning::

            The ``caplog`` fixture adds a handler to the root logger to capture logs. If the root logger is modified during a test, for example with ``logging.config.dictConfig``, this handler may be removed and cause no logs to be captured. To avoid this, ensure that any root logger configuration only adds to the existing handlers.


.. _live_logs:

实时日志
^^^^^^^^^

*Live Logs*

.. tabs::

    .. tab:: 中文

        通过将 :confval:`log_cli` 配置选项设置为 ``true``, pytest 将在日志记录被发出时直接将其输出到控制台。

        您可以通过传递 ``--log-cli-level`` 来指定打印到控制台的日志记录的日志级别，该级别等于或高于指定级别。此设置接受日志级别名称或数字值，详细信息见 :ref:`logging's documentation <python:levels>`。

        此外，您还可以指定 ``--log-cli-format`` 和 ``--log-cli-date-format``，这两个选项的默认值与 ``--log-format`` 和 ``--log-date-format`` 相同（如果未提供），但仅适用于控制台日志处理程序。

        所有 CLI 日志选项也可以在配置 INI 文件中设置。选项名称如下：

        * ``log_cli_level``
        * ``log_cli_format``
        * ``log_cli_date_format``

        如果您需要将整个测试套件的日志记录调用记录到文件中，可以传递 ``--log-file=/path/to/log/file``。此日志文件默认以写入模式打开，这意味着每次运行测试会话时都会覆盖该文件。如果您希望以追加模式打开该文件，则可以传递 ``--log-file-mode=a``。请注意，无论是在 CLI 上传递的相对路径还是在配置文件中声明的相对路径，日志文件位置始终相对于当前工作目录进行解析。

        您还可以通过传递 ``--log-file-level`` 来指定日志文件的日志级别。此设置接受日志级别名称或数字值，详细信息见 :ref:`logging's documentation <python:levels>`。

        此外，您还可以指定 ``--log-file-format`` 和 ``--log-file-date-format``，这两个选项等同于 ``--log-format`` 和 ``--log-date-format``，但适用于日志文件日志处理程序。

        所有日志文件选项也可以在配置 INI 文件中设置。选项名称如下：

        * ``log_file``
        * ``log_file_mode``
        * ``log_file_level``
        * ``log_file_format``
        * ``log_file_date_format``

        您可以调用 ``set_log_path()`` 动态自定义日志文件路径。此功能被视为 **实验性**。请注意， ``set_log_path()`` 会遵循 ``log_file_mode`` 选项。

    .. tab:: 英文

        By setting the :confval:`log_cli` configuration option to ``true``, pytest will output logging records as they are emitted directly into the console.

        You can specify the logging level for which log records with equal or higher level are printed to the console by passing ``--log-cli-level``. This setting accepts the logging level names or numeric values as seen in :ref:`logging's documentation <python:levels>`.

        Additionally, you can also specify ``--log-cli-format`` and ``--log-cli-date-format`` which mirror and default to ``--log-format`` and ``--log-date-format`` if not provided, but are applied only to the console logging handler.

        All of the CLI log options can also be set in the configuration INI file. The
        option names are:

        * ``log_cli_level``
        * ``log_cli_format``
        * ``log_cli_date_format``

        If you need to record the whole test suite logging calls to a file, you can pass
        ``--log-file=/path/to/log/file``. This log file is opened in write mode by default which
        means that it will be overwritten at each run tests session.
        If you'd like the file opened in append mode instead, then you can pass ``--log-file-mode=a``.
        Note that relative paths for the log-file location, whether passed on the CLI or declared in a
        config file, are always resolved relative to the current working directory.

        You can also specify the logging level for the log file by passing
        ``--log-file-level``. This setting accepts the logging level names or numeric
        values as seen in :ref:`logging's documentation <python:levels>`.

        Additionally, you can also specify ``--log-file-format`` and
        ``--log-file-date-format`` which are equal to ``--log-format`` and
        ``--log-date-format`` but are applied to the log file logging handler.

        All of the log file options can also be set in the configuration INI file. The
        option names are:

        * ``log_file``
        * ``log_file_mode``
        * ``log_file_level``
        * ``log_file_format``
        * ``log_file_date_format``

        You can call ``set_log_path()`` to customize the log_file path dynamically. This functionality
        is considered **experimental**. Note that ``set_log_path()`` respects the ``log_file_mode`` option.

.. _log_colors:

自定义颜色
^^^^^^^^^^^^^^^^^^

*Customizing Colors*

.. tabs::

    .. tab:: 中文

        如果启用了彩色终端输出，日志级别将以颜色显示。支持通过 ``add_color_level()`` 更改默认颜色或为自定义日志级别添加颜色。示例如下：

        .. code-block:: python

            @pytest.hookimpl(trylast=True)
            def pytest_configure(config):
                logging_plugin = config.pluginmanager.get_plugin("logging-plugin")

                # 更改现有日志级别的颜色
                logging_plugin.log_cli_handler.formatter.add_color_level(logging.INFO, "cyan")

                # 为自定义日志级别添加颜色（自定义日志级别 `SPAM` 已设置）
                logging_plugin.log_cli_handler.formatter.add_color_level(logging.SPAM, "blue")

        .. warning::

            此功能及其 API 被视为 **实验性**，可能会在发布之间发生更改，而无需提供弃用通知。

    .. tab:: 英文

        Log levels are colored if colored terminal output is enabled. Changing
        from default colors or putting color on custom log levels is supported
        through ``add_color_level()``. Example:

        .. code-block:: python

            @pytest.hookimpl(trylast=True)
            def pytest_configure(config):
                logging_plugin = config.pluginmanager.get_plugin("logging-plugin")

                # Change color on existing log level
                logging_plugin.log_cli_handler.formatter.add_color_level(logging.INFO, "cyan")

                # Add color to a custom log level (a custom log level `SPAM` is already set up)
                logging_plugin.log_cli_handler.formatter.add_color_level(logging.SPAM, "blue")
        .. warning::

            This feature and its API are considered **experimental** and might change
            between releases without a deprecation notice.

.. _log_release_notes:

发行说明
^^^^^^^^^^^^^

*Release notes*

.. tabs::

    .. tab:: 中文

        此功能作为 :pypi:`pytest-catchlog` 插件的即插即用替代品引入，并且它们之间存在冲突。在引入此功能时，已删除与 ``pytest-capturelog`` 的向后兼容 API，因此如果出于这个原因您仍然需要 ``pytest-catchlog``，可以通过在 ``pytest.ini`` 中添加以下内容来禁用内部功能：

        .. code-block:: ini

            [pytest]
                addopts=-p no:logging

    .. tab:: 英文

        This feature was introduced as a drop-in replacement for the
        :pypi:`pytest-catchlog` plugin and they conflict
        with each other. The backward compatibility API with ``pytest-capturelog``
        has been dropped when this feature was introduced, so if for that reason you
        still need ``pytest-catchlog`` you can disable the internal feature by
        adding to your ``pytest.ini``:

        .. code-block:: ini

            [pytest]
                addopts=-p no:logging


.. _log_changes_3_4:

pytest 3.4 中的不兼容更改
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

*Incompatible changes in pytest 3.4*

.. tabs::

    .. tab:: 中文

        此功能是在 ``3.3`` 中引入的，并且在 ``3.4`` 中经过社区反馈做了一些 **不兼容的更改**：

        * 日志级别不会再更改，除非通过 :confval:`log_level` 配置或 ``--log-level`` 命令行选项显式请求。这允许用户自行配置记录器对象。设置 :confval:`log_level` 将全局设置捕获的日志级别，因此如果特定测试需要比这个级别更低的级别，请使用 ``caplog.set_level()`` 功能，否则该测试可能会失败。
        * :ref:`实时日志 <live_logs>` 现在默认禁用，可以通过将 :confval:`log_cli` 配置选项设置为 ``true`` 来启用。当启用时，日志的详细程度增加，因此每个测试的日志都是可见的。
        * :ref:`实时日志 <live_logs>` 现在发送到 ``sys.stdout``，不再需要 ``-s`` 命令行选项才能工作。

        如果您希望部分恢复 ``3.3`` 版本的日志记录行为，可以将以下选项添加到您的 ``ini`` 文件中：

        .. code-block:: ini

            [pytest]
            log_cli=true
            log_level=NOTSET

        有关导致这些更改的讨论的更多详细信息，请参见 :issue:`3013`。

    .. tab:: 英文

        This feature was introduced in ``3.3`` and some **incompatible changes** have been
        made in ``3.4`` after community feedback:

        * Log levels are no longer changed unless explicitly requested by the :confval:`log_level` configuration
        or ``--log-level`` command-line options. This allows users to configure logger objects themselves.
        Setting :confval:`log_level` will set the level that is captured globally so if a specific test requires
        a lower level than this, use the ``caplog.set_level()`` functionality otherwise that test will be prone to
        failure.
        * :ref:`Live Logs <live_logs>` is now disabled by default and can be enabled setting the
        :confval:`log_cli` configuration option to ``true``. When enabled, the verbosity is increased so logging for each
        test is visible.
        * :ref:`Live Logs <live_logs>` are now sent to ``sys.stdout`` and no longer require the ``-s`` command-line option
        to work.

        If you want to partially restore the logging behavior of version ``3.3``, you can add this options to your ``ini``
        file:

        .. code-block:: ini

            [pytest]
            log_cli=true
            log_level=NOTSET

        More details about the discussion that lead to this changes can be read in :issue:`3013`.
