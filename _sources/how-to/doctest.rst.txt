.. _doctest:

如何运行文档测试
=========================================================

**How to run doctests**

.. tabs::

    .. tab:: 中文

        默认情况下，所有匹配 ``test*.txt`` 模式的文件将通过 Python 标准 :mod:`doctest` 模块进行运行。你可以通过以下命令更改模式：

        .. code-block:: bash

            pytest --doctest-glob="*.rst"

        在命令行中可以多次指定 ``--doctest-glob``。

        如果你有一个这样的文本文件：

        .. code-block:: text

            # content of test_example.txt

            hello this is a doctest
            >>> x = 3
            >>> x
            3

        那么你可以直接调用 ``pytest``：

        .. code-block:: pytest

            $ pytest
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 1 item

            test_example.txt .                                                   [100%]

            ============================ 1 passed in 0.12s =============================

        默认情况下，pytest 将收集 ``test*.txt`` 文件以查找 doctest 指令，但你可以使用 ``--doctest-glob`` 选项传递额外的模式（允许多次使用）。

        除了文本文件，你还可以直接从类和函数的文档字符串中执行 doctests，包括来自测试模块的：

        .. code-block:: python

            # content of mymodule.py
            def something():
                """a doctest in a docstring
                >>> something()
                42
                """
                return 42

        .. code-block:: bash

            $ pytest --doctest-modules
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 2 items

            mymodule.py .                                                        [ 50%]
            test_example.txt .                                                   [100%]

            ============================ 2 passed in 0.12s =============================

        你可以通过将这些更改放入 pytest.ini 文件中，使其在项目中永久生效，如下所示：

        .. code-block:: ini

            # content of pytest.ini
            [pytest]
            addopts = --doctest-modules

    .. tab:: 英文

        By default, all files matching the ``test*.txt`` pattern will
        be run through the python standard :mod:`doctest` module.  You
        can change the pattern by issuing:

        .. code-block:: bash

            pytest --doctest-glob="*.rst"

        on the command line. ``--doctest-glob`` can be given multiple times in the command-line.

        If you then have a text file like this:

        .. code-block:: text

            # content of test_example.txt

            hello this is a doctest
            >>> x = 3
            >>> x
            3

        then you can just invoke ``pytest`` directly:

        .. code-block:: pytest

            $ pytest
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 1 item

            test_example.txt .                                                   [100%]

            ============================ 1 passed in 0.12s =============================

        By default, pytest will collect ``test*.txt`` files looking for doctest directives, but you
        can pass additional globs using the ``--doctest-glob`` option (multi-allowed).

        In addition to text files, you can also execute doctests directly from docstrings of your classes
        and functions, including from test modules:

        .. code-block:: python

            # content of mymodule.py
            def something():
                """a doctest in a docstring
                >>> something()
                42
                """
                return 42

        .. code-block:: bash

            $ pytest --doctest-modules
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 2 items

            mymodule.py .                                                        [ 50%]
            test_example.txt .                                                   [100%]

            ============================ 2 passed in 0.12s =============================

        You can make these changes permanent in your project by
        putting them into a pytest.ini file like this:

        .. code-block:: ini

            # content of pytest.ini
            [pytest]
            addopts = --doctest-modules


编码
--------

**Encoding**

.. tabs::

    .. tab:: 中文

        默认编码为 **UTF-8**，但您可以使用 ``doctest_encoding`` ini 选项指定将用于这些 doctest 文件的编码：

        .. code-block:: ini

            # pytest.ini 的内容
            [pytest]
            doctest_encoding = latin1

    .. tab:: 英文

        The default encoding is **UTF-8**, but you can specify the encoding
        that will be used for those doctest files using the
        ``doctest_encoding`` ini option:

        .. code-block:: ini

            # content of pytest.ini
            [pytest]
            doctest_encoding = latin1

.. _using doctest options:

使用 'doctest' 选项
-----------------------

**Using 'doctest' options**

.. tabs::

    .. tab:: 中文

        Python 的标准 :mod:`doctest` 模块提供了一些 :ref:`options <python:option-flags-and-directives>` 用于配置 doctest 测试的严格程度。在 pytest 中，你可以通过配置文件启用这些标志。

        例如，为了使 pytest 忽略尾随空格并忽略冗长的异常堆栈跟踪，你可以这样写：

        .. code-block:: ini

            [pytest]
            doctest_optionflags = NORMALIZE_WHITESPACE IGNORE_EXCEPTION_DETAIL

        另外，选项也可以通过文档测试中的内联注释来启用：

        .. code-block:: rst

            >>> something_that_raises()  # doctest: +IGNORE_EXCEPTION_DETAIL
            Traceback (most recent call last):
            ValueError: ...

        pytest 还引入了新的选项：

        * ``ALLOW_UNICODE``：启用后，预期 doctest 输出中的 unicode 字符串将去掉 ``u`` 前缀。这使得 doctests 可以在 Python 2 和 Python 3 中无差异地运行。

        * ``ALLOW_BYTES``：类似地，预期 doctest 输出中的字节字符串将去掉 ``b`` 前缀。

        * ``NUMBER``：启用后，浮点数只需匹配到你在预期 doctest 输出中写的精度。数字使用 :func:`pytest.approx` 进行比较，相对容差等于精度。例如，以下输出在比较 ``3.14`` 和 ``pytest.approx(math.pi, rel=10**-2)`` 时只需匹配到 2 位小数：

            >>> math.pi
            3.14

          如果你写了 ``3.1416``，那么实际输出需要大约匹配到 4 位小数；依此类推。

          这避免了由浮点精度限制引起的误报，例如::

            Expected:
                0.233
            Got:
                0.23300000000000001

          ``NUMBER`` 还支持浮点数列表——事实上，它匹配输出中出现的任何浮点数，甚至在字符串内部！这意味着在你的配置文件中的 ``doctest_optionflags`` 中全局启用可能并不合适。

          .. versionadded:: 5.1

    .. tab:: 英文

        Python's standard :mod:`doctest` module provides some :ref:`options <python:option-flags-and-directives>` to configure the strictness of doctest tests. In pytest, you can enable those flags using the configuration file.

        For example, to make pytest ignore trailing whitespaces and ignore lengthy exception stack traces you can just write:

        .. code-block:: ini

            [pytest]
            doctest_optionflags = NORMALIZE_WHITESPACE IGNORE_EXCEPTION_DETAIL

        Alternatively, options can be enabled by an inline comment in the doc test itself:

        .. code-block:: rst

            >>> something_that_raises()  # doctest: +IGNORE_EXCEPTION_DETAIL
            Traceback (most recent call last):
            ValueError: ...

        pytest also introduces new options:

        * ``ALLOW_UNICODE``: when enabled, the ``u`` prefix is stripped from unicode strings in expected doctest output. This allows doctests to run in Python 2 and Python 3 unchanged.

        * ``ALLOW_BYTES``: similarly, the ``b`` prefix is stripped from byte strings in expected doctest output.

        * ``NUMBER``: when enabled, floating-point numbers only need to match as far as the precision you have written in the expected doctest output. The numbers are compared using :func:`pytest.approx` with relative tolerance equal to the precision. For example, the following output would only need to match to 2 decimal places when comparing ``3.14`` to ``pytest.approx(math.pi, rel=10**-2)``::

              >>> math.pi
              3.14

          If you wrote ``3.1416`` then the actual output would need to match to
          approximately 4 decimal places; and so on.

          This avoids false positives caused by limited floating-point precision, like
          this::

            Expected:
                0.233
            Got:
                0.23300000000000001

          ``NUMBER`` also supports lists of floating-point numbers -- in fact, it
          matches floating-point numbers appearing anywhere in the output, even inside
          a string! This means that it may not be appropriate to enable globally in
          ``doctest_optionflags`` in your configuration file.

          .. versionadded:: 5.1


失败时继续
-------------------

**Continue on failure**

.. tabs::

    .. tab:: 中文

        默认情况下，pytest 只会报告给定 doctest 的第一次失败。如果您想在失败时继续测试，请执行以下操作：

        .. code-block:: bash

            pytest --doctest-modules --doctest-continue-on-failure

    .. tab:: 英文

        By default, pytest would report only the first failure for a given doctest. If
        you want to continue the test even when you have failures, do:

        .. code-block:: bash

            pytest --doctest-modules --doctest-continue-on-failure


输出格式
-------------

**Output format**

.. tabs::

    .. tab:: 中文

        您可以使用选项中的标准 doctest 模块格式之一来更改 doctest 失败时的 diff 输出格式（请参阅 :data:`python:doctest.REPORT_UDIFF`、 :data:`python:doctest.REPORT_CDIFF`、 :data:`python:doctest.REPORT_NDIFF`、 :data:`python:doctest.REPORT_ONLY_FIRST_FAILURE`）：

        .. code-block:: bash

            pytest --doctest-modules --doctest-report none
            pytest --doctest-modules --doctest-report udiff
            pytest --doctest-modules --doctest-report cdiff
            pytest --doctest-modules --doctest-report ndiff
            pytest --doctest-modules --doctest-report only_first_failure

    .. tab:: 英文

        You can change the diff output format on failure for your doctests
        by using one of standard doctest modules format in options
        (see :data:`python:doctest.REPORT_UDIFF`, :data:`python:doctest.REPORT_CDIFF`,
        :data:`python:doctest.REPORT_NDIFF`, :data:`python:doctest.REPORT_ONLY_FIRST_FAILURE`):

        .. code-block:: bash

            pytest --doctest-modules --doctest-report none
            pytest --doctest-modules --doctest-report udiff
            pytest --doctest-modules --doctest-report cdiff
            pytest --doctest-modules --doctest-report ndiff
            pytest --doctest-modules --doctest-report only_first_failure


pytest 特定功能
------------------------

**pytest-specific features**

.. tabs::

    .. tab:: 中文

        提供的一些功能可使编写 文档测试(doctest) 更加容易或更好地与现有测试套件集成。但请记住，使用这些功能将使 文档测试(doctest) 与标准 ``doctests`` 模块不兼容。

    .. tab:: 英文

        Some features are provided to make writing doctests easier or with better integration with your existing test suite. Keep in mind however that by using those features you will make your doctests incompatible with the standard ``doctests`` module.

使用 fixtures
^^^^^^^^^^^^^^

**Using fixtures**

.. tabs::

    .. tab:: 中文

        可以使用 ``getfixture`` 辅助函数来使用 fixtures:

        .. code-block:: text

            # content of example.rst
            >>> tmp = getfixture('tmp_path')
            >>> ...
            >>>

        请注意，fixture 需要在 pytest 可见的地方定义，例如 `conftest.py` 文件或插件；正常的 Python 文件中的文档字符串通常不会被扫描以查找 fixtures，除非通过 :confval:`python_files` 明确配置。

        此外，在执行文本 doctest 文件时，支持 :ref:`usefixtures <usefixtures>` 标记和标记为 :ref:`autouse <autouse>` 的 fixtures。

    .. tab:: 英文

        It is possible to use fixtures using the ``getfixture`` helper:

        .. code-block:: text

            # content of example.rst
            >>> tmp = getfixture('tmp_path')
            >>> ...
            >>>

        Note that the fixture needs to be defined in a place visible by pytest, for example, a `conftest.py`
        file or plugin; normal python files containing docstrings are not normally scanned for fixtures
        unless explicitly configured by :confval:`python_files`.

        Also, the :ref:`usefixtures <usefixtures>` mark and fixtures marked as :ref:`autouse <autouse>` are supported
        when executing text doctest files.


.. _`doctest_namespace`:

'doctest_namespace' fixture
^^^^^^^^^^^^^^^^^^^^^^^^^^^

**'doctest_namespace' fixture**

.. tabs::

    .. tab:: 中文

        ``doctest_namespace`` fixture 可用于将项目注入到你的 文档测试(doctests) 运行的命名空间中。它旨在与自己的 fixtures 一起使用，为使用它们的测试提供上下文。

        ``doctest_namespace`` 是一个标准的 ``dict`` 对象，你可以将希望出现在 文档测试(doctest) 命名空间中的对象放入其中：

        .. code-block:: python

            # content of conftest.py
            import pytest
            import numpy


            @pytest.fixture(autouse=True)
            def add_np(doctest_namespace):
                doctest_namespace["np"] = numpy

        然后可以直接在你的 doctests 中使用：

        .. code-block:: python

            # content of numpy.py
            def arange():
                """
                >>> a = np.arange(10)
                >>> len(a)
                10
                """

        请注意，与普通的 ``conftest.py`` 一样，fixtures 会在 conftest 所在的目录树中被发现。这意味着如果你将你的 文档测试(doctest) 和源代码放在一起，相关的 conftest.py 需要在同一目录树中。fixtures 不会在兄弟目录树中被发现！

    .. tab:: 英文

        The ``doctest_namespace`` fixture can be used to inject items into the
        namespace in which your doctests run. It is intended to be used within
        your own fixtures to provide the tests that use them with context.

        ``doctest_namespace`` is a standard ``dict`` object into which you
        place the objects you want to appear in the doctest namespace:

        .. code-block:: python

            # content of conftest.py
            import pytest
            import numpy


            @pytest.fixture(autouse=True)
            def add_np(doctest_namespace):
                doctest_namespace["np"] = numpy

        which can then be used in your doctests directly:

        .. code-block:: python

            # content of numpy.py
            def arange():
                """
                >>> a = np.arange(10)
                >>> len(a)
                10
                """

        Note that like the normal ``conftest.py``, the fixtures are discovered in the directory tree conftest is in.
        Meaning that if you put your doctest with your source code, the relevant conftest.py needs to be in the same directory tree.
        Fixtures will not be discovered in a sibling directory tree!

跳过测试
^^^^^^^^^^^^^^

**Skipping tests**

.. tabs::

    .. tab:: 中文

        出于同样的原因，有人可能想要跳过正常测试，也可以在 文档测试(doctests) 中跳过测试。

        要在 doctest 中跳过单个检查，可以使用标准的 :data:`doctest.SKIP` 指令：

        .. code-block:: python

            def test_random(y):
                """
                >>> random.random()  # doctest: +SKIP
                0.156231223

                >>> 1 + 1
                2
                """

        这将跳过第一个检查，但不会跳过第二个。

        pytest 还允许在 文档测试(doctests) 中使用标准 pytest 函数 :func:`pytest.skip` 和 :func:`pytest.xfail`，这可能很有用，因为这样你可以根据外部条件跳过/预期失败测试：

        .. code-block:: text

            >>> import sys, pytest
            >>> if sys.platform.startswith('win'):
            ...     pytest.skip('this doctest does not work on Windows')
            ...
            >>> import fcntl
            >>> ...

        然而，不鼓励使用这些函数，因为它降低了文档字符串的可读性。

        .. note::

            :func:`pytest.skip` 和 :func:`pytest.xfail` 的行为取决于 doctests 是否在 Python 文件（文档字符串中）或包含文本的文本文件中：

            * Python 模块（文档字符串）：这些函数只在特定的文档字符串中起作用，允许同一模块中的其他文档字符串正常执行。

            * 文本文件：这些函数将跳过/预期失败整个文件的检查。

    .. tab:: 英文

        For the same reasons one might want to skip normal tests, it is also possible to skip tests inside doctests.

        To skip a single check inside a doctest you can use the standard :data:`doctest.SKIP` directive:

        .. code-block:: python

            def test_random(y):
                """
                >>> random.random()  # doctest: +SKIP
                0.156231223

                >>> 1 + 1
                2
                """

        This will skip the first check, but not the second.

        pytest also allows using the standard pytest functions :func:`pytest.skip` and :func:`pytest.xfail` inside doctests, which might be useful because you can then skip/xfail tests based on external conditions:


        .. code-block:: text

            >>> import sys, pytest
            >>> if sys.platform.startswith('win'):
            ...     pytest.skip('this doctest does not work on Windows')
            ...
            >>> import fcntl
            >>> ...

        However using those functions is discouraged because it reduces the readability of the docstring.

        .. note::

            :func:`pytest.skip` and :func:`pytest.xfail` behave differently depending if the doctests are in a Python file (in docstrings) or a text file containing doctests intermingled with text:

            * Python modules (docstrings): the functions only act in that specific docstring, letting the other docstrings in the same module execute as normal.

            * Text files: the functions will skip/xfail the checks for the rest of the entire file.


替代方案
------------

**Alternatives**

.. tabs::

    .. tab:: 中文

        虽然内置的 pytest 支持为使用 文档测试(doctests) 提供了一系列良好的功能，但如果你广泛使用它们，可能会对那些添加更多功能并包含 pytest 集成的外部包感兴趣：

        * `pytest-doctestplus <https://github.com/astropy/pytest-doctestplus>`__: 提供高级的 文档测试(doctest) 支持，并启用对 reStructuredText (".rst") 文件的测试。

        * `Sybil <https://sybil.readthedocs.io>`__：提供了一种通过从文档源解析示例并将解析后的示例作为正常测试运行的一部分进行测试的方法。

    .. tab:: 英文

        While the built-in pytest support provides a good set of functionalities for using doctests, if you use them extensively you might be interested in those external packages which add many more features, and include pytest integration:

        * `pytest-doctestplus <https://github.com/astropy/pytest-doctestplus>`__: provides advanced doctest support and enables the testing of reStructuredText (".rst") files.

        * `Sybil <https://sybil.readthedocs.io>`__: provides a way to test examples in your documentation by parsing them from the documentation source and evaluating the parsed examples as part of your normal test run.
