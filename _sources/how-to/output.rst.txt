.. _how-to-manage-output:

管理 pytest 的输出
=========================

**Managing pytest's output**

.. _how-to-modifying-python-tb-printing:

修改 Python 回溯打印
--------------------------------------------------

**Modifying Python traceback printing**

.. tabs::

    .. tab:: 中文

        以下是用于修改回溯打印的示例：

        .. code-block:: bash

            pytest --showlocals     # 在回溯中显示局部变量
            pytest -l               # 显示局部变量（快捷方式）
            pytest --no-showlocals  # 隐藏局部变量（如果 addopts 启用了它们）

            pytest --capture=fd     # 默认设置，在文件描述符级别捕获
            pytest --capture=sys    # 在 sys 级别捕获
            pytest --capture=no     # 不捕获
            pytest -s               # 不捕获（快捷方式）
            pytest --capture=tee-sys # 捕获日志并输出到 sys 级别流

            pytest --tb=auto    # （默认）对第一个和最后一个条目使用“长”回溯，
                                # 对其他条目使用“短”格式
            pytest --tb=long    # 全面、信息丰富的回溯格式
            pytest --tb=short   # 更简短的回溯格式
            pytest --tb=line    # 每个失败仅一行
            pytest --tb=native  # Python 标准库格式
            pytest --tb=no      # 不显示回溯

        ``--full-trace`` 选项会在出错时打印出更长的回溯信息（比 ``--tb=long`` 还要长）。它还可以确保在 **KeyboardInterrupt**（Ctrl+C）时打印堆栈跟踪。

        当测试运行时间过长且您通过 Ctrl+C 中断时，这非常有用，以便查明测试*卡在了*哪里。默认情况下，不会显示输出（因为 KeyboardInterrupt 被 pytest 捕获）。使用此选项可以确保显示回溯。

    .. tab:: 英文

        Examples for modifying traceback printing:

        .. code-block:: bash

            pytest --showlocals     # show local variables in tracebacks
            pytest -l               # show local variables (shortcut)
            pytest --no-showlocals  # hide local variables (if addopts enables them)

            pytest --capture=fd  # default, capture at the file descriptor level
            pytest --capture=sys # capture at the sys level
            pytest --capture=no  # don't capture
            pytest -s            # don't capture (shortcut)
            pytest --capture=tee-sys # capture to logs but also output to sys level streams

            pytest --tb=auto    # (default) 'long' tracebacks for the first and last
                                # entry, but 'short' style for the other entries
            pytest --tb=long    # exhaustive, informative traceback formatting
            pytest --tb=short   # shorter traceback format
            pytest --tb=line    # only one line per failure
            pytest --tb=native  # Python standard library formatting
            pytest --tb=no      # no traceback at all

        The ``--full-trace`` causes very long traces to be printed on error (longer
        than ``--tb=long``). It also ensures that a stack trace is printed on
        **KeyboardInterrupt** (Ctrl+C).
        This is very useful if the tests are taking too long and you interrupt them
        with Ctrl+C to find out where the tests are *hanging*. By default no output
        will be shown (because KeyboardInterrupt is caught by pytest). By using this
        option you make sure a trace is shown.


冗长详细
--------------------------------------------------

**Verbosity**

.. tabs::

    .. tab:: 中文

        以下是用于修改打印详细程度的示例：

        .. code-block:: bash

            pytest --quiet          # 安静模式 - 较少的输出
            pytest -q               # 安静模式（快捷方式）
            pytest -v               # 增加详细信息，显示每个测试名称
            pytest -vv              # 更详细，显示更多测试输出细节
            pytest -vvv             # 非标准选项，但在某些配置中可用于显示更多细节

        ``-v`` 标志控制 pytest 输出的详细程度，包括测试会话进度、测试失败时的断言细节、使用 ``--fixtures`` 时的夹具详细信息等。

        .. regendoc:wipe

        以下是一个简单文件的示例：

        .. code-block:: python

            # test_verbosity_example.py 的内容
            def test_ok():
                pass


            def test_words_fail():
                fruits1 = ["banana", "apple", "grapes", "melon", "kiwi"]
                fruits2 = ["banana", "apple", "orange", "melon", "kiwi"]
                assert fruits1 == fruits2


            def test_numbers_fail():
                number_to_text1 = {str(x): x for x in range(5)}
                number_to_text2 = {str(x * 10): x * 10 for x in range(5)}
                assert number_to_text1 == number_to_text2


            def test_long_text_fail():
                long_text = "Lorem ipsum dolor sit amet " * 10
                assert "hello world" in long_text

        正常执行 pytest 会给出以下输出（此处省略了标题以聚焦于主要内容）：

        .. code-block:: pytest

            $ pytest --no-header
            =========================== test session starts ============================
            collected 4 items

            test_verbosity_example.py .FFF                                       [100%]

            ================================= FAILURES =================================
            _____________________________ test_words_fail ______________________________

                def test_words_fail():
                    fruits1 = ["banana", "apple", "grapes", "melon", "kiwi"]
                    fruits2 = ["banana", "apple", "orange", "melon", "kiwi"]
            >       assert fruits1 == fruits2
            E       AssertionError: assert ['banana', 'a...elon', 'kiwi'] == ['banana', 'a...elon', 'kiwi']
            E
            E         在索引 2 处的差异: 'grapes' != 'orange'
            E         使用 -v 获取更多差异信息

            test_verbosity_example.py:8: AssertionError
            ____________________________ test_numbers_fail _____________________________

                def test_numbers_fail():
                    number_to_text1 = {str(x): x for x in range(5)}
                    number_to_text2 = {str(x * 10): x * 10 for x in range(5)}
            >       assert number_to_text1 == number_to_text2
            E       AssertionError: assert {'0': 0, '1':..., '3': 3, ...} == {'0': 0, '10'...'30': 30, ...}
            E
            E         忽略 1 个相同项，使用 -vv 查看
            E         左边包含 4 个额外项:
            E         {'1': 1, '2': 2, '3': 3, '4': 4}
            E         右边包含 4 个额外项:
            E         {'10': 10, '20': 20, '30': 30, '40': 40}
            E         使用 -v 获取更多差异信息

            test_verbosity_example.py:14: AssertionError
            ___________________________ test_long_text_fail ____________________________

                def test_long_text_fail():
                    long_text = "Lorem ipsum dolor sit amet " * 10
            >       assert "hello world" in long_text
            E       AssertionError: assert 'hello world' in 'Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ips... sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet '

            test_verbosity_example.py:19: AssertionError
            ========================= short test summary info ==========================
            FAILED test_verbosity_example.py::test_words_fail - AssertionError: asser...
            FAILED test_verbosity_example.py::test_numbers_fail - AssertionError: ass...
            FAILED test_verbosity_example.py::test_long_text_fail - AssertionError: a...
            ======================= 3 failed, 1 passed in 0.12s ========================

        注意事项：

        * 文件中的每个测试在输出中以单个字符表示：通过的显示为 ``.``, 失败的显示为 ``F``。
        * ``test_words_fail`` 失败了，输出显示两个列表在索引 2 处不相同。
        * ``test_numbers_fail`` 失败了，输出显示字典项在左侧和右侧的差异。相同的项被省略。
        * ``test_long_text_fail`` 失败了，``in`` 语句的右侧被使用 ``...`` 截断，因为其长度超过了内部阈值（当前为 240 个字符）。

        现在我们可以增加 pytest 的详细程度：

        .. code-block:: pytest

            $ pytest --no-header -v
            =========================== test session starts ============================
            collecting ... collected 4 items

            test_verbosity_example.py::test_ok PASSED                            [ 25%]
            test_verbosity_example.py::test_words_fail FAILED                    [ 50%]
            test_verbosity_example.py::test_numbers_fail FAILED                  [ 75%]
            test_verbosity_example.py::test_long_text_fail FAILED                [100%]

            ================================= FAILURES =================================
            _____________________________ test_words_fail ______________________________

                def test_words_fail():
                    fruits1 = ["banana", "apple", "grapes", "melon", "kiwi"]
                    fruits2 = ["banana", "apple", "orange", "melon", "kiwi"]
            >       assert fruits1 == fruits2
            E       AssertionError: assert ['banana', 'a...elon', 'kiwi'] == ['banana', 'a...elon', 'kiwi']
            E
            E         在索引 2 处的差异: 'grapes' != 'orange'
            E
            E         完整差异:
            E           [
            E               'banana',
            E               'apple',...
            E
            E         ...完整输出被截断（隐藏了 7 行），使用 '-vv' 显示

            test_verbosity_example.py:8: AssertionError
            ____________________________ test_numbers_fail _____________________________

                def test_numbers_fail():
                    number_to_text1 = {str(x): x for x in range(5)}
                    number_to_text2 = {str(x * 10): x * 10 for x in range(5)}
            >       assert number_to_text1 == number_to_text2
            E       AssertionError: assert {'0': 0, '1':..., '3': 3, ...} == {'0': 0, '10'...'30': 30, ...}
            E
            E         忽略 1 个相同项，使用 -vv 显示
            E         左边包含 4 个额外项:
            E         {'1': 1, '2': 2, '3': 3, '4': 4}
            E         右边包含 4 个额外项:
            E         {'10': 10, '20': 20, '30': 30, '40': 40}
            E         ...
            E
            E         ...完整输出被截断（隐藏了 16 行），使用 '-vv' 显示

            test_verbosity_example.py:14: AssertionError
            ___________________________ test_long_text_fail ____________________________

                def test_long_text_fail():
                    long_text = "Lorem ipsum dolor sit amet " * 10
            >       assert "hello world" in long_text
            E       AssertionError: assert 'hello world' in 'Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet '

            test_verbosity_example.py:19: AssertionError
            ========================= short test summary info ==========================
            FAILED test_verbosity_example.py::test_words_fail - AssertionError: asser...
            FAILED test_verbosity_example.py::test_numbers_fail - AssertionError: ass...
            FAILED test_verbosity_example.py::test_long_text_fail - AssertionError: a...
            ======================= 3 failed, 1 passed in 0.12s ========================

        现在请注意：

        * 文件中的每个测试在输出中都占据了自己的行。
        * ``test_words_fail`` 现在显示了两个失败列表的完整内容，以及不同的索引。
        * ``test_numbers_fail`` 现在显示了两个字典的文本差异，内容被截断。
        * ``test_long_text_fail`` 不再截断 ``in`` 语句的右侧，因为内部截断阈值现在增大了（当前为 2400 个字符）。

        现在如果我们进一步增加详细程度：

        .. code-block:: pytest

            $ pytest --no-header -vv
            =========================== test session starts ============================
            collecting ... collected 4 items

            test_verbosity_example.py::test_ok PASSED                            [ 25%]
            test_verbosity_example.py::test_words_fail FAILED                    [ 50%]
            test_verbosity_example.py::test_numbers_fail FAILED                  [ 75%]
            test_verbosity_example.py::test_long_text_fail FAILED                [100%]

            ================================= FAILURES =================================
            _____________________________ test_words_fail ______________________________

                def test_words_fail():
                    fruits1 = ["banana", "apple", "grapes", "melon", "kiwi"]
                    fruits2 = ["banana", "apple", "orange", "melon", "kiwi"]
            >       assert fruits1 == fruits2
            E       AssertionError: assert ['banana', 'apple', 'grapes', 'melon', 'kiwi'] == ['banana', 'apple', 'orange', 'melon', 'kiwi']
            E
            E         在索引 2 处的差异: 'grapes' != 'orange'
            E
            E         完整差异:
            E           [
            E               'banana',
            E               'apple',
            E         -     'orange',
            E         ?      ^  ^^
            E         +     'grapes',
            E         ?      ^  ^ +
            E               'melon',
            E               'kiwi',
            E           ]

            test_verbosity_example.py:8: AssertionError
            ____________________________ test_numbers_fail _____________________________

                def test_numbers_fail():
                    number_to_text1 = {str(x): x for x in range(5)}
                    number_to_text2 = {str(x * 10): x * 10 for x in range(5)}
            >       assert number_to_text1 == number_to_text2
            E       AssertionError: assert {'0': 0, '1': 1, '2': 2, '3': 3, '4': 4} == {'0': 0, '10': 10, '20': 20, '30': 30, '40': 40}
            E
            E         共同项:
            E         {'0': 0}
            E         左边包含 4 个额外项:
            E         {'1': 1, '2': 2, '3': 3, '4': 4}
            E         右边包含 4 个额外项:
            E         {'10': 10, '20': 20, '30': 30, '40': 40}
            E
            E         完整差异:
            E           {
            E               '0': 0,
            E         -     '10': 10,
            E         ?       -    -
            E         +     '1': 1,
            E         -     '20': 20,
            E         ?       -    -
            E         +     '2': 2,
            E         -     '30': 30,
            E         ?       -    -
            E         +     '3': 3,
            E         -     '40': 40,
            E         ?       -    -
            E         +     '4': 4,
            E           }

            test_verbosity_example.py:14: AssertionError
            ___________________________ test_long_text_fail ____________________________

                def test_long_text_fail():
                    long_text = "Lorem ipsum dolor sit amet " * 10
            >       assert "hello world" in long_text
            E       AssertionError: assert 'hello world' in 'Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet '

            test_verbosity_example.py:19: AssertionError
            ========================= short test summary info ==========================
            FAILED test_verbosity_example.py::test_words_fail - AssertionError: assert ['banana', 'apple', 'grapes', 'melon', 'kiwi'] == ['banana', 'apple', 'orange', 'melon', 'kiwi']

            在索引 2 处的差异: 'grapes' != 'orange'

            完整差异:
                [
                    'banana',
                    'apple',
            -     'orange',
            ?      ^  ^^
            +     'grapes',
            ?      ^  ^ +
                    'melon',
                    'kiwi',
                ]
            FAILED test_verbosity_example.py::test_numbers_fail - AssertionError: assert {'0': 0, '1': 1, '2': 2, '3': 3, '4': 4} == {'0': 0, '10': 10, '20': 20, '30': 30, '40': 40}

            共同项:
            {'0': 0}
            左边包含 4 个额外项:
            {'1': 1, '2': 2, '3': 3, '4': 4}
            右边包含 4 个额外项:
            {'10': 10, '20': 20, '30': 30, '40': 40}

            完整差异:
                {
                    '0': 0,
            -     '10': 10,
            ?       -    -
            +     '1': 1,
            -     '20': 20,
            ?       -    -
            +     '2': 2,
            -     '30': 30,
            ?       -    -
            +     '3': 3,
            -     '40': 40,
            ?       -    -
            +     '4': 4,
                }
            FAILED test_verbosity_example.py::test_long_text_fail - AssertionError: assert 'hello world' in 'Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet '
            ======================= 3 failed, 1 passed in 0.12s ========================

        现在请注意：

        * 文件中的每个测试在输出中都占据了自己的行。
        * ``test_words_fail`` 在这种情况下给出的输出与之前相同。
        * ``test_numbers_fail`` 现在显示了两个字典的完整文本差异。
        * ``test_long_text_fail`` 也不再在右侧截断，pytest 现在不会截断任何文本，无论其大小。

        这些是详细程度如何影响正常测试会话输出的示例，但详细程度也用于其他情况，例如，如果您使用 ``pytest --fixtures -v``，则会显示以 ``_`` 开头的所有 fixtures。

        支持更高的详细级别（``-vvv``, ``-vvvv``, ...），但目前对 pytest 本身没有影响，然而一些插件可能会利用更高的详细程度。

    .. tab:: 英文

        Examples for modifying printing verbosity:

        .. code-block:: bash

            pytest --quiet          # quiet - less verbose - mode
            pytest -q               # quiet - less verbose - mode (shortcut)
            pytest -v               # increase verbosity, display individual test names
            pytest -vv              # more verbose, display more details from the test output
            pytest -vvv             # not a standard , but may be used for even more detail in certain setups

        The ``-v`` flag controls the verbosity of pytest output in various aspects: test session progress, assertion
        details when tests fail, fixtures details with ``--fixtures``, etc.

        .. regendoc:wipe

        Consider this simple file:

        .. code-block:: python

            # content of test_verbosity_example.py
            def test_ok():
                pass


            def test_words_fail():
                fruits1 = ["banana", "apple", "grapes", "melon", "kiwi"]
                fruits2 = ["banana", "apple", "orange", "melon", "kiwi"]
                assert fruits1 == fruits2


            def test_numbers_fail():
                number_to_text1 = {str(x): x for x in range(5)}
                number_to_text2 = {str(x * 10): x * 10 for x in range(5)}
                assert number_to_text1 == number_to_text2


            def test_long_text_fail():
                long_text = "Lorem ipsum dolor sit amet " * 10
                assert "hello world" in long_text

        Executing pytest normally gives us this output (we are skipping the header to focus on the rest):

        .. code-block:: pytest

            $ pytest --no-header
            =========================== test session starts ============================
            collected 4 items

            test_verbosity_example.py .FFF                                       [100%]

            ================================= FAILURES =================================
            _____________________________ test_words_fail ______________________________

                def test_words_fail():
                    fruits1 = ["banana", "apple", "grapes", "melon", "kiwi"]
                    fruits2 = ["banana", "apple", "orange", "melon", "kiwi"]
            >       assert fruits1 == fruits2
            E       AssertionError: assert ['banana', 'a...elon', 'kiwi'] == ['banana', 'a...elon', 'kiwi']
            E
            E         At index 2 diff: 'grapes' != 'orange'
            E         Use -v to get more diff

            test_verbosity_example.py:8: AssertionError
            ____________________________ test_numbers_fail _____________________________

                def test_numbers_fail():
                    number_to_text1 = {str(x): x for x in range(5)}
                    number_to_text2 = {str(x * 10): x * 10 for x in range(5)}
            >       assert number_to_text1 == number_to_text2
            E       AssertionError: assert {'0': 0, '1':..., '3': 3, ...} == {'0': 0, '10'...'30': 30, ...}
            E
            E         Omitting 1 identical items, use -vv to show
            E         Left contains 4 more items:
            E         {'1': 1, '2': 2, '3': 3, '4': 4}
            E         Right contains 4 more items:
            E         {'10': 10, '20': 20, '30': 30, '40': 40}
            E         Use -v to get more diff

            test_verbosity_example.py:14: AssertionError
            ___________________________ test_long_text_fail ____________________________

                def test_long_text_fail():
                    long_text = "Lorem ipsum dolor sit amet " * 10
            >       assert "hello world" in long_text
            E       AssertionError: assert 'hello world' in 'Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ips... sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet '

            test_verbosity_example.py:19: AssertionError
            ========================= short test summary info ==========================
            FAILED test_verbosity_example.py::test_words_fail - AssertionError: asser...
            FAILED test_verbosity_example.py::test_numbers_fail - AssertionError: ass...
            FAILED test_verbosity_example.py::test_long_text_fail - AssertionError: a...
            ======================= 3 failed, 1 passed in 0.12s ========================

        Notice that:

        * Each test inside the file is shown by a single character in the output: ``.`` for passing, ``F`` for failure.
        * ``test_words_fail`` failed, and we are shown a short summary indicating the index 2 of the two lists differ.
        * ``test_numbers_fail`` failed, and we are shown a summary of left/right differences on dictionary items. Identical items are omitted.
        * ``test_long_text_fail`` failed, and the right hand side of the ``in`` statement is truncated using ``...```
        because it is longer than an internal threshold (240 characters currently).

        Now we can increase pytest's verbosity:

        .. code-block:: pytest

            $ pytest --no-header -v
            =========================== test session starts ============================
            collecting ... collected 4 items

            test_verbosity_example.py::test_ok PASSED                            [ 25%]
            test_verbosity_example.py::test_words_fail FAILED                    [ 50%]
            test_verbosity_example.py::test_numbers_fail FAILED                  [ 75%]
            test_verbosity_example.py::test_long_text_fail FAILED                [100%]

            ================================= FAILURES =================================
            _____________________________ test_words_fail ______________________________

                def test_words_fail():
                    fruits1 = ["banana", "apple", "grapes", "melon", "kiwi"]
                    fruits2 = ["banana", "apple", "orange", "melon", "kiwi"]
            >       assert fruits1 == fruits2
            E       AssertionError: assert ['banana', 'a...elon', 'kiwi'] == ['banana', 'a...elon', 'kiwi']
            E
            E         At index 2 diff: 'grapes' != 'orange'
            E
            E         Full diff:
            E           [
            E               'banana',
            E               'apple',...
            E
            E         ...Full output truncated (7 lines hidden), use '-vv' to show

            test_verbosity_example.py:8: AssertionError
            ____________________________ test_numbers_fail _____________________________

                def test_numbers_fail():
                    number_to_text1 = {str(x): x for x in range(5)}
                    number_to_text2 = {str(x * 10): x * 10 for x in range(5)}
            >       assert number_to_text1 == number_to_text2
            E       AssertionError: assert {'0': 0, '1':..., '3': 3, ...} == {'0': 0, '10'...'30': 30, ...}
            E
            E         Omitting 1 identical items, use -vv to show
            E         Left contains 4 more items:
            E         {'1': 1, '2': 2, '3': 3, '4': 4}
            E         Right contains 4 more items:
            E         {'10': 10, '20': 20, '30': 30, '40': 40}
            E         ...
            E
            E         ...Full output truncated (16 lines hidden), use '-vv' to show

            test_verbosity_example.py:14: AssertionError
            ___________________________ test_long_text_fail ____________________________

                def test_long_text_fail():
                    long_text = "Lorem ipsum dolor sit amet " * 10
            >       assert "hello world" in long_text
            E       AssertionError: assert 'hello world' in 'Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet '

            test_verbosity_example.py:19: AssertionError
            ========================= short test summary info ==========================
            FAILED test_verbosity_example.py::test_words_fail - AssertionError: asser...
            FAILED test_verbosity_example.py::test_numbers_fail - AssertionError: ass...
            FAILED test_verbosity_example.py::test_long_text_fail - AssertionError: a...
            ======================= 3 failed, 1 passed in 0.12s ========================

        Notice now that:

        * Each test inside the file gets its own line in the output.
        * ``test_words_fail`` now shows the two failing lists in full, in addition to which index differs.
        * ``test_numbers_fail`` now shows a text diff of the two dictionaries, truncated.
        * ``test_long_text_fail`` no longer truncates the right hand side of the ``in`` statement, because the internal
        threshold for truncation is larger now (2400 characters currently).

        Now if we increase verbosity even more:

        .. code-block:: pytest

            $ pytest --no-header -vv
            =========================== test session starts ============================
            collecting ... collected 4 items

            test_verbosity_example.py::test_ok PASSED                            [ 25%]
            test_verbosity_example.py::test_words_fail FAILED                    [ 50%]
            test_verbosity_example.py::test_numbers_fail FAILED                  [ 75%]
            test_verbosity_example.py::test_long_text_fail FAILED                [100%]

            ================================= FAILURES =================================
            _____________________________ test_words_fail ______________________________

                def test_words_fail():
                    fruits1 = ["banana", "apple", "grapes", "melon", "kiwi"]
                    fruits2 = ["banana", "apple", "orange", "melon", "kiwi"]
            >       assert fruits1 == fruits2
            E       AssertionError: assert ['banana', 'apple', 'grapes', 'melon', 'kiwi'] == ['banana', 'apple', 'orange', 'melon', 'kiwi']
            E
            E         At index 2 diff: 'grapes' != 'orange'
            E
            E         Full diff:
            E           [
            E               'banana',
            E               'apple',
            E         -     'orange',
            E         ?      ^  ^^
            E         +     'grapes',
            E         ?      ^  ^ +
            E               'melon',
            E               'kiwi',
            E           ]

            test_verbosity_example.py:8: AssertionError
            ____________________________ test_numbers_fail _____________________________

                def test_numbers_fail():
                    number_to_text1 = {str(x): x for x in range(5)}
                    number_to_text2 = {str(x * 10): x * 10 for x in range(5)}
            >       assert number_to_text1 == number_to_text2
            E       AssertionError: assert {'0': 0, '1': 1, '2': 2, '3': 3, '4': 4} == {'0': 0, '10': 10, '20': 20, '30': 30, '40': 40}
            E
            E         Common items:
            E         {'0': 0}
            E         Left contains 4 more items:
            E         {'1': 1, '2': 2, '3': 3, '4': 4}
            E         Right contains 4 more items:
            E         {'10': 10, '20': 20, '30': 30, '40': 40}
            E
            E         Full diff:
            E           {
            E               '0': 0,
            E         -     '10': 10,
            E         ?       -    -
            E         +     '1': 1,
            E         -     '20': 20,
            E         ?       -    -
            E         +     '2': 2,
            E         -     '30': 30,
            E         ?       -    -
            E         +     '3': 3,
            E         -     '40': 40,
            E         ?       -    -
            E         +     '4': 4,
            E           }

            test_verbosity_example.py:14: AssertionError
            ___________________________ test_long_text_fail ____________________________

                def test_long_text_fail():
                    long_text = "Lorem ipsum dolor sit amet " * 10
            >       assert "hello world" in long_text
            E       AssertionError: assert 'hello world' in 'Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet '

            test_verbosity_example.py:19: AssertionError
            ========================= short test summary info ==========================
            FAILED test_verbosity_example.py::test_words_fail - AssertionError: assert ['banana', 'apple', 'grapes', 'melon', 'kiwi'] == ['banana', 'apple', 'orange', 'melon', 'kiwi']

            At index 2 diff: 'grapes' != 'orange'

            Full diff:
                [
                    'banana',
                    'apple',
            -     'orange',
            ?      ^  ^^
            +     'grapes',
            ?      ^  ^ +
                    'melon',
                    'kiwi',
                ]
            FAILED test_verbosity_example.py::test_numbers_fail - AssertionError: assert {'0': 0, '1': 1, '2': 2, '3': 3, '4': 4} == {'0': 0, '10': 10, '20': 20, '30': 30, '40': 40}

            Common items:
            {'0': 0}
            Left contains 4 more items:
            {'1': 1, '2': 2, '3': 3, '4': 4}
            Right contains 4 more items:
            {'10': 10, '20': 20, '30': 30, '40': 40}

            Full diff:
                {
                    '0': 0,
            -     '10': 10,
            ?       -    -
            +     '1': 1,
            -     '20': 20,
            ?       -    -
            +     '2': 2,
            -     '30': 30,
            ?       -    -
            +     '3': 3,
            -     '40': 40,
            ?       -    -
            +     '4': 4,
                }
            FAILED test_verbosity_example.py::test_long_text_fail - AssertionError: assert 'hello world' in 'Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet Lorem ipsum dolor sit amet '
            ======================= 3 failed, 1 passed in 0.12s ========================

        Notice now that:

        * Each test inside the file gets its own line in the output.
        * ``test_words_fail`` gives the same output as before in this case.
        * ``test_numbers_fail`` now shows a full text diff of the two dictionaries.
        * ``test_long_text_fail`` also doesn't truncate on the right hand side as before, but now pytest won't truncate any
        text at all, regardless of its size.

        Those were examples of how verbosity affects normal test session output, but verbosity also is used in other
        situations, for example you are shown even fixtures that start with ``_`` if you use ``pytest --fixtures -v``.

        Using higher verbosity levels (``-vvv``, ``-vvvv``, ...) is supported, but has no effect in pytest itself at the moment,
        however some plugins might make use of higher verbosity.

.. _`pytest.fine_grained_verbosity`:

细粒度的详细程度
~~~~~~~~~~~~~~~~~~~~~~

**Fine-grained verbosity**

.. tabs::

    .. tab:: 中文

        除了指定应用程序的全局详细程度外，还可以独立控制特定方面。这是通过在配置文件中为输出的特定方面设置详细程度级别来完成的。

        :confval:`verbosity_assertions`: 控制在执行 pytest 时断言输出的详细程度。使用值为 ``2`` 运行 ``pytest --no-header`` 将产生与前一个示例相同的输出，但文件中的每个测试在输出中仅用一个字符表示。

        :confval:`verbosity_test_cases`: 控制在执行 pytest 时测试执行输出的详细程度。使用值为 ``2`` 运行 ``pytest --no-header`` 将产生与第一个详细程度示例相同的输出，但文件中的每个测试在输出中都占据了自己的一行。

    .. tab:: 英文

        In addition to specifying the application wide verbosity level, it is possible to control specific aspects independently.
        This is done by setting a verbosity level in the configuration file for the specific aspect of the output.

        :confval:`verbosity_assertions`: Controls how verbose the assertion output should be when pytest is executed. Running
        ``pytest --no-header`` with a value of ``2`` would have the same output as the previous example, but each test inside
        the file is shown by a single character in the output.

        :confval:`verbosity_test_cases`: Controls how verbose the test execution output should be when pytest is executed.
        Running ``pytest --no-header`` with a value of ``2`` would have the same output as the first verbosity example, but each
        test inside the file gets its own line in the output.

.. _`pytest.detailed_failed_tests_usage`:

制作详细的摘要报告
--------------------------------------------------

**Producing a detailed summary report**

.. tabs::

    .. tab:: 中文

        ``-r`` 标志可用于在测试会话结束时显示“短测试摘要信息”，使得在大型测试套件中轻松获取所有失败、跳过、预期失败等情况的清晰图像。

        默认情况下，它为 ``fE``，用于列出失败和错误。

        .. regendoc:wipe

        示例：

        .. code-block:: python

            # content of test_example.py
            import pytest


            @pytest.fixture
            def error_fixture():
                assert 0


            def test_ok():
                print("ok")


            def test_fail():
                assert 0


            def test_error(error_fixture):
                pass


            def test_skip():
                pytest.skip("skipping this test")


            def test_xfail():
                pytest.xfail("xfailing this test")


            @pytest.mark.xfail(reason="always xfail")
            def test_xpass():
                pass


        .. code-block:: pytest

            $ pytest -ra
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 6 items

            test_example.py .FEsxX                                               [100%]

            ================================== ERRORS ==================================
            _______________________ ERROR at setup of test_error _______________________

                @pytest.fixture
                def error_fixture():
            >       assert 0
            E       assert 0

            test_example.py:6: AssertionError
            ================================= FAILURES =================================
            ________________________________ test_fail _________________________________

                def test_fail():
            >       assert 0
            E       assert 0

            test_example.py:14: AssertionError
            ================================= XPASSES ==================================
            ========================= short test summary info ==========================
            SKIPPED [1] test_example.py:22: skipping this test
            XFAIL test_example.py::test_xfail - reason: xfailing this test
            XPASS test_example.py::test_xpass - always xfail
            ERROR test_example.py::test_error - assert 0
            FAILED test_example.py::test_fail - assert 0
            == 1 failed, 1 passed, 1 skipped, 1 xfailed, 1 xpassed, 1 error in 0.12s ===

        ``-r`` 选项接受多个字符，前面的 ``a`` 意味着“除了通过的测试”。

        以下是可用字符的完整列表：

        - ``f`` - 失败
        - ``E`` - 错误
        - ``s`` - 跳过
        - ``x`` - 预期失败
        - ``X`` - 预期通过
        - ``p`` - 通过
        - ``P`` - 带输出的通过

        用于（去）选择组的特殊字符：

        - ``a`` - 除了 ``pP`` 的所有
        - ``A`` - 所有
        - ``N`` - 无，这可以用来显示无（因为 ``fE`` 是默认值）

        可以使用多个字符，因此例如要仅查看失败和跳过的测试，可以执行：

        .. code-block:: pytest

            $ pytest -rfs
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 6 items

            test_example.py .FEsxX                                               [100%]

            ================================== ERRORS ==================================
            _______________________ ERROR at setup of test_error _______________________

                @pytest.fixture
                def error_fixture():
            >       assert 0
            E       assert 0

            test_example.py:6: AssertionError
            ================================= FAILURES =================================
            ________________________________ test_fail _________________________________

                def test_fail():
            >       assert 0
            E       assert 0

            test_example.py:14: AssertionError
            ========================= short test summary info ==========================
            FAILED test_example.py::test_fail - assert 0
            SKIPPED [1] test_example.py:22: skipping this test
            == 1 failed, 1 passed, 1 skipped, 1 xfailed, 1 xpassed, 1 error in 0.12s ===

        使用 ``p`` 列出通过的测试，而 ``P`` 则添加额外的“通过”部分，包含那些通过但有捕获输出的测试：

        .. code-block:: pytest

            $ pytest -rpP
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 6 items

            test_example.py .FEsxX                                               [100%]

            ================================== ERRORS ==================================
            _______________________ ERROR at setup of test_error _______________________

                @pytest.fixture
                def error_fixture():
            >       assert 0
            E       assert 0

            test_example.py:6: AssertionError
            ================================= FAILURES =================================
            ________________________________ test_fail _________________________________

                def test_fail():
            >       assert 0
            E       assert 0

            test_example.py:14: AssertionError
            ================================== PASSES ==================================
            _________________________________ test_ok __________________________________
            --------------------------- Captured stdout call ---------------------------
            ok
            ========================= short test summary info ==========================
            PASSED test_example.py::test_ok
            == 1 failed, 1 passed, 1 skipped, 1 xfailed, 1 xpassed, 1 error in 0.12s ===

        .. note::

            默认情况下，如果跳过测试的参数化变体共享相同的跳过原因，则它们会被分组在一起。您可以使用 ``--no-fold-skipped`` 单独打印每个跳过的测试。

    .. tab:: 英文

        The ``-r`` flag can be used to display a "short test summary info" at the end of the test session,
        making it easy in large test suites to get a clear picture of all failures, skips, xfails, etc.

        It defaults to ``fE`` to list failures and errors.

        .. regendoc:wipe

        Example:

        .. code-block:: python

            # content of test_example.py
            import pytest


            @pytest.fixture
            def error_fixture():
                assert 0


            def test_ok():
                print("ok")


            def test_fail():
                assert 0


            def test_error(error_fixture):
                pass


            def test_skip():
                pytest.skip("skipping this test")


            def test_xfail():
                pytest.xfail("xfailing this test")


            @pytest.mark.xfail(reason="always xfail")
            def test_xpass():
                pass


        .. code-block:: pytest

            $ pytest -ra
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 6 items

            test_example.py .FEsxX                                               [100%]

            ================================== ERRORS ==================================
            _______________________ ERROR at setup of test_error _______________________

                @pytest.fixture
                def error_fixture():
            >       assert 0
            E       assert 0

            test_example.py:6: AssertionError
            ================================= FAILURES =================================
            ________________________________ test_fail _________________________________

                def test_fail():
            >       assert 0
            E       assert 0

            test_example.py:14: AssertionError
            ================================= XPASSES ==================================
            ========================= short test summary info ==========================
            SKIPPED [1] test_example.py:22: skipping this test
            XFAIL test_example.py::test_xfail - reason: xfailing this test
            XPASS test_example.py::test_xpass - always xfail
            ERROR test_example.py::test_error - assert 0
            FAILED test_example.py::test_fail - assert 0
            == 1 failed, 1 passed, 1 skipped, 1 xfailed, 1 xpassed, 1 error in 0.12s ===

        The ``-r`` options accepts a number of characters after it, with ``a`` used
        above meaning "all except passes".

        Here is the full list of available characters that can be used:

        - ``f`` - failed
        - ``E`` - error
        - ``s`` - skipped
        - ``x`` - xfailed
        - ``X`` - xpassed
        - ``p`` - passed
        - ``P`` - passed with output

        Special characters for (de)selection of groups:

        - ``a`` - all except ``pP``
        - ``A`` - all
        - ``N`` - none, this can be used to display nothing (since ``fE`` is the default)

        More than one character can be used, so for example to only see failed and skipped tests, you can execute:

        .. code-block:: pytest

            $ pytest -rfs
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 6 items

            test_example.py .FEsxX                                               [100%]

            ================================== ERRORS ==================================
            _______________________ ERROR at setup of test_error _______________________

                @pytest.fixture
                def error_fixture():
            >       assert 0
            E       assert 0

            test_example.py:6: AssertionError
            ================================= FAILURES =================================
            ________________________________ test_fail _________________________________

                def test_fail():
            >       assert 0
            E       assert 0

            test_example.py:14: AssertionError
            ========================= short test summary info ==========================
            FAILED test_example.py::test_fail - assert 0
            SKIPPED [1] test_example.py:22: skipping this test
            == 1 failed, 1 passed, 1 skipped, 1 xfailed, 1 xpassed, 1 error in 0.12s ===

        Using ``p`` lists the passing tests, whilst ``P`` adds an extra section "PASSES" with those tests that passed but had
        captured output:

        .. code-block:: pytest

            $ pytest -rpP
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 6 items

            test_example.py .FEsxX                                               [100%]

            ================================== ERRORS ==================================
            _______________________ ERROR at setup of test_error _______________________

                @pytest.fixture
                def error_fixture():
            >       assert 0
            E       assert 0

            test_example.py:6: AssertionError
            ================================= FAILURES =================================
            ________________________________ test_fail _________________________________

                def test_fail():
            >       assert 0
            E       assert 0

            test_example.py:14: AssertionError
            ================================== PASSES ==================================
            _________________________________ test_ok __________________________________
            --------------------------- Captured stdout call ---------------------------
            ok
            ========================= short test summary info ==========================
            PASSED test_example.py::test_ok
            == 1 failed, 1 passed, 1 skipped, 1 xfailed, 1 xpassed, 1 error in 0.12s ===

        .. note::

            By default, parametrized variants of skipped tests are grouped together if
            they share the same skip reason. You can use ``--no-fold-skipped`` to print each skipped test separately.


.. _truncation-params:

修改截断限制
--------------------------------------------------

**Modifying truncation limits**

.. tabs::

    .. tab:: 中文

        .. versionadded: 8.4

        默认的截断限制为 8 行或 640 个字符，以先到者为准。要设置自定义截断限制，您可以使用以下 ``pytest.ini`` 文件选项：

        .. code-block:: ini

            [pytest]
            truncation_limit_lines = 10
            truncation_limit_chars = 90

        这将导致 pytest 将断言截断为 10 行或 90 个字符，以先到者为准。

        将 :confval:`truncation_limit_lines` 和 :confval:`truncation_limit_chars` 均设置为 ``0`` 将禁用截断。但是，仅设置其中一个值将禁用一种截断模式，而保留另一种模式。

    .. tab:: 英文

        .. versionadded: 8.4

        Default truncation limits are 8 lines or 640 characters, whichever comes first.
        To set custom truncation limits you can use following ``pytest.ini`` file options:

        .. code-block:: ini

            [pytest]
            truncation_limit_lines = 10
            truncation_limit_chars = 90

        That will cause pytest to truncate the assertions to 10 lines or 90 characters, whichever comes first.

        Setting both :confval:`truncation_limit_lines` and :confval:`truncation_limit_chars` to ``0`` will disable the truncation.
        However, setting only one of those values will disable one truncation mode, but will leave the other one intact.

创建结果日志格式文件
--------------------------------------------------

**Creating resultlog format files**

.. tabs::

    .. tab:: 中文

        要创建纯文本的机器可读结果文件，您可以执行：

        .. code-block:: bash

            pytest --resultlog=path

        然后查看 ``path`` 位置的内容。这种文件被例如 `PyPy-test`_ 网页用于显示多个版本的测试结果。

        .. warning::

            此选项很少使用，并计划在 pytest 6.0 中移除。

            如果您使用此选项，请考虑使用新的 `pytest-reportlog <https://github.com/pytest-dev/pytest-reportlog>`__ 插件。

            有关更多信息，请参见 :ref:`弃用文档 <resultlog deprecated>`。

    .. tab:: 英文

        To create plain-text machine-readable result files you can issue:

        .. code-block:: bash

            pytest --resultlog=path

        and look at the content at the ``path`` location.  Such files are used e.g.
        by the `PyPy-test`_ web page to show test results over several revisions.

        .. warning::

            This option is rarely used and is scheduled for removal in pytest 6.0.

            If you use this option, consider using the new `pytest-reportlog <https://github.com/pytest-dev/pytest-reportlog>`__ plugin instead.

            See :ref:`the deprecation docs <resultlog deprecated>` for more information.


.. _`PyPy-test`: http://buildbot.pypy.org/summary


创建 JUnitXML 格式文件
----------------------------------------------------

**Creating JUnitXML format files**

.. tabs::

    .. tab:: 中文

        要创建可以被 Jenkins_ 或其他持续集成服务器读取的结果文件，请使用以下命令：

        .. code-block:: bash

            pytest --junit-xml=path

        在 ``path`` 位置创建一个 XML 文件。

        要设置根测试套件 XML 项的名称，您可以在配置文件中配置 ``junit_suite_name`` 选项：

        .. code-block:: ini

            [pytest]
            junit_suite_name = my_suite

        .. versionadded:: 4.0

        JUnit XML 规范似乎指示 ``"time"`` 属性应报告总的测试执行时间，包括设置和拆卸
        (`1 <http://windyroad.com.au/dl/Open%20Source/JUnit.xsd>`_, `2
        <https://www.ibm.com/support/knowledgecenter/en/SSQ2R2_14.1.0/com.ibm.rsar.analysis.codereview.cobol.doc/topics/cac_useresults_junit.html>`_)。
        这是 pytest 的默认行为。要仅报告调用持续时间，请像这样配置 ``junit_duration_report`` 选项：

        .. code-block:: ini

            [pytest]
            junit_duration_report = call

    .. tab:: 英文

        To create result files which can be read by Jenkins_ or other Continuous
        integration servers, use this invocation:

        .. code-block:: bash

            pytest --junit-xml=path

        to create an XML file at ``path``.

        To set the name of the root test suite xml item, you can configure the ``junit_suite_name`` option in your config file:

        .. code-block:: ini

            [pytest]
            junit_suite_name = my_suite

        .. versionadded:: 4.0

        JUnit XML specification seems to indicate that ``"time"`` attribute
        should report total test execution times, including setup and teardown
        (`1 <http://windyroad.com.au/dl/Open%20Source/JUnit.xsd>`_, `2
        <https://www.ibm.com/support/knowledgecenter/en/SSQ2R2_14.1.0/com.ibm.rsar.analysis.codereview.cobol.doc/topics/cac_useresults_junit.html>`_).
        It is the default pytest behavior. To report just call durations
        instead, configure the ``junit_duration_report`` option like this:

        .. code-block:: ini

            [pytest]
            junit_duration_report = call

.. _record_property example:

``record_property`` fixture
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**record_property**

.. tabs::

    .. tab:: 中文

        如果您想为测试记录额外的信息，可以使用 ``record_property`` 固件：

        .. code-block:: python

            def test_function(record_property):
                record_property("example_key", 1)
                assert True

        这将向生成的 ``testcase`` 标签添加一个额外的属性 ``example_key="1"``：

        .. code-block:: xml

            <testcase classname="test_function" file="test_function.py" line="0" name="test_function" time="0.0009">
            <properties>
                <property name="example_key" value="1" />
            </properties>
            </testcase>

        另外，您可以将此功能与自定义标记集成：

        .. code-block:: python

            # content of conftest.py

            def pytest_collection_modifyitems(session, config, items):
                for item in items:
                    for marker in item.iter_markers(name="test_id"):
                        test_id = marker.args[0]
                        item.user_properties.append(("test_id", test_id))

        在您的测试中：

        .. code-block:: python

            # content of test_function.py
            import pytest

            @pytest.mark.test_id(1501)
            def test_function():
                assert True

        将会得到：

        .. code-block:: xml

            <testcase classname="test_function" file="test_function.py" line="0" name="test_function" time="0.0009">
            <properties>
                <property name="test_id" value="1501" />
            </properties>
            </testcase>

        .. warning::

            请注意，使用此功能将会破坏最新 JUnitXML 模式的模式验证。
            这在某些 CI 服务器上使用时可能会成为一个问题。

    .. tab:: 英文

        If you want to log additional information for a test, you can use the
        ``record_property`` fixture:

        .. code-block:: python

            def test_function(record_property):
                record_property("example_key", 1)
                assert True

        This will add an extra property ``example_key="1"`` to the generated
        ``testcase`` tag:

        .. code-block:: xml

            <testcase classname="test_function" file="test_function.py" line="0" name="test_function" time="0.0009">
            <properties>
                <property name="example_key" value="1" />
            </properties>
            </testcase>

        Alternatively, you can integrate this functionality with custom markers:

        .. code-block:: python

            # content of conftest.py


            def pytest_collection_modifyitems(session, config, items):
                for item in items:
                    for marker in item.iter_markers(name="test_id"):
                        test_id = marker.args[0]
                        item.user_properties.append(("test_id", test_id))

        And in your tests:

        .. code-block:: python

            # content of test_function.py
            import pytest


            @pytest.mark.test_id(1501)
            def test_function():
                assert True

        Will result in:

        .. code-block:: xml

            <testcase classname="test_function" file="test_function.py" line="0" name="test_function" time="0.0009">
            <properties>
                <property name="test_id" value="1501" />
            </properties>
            </testcase>

        .. warning::

            Please note that using this feature will break schema verifications for the latest JUnitXML schema.
            This might be a problem when used with some CI servers.


``record_xml_attribute`` fixture
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**record_xml_attribute**

.. tabs::

    .. tab:: 中文

        要向 testcase 元素添加额外的 xml 属性，可以使用 ``record_xml_attribute`` 固件。这也可以用来覆盖现有值：

        .. code-block:: python

            def test_function(record_xml_attribute):
                record_xml_attribute("assertions", "REQ-1234")
                record_xml_attribute("classname", "custom_classname")
                print("hello world")
                assert True

        与 ``record_property`` 不同，这不会添加一个新的子元素。
        相反，这将在生成的 ``testcase`` 标签内添加一个属性 ``assertions="REQ-1234"``，并将默认的 ``classname`` 替换为 ``"classname=custom_classname"``：

        .. code-block:: xml

            <testcase classname="custom_classname" file="test_function.py" line="0" name="test_function" time="0.003" assertions="REQ-1234">
                <system-out>
                    hello world
                </system-out>
            </testcase>

        .. warning::

            ``record_xml_attribute`` 是一个实验性功能，其接口可能在未来版本中被更强大和通用的内容所替代。
            然而，该功能本身将被保留。

            使用这个功能而不是 ``record_xml_property`` 可以在使用 ci 工具解析 xml 报告时提供帮助。
            然而，一些解析器对于允许的元素和属性非常严格。
            许多工具使用 xsd 模式（如下例所示）来验证传入的 xml。
            请确保您使用的属性名称是解析器允许的。

            以下是 Jenkins 用于验证 XML 报告的方案：

            .. code-block:: xml

                <xs:element name="testcase">
                    <xs:complexType>
                        <xs:sequence>
                            <xs:element ref="skipped" minOccurs="0" maxOccurs="1"/>
                            <xs:element ref="error" minOccurs="0" maxOccurs="unbounded"/>
                            <xs:element ref="failure" minOccurs="0" maxOccurs="unbounded"/>
                            <xs:element ref="system-out" minOccurs="0" maxOccurs="unbounded"/>
                            <xs:element ref="system-err" minOccurs="0" maxOccurs="unbounded"/>
                        </xs:sequence>
                        <xs:attribute name="name" type="xs:string" use="required"/>
                        <xs:attribute name="assertions" type="xs:string" use="optional"/>
                        <xs:attribute name="time" type="xs:string" use="optional"/>
                        <xs:attribute name="classname" type="xs:string" use="optional"/>
                        <xs:attribute name="status" type="xs:string" use="optional"/>
                    </xs:complexType>
                </xs:element>

        .. warning::

            请注意，使用此功能将会破坏最新 JUnitXML 模式的模式验证。
            这在某些 CI 服务器上使用时可能会成为一个问题。

    .. tab:: 英文

        To add an additional xml attribute to a testcase element, you can use
        ``record_xml_attribute`` fixture. This can also be used to override existing values:

        .. code-block:: python

            def test_function(record_xml_attribute):
                record_xml_attribute("assertions", "REQ-1234")
                record_xml_attribute("classname", "custom_classname")
                print("hello world")
                assert True

        Unlike ``record_property``, this will not add a new child element.
        Instead, this will add an attribute ``assertions="REQ-1234"`` inside the generated
        ``testcase`` tag and override the default ``classname`` with ``"classname=custom_classname"``:

        .. code-block:: xml

            <testcase classname="custom_classname" file="test_function.py" line="0" name="test_function" time="0.003" assertions="REQ-1234">
                <system-out>
                    hello world
                </system-out>
            </testcase>

        .. warning::

            ``record_xml_attribute`` is an experimental feature, and its interface might be replaced
            by something more powerful and general in future versions. The
            functionality per-se will be kept, however.

            Using this over ``record_xml_property`` can help when using ci tools to parse the xml report.
            However, some parsers are quite strict about the elements and attributes that are allowed.
            Many tools use an xsd schema (like the example below) to validate incoming xml.
            Make sure you are using attribute names that are allowed by your parser.

            Below is the Scheme used by Jenkins to validate the XML report:

            .. code-block:: xml

                <xs:element name="testcase">
                    <xs:complexType>
                        <xs:sequence>
                            <xs:element ref="skipped" minOccurs="0" maxOccurs="1"/>
                            <xs:element ref="error" minOccurs="0" maxOccurs="unbounded"/>
                            <xs:element ref="failure" minOccurs="0" maxOccurs="unbounded"/>
                            <xs:element ref="system-out" minOccurs="0" maxOccurs="unbounded"/>
                            <xs:element ref="system-err" minOccurs="0" maxOccurs="unbounded"/>
                        </xs:sequence>
                        <xs:attribute name="name" type="xs:string" use="required"/>
                        <xs:attribute name="assertions" type="xs:string" use="optional"/>
                        <xs:attribute name="time" type="xs:string" use="optional"/>
                        <xs:attribute name="classname" type="xs:string" use="optional"/>
                        <xs:attribute name="status" type="xs:string" use="optional"/>
                    </xs:complexType>
                </xs:element>

        .. warning::

            Please note that using this feature will break schema verifications for the latest JUnitXML schema.
            This might be a problem when used with some CI servers.

.. _record_testsuite_property example:

``record_testsuite_property`` fixture
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**record_testsuite_property**

.. tabs::

    .. tab:: 中文

        .. versionadded:: 4.5

        如果您想在测试套件级别添加一个属性节点，该节点可能包含与所有测试相关的属性，可以使用 ``record_testsuite_property`` 会话范围的固件：

        ``record_testsuite_property`` 会话范围的固件可用于添加与所有测试相关的属性。

        .. code-block:: python

            import pytest


            @pytest.fixture(scope="session", autouse=True)
            def log_global_env_facts(record_testsuite_property):
                record_testsuite_property("ARCH", "PPC")
                record_testsuite_property("STORAGE_TYPE", "CEPH")


            class TestMe:
                def test_foo(self):
                    assert True

        该固件是一个可调用对象，接收 ``<property>`` 标签的 ``name`` 和 ``value``，这些标签在生成的 XML 的测试套件级别添加：

        .. code-block:: xml

            <testsuite errors="0" failures="0" name="pytest" skipped="0" tests="1" time="0.006">
            <properties>
                <property name="ARCH" value="PPC"/>
                <property name="STORAGE_TYPE" value="CEPH"/>
            </properties>
            <testcase classname="test_me.TestMe" file="test_me.py" line="16" name="test_foo" time="0.000243663787842"/>
            </testsuite>

        ``name`` 必须是一个字符串，``value`` 将被转换为字符串并正确地进行 XML 转义。

        生成的 XML 与最新的 ``xunit`` 标准兼容，这与 `record_property`_ 和 `record_xml_attribute`_ 相反。

    .. tab:: 英文

        .. versionadded:: 4.5

        If you want to add a properties node at the test-suite level, which may contains properties that are relevant to all tests, you can use the ``record_testsuite_property`` session-scoped fixture:

        The ``record_testsuite_property`` session-scoped fixture can be used to add properties relevant to all tests.

        .. code-block:: python

            import pytest


            @pytest.fixture(scope="session", autouse=True)
            def log_global_env_facts(record_testsuite_property):
                record_testsuite_property("ARCH", "PPC")
                record_testsuite_property("STORAGE_TYPE", "CEPH")


            class TestMe:
                def test_foo(self):
                    assert True

        The fixture is a callable which receives ``name`` and ``value`` of a ``<property>`` tag added at the test-suite level of the generated xml:

        .. code-block:: xml

            <testsuite errors="0" failures="0" name="pytest" skipped="0" tests="1" time="0.006">
            <properties>
                <property name="ARCH" value="PPC"/>
                <property name="STORAGE_TYPE" value="CEPH"/>
            </properties>
            <testcase classname="test_me.TestMe" file="test_me.py" line="16" name="test_foo" time="0.000243663787842"/>
            </testsuite>

        ``name`` must be a string, ``value`` will be converted to a string and properly xml-escaped.

        The generated XML is compatible with the latest ``xunit`` standard, contrary to `record_property`_ and `record_xml_attribute`_.


将测试报告发送至在线 pastebin 服务
--------------------------------------------------

**Sending test report to an online pastebin service**

.. tabs::

    .. tab:: 中文

        **为每个测试失败创建 URL**:

        .. code-block:: bash

            pytest --pastebin=failed

        这将把测试运行信息提交到远程 Paste 服务，并为每个失败提供一个 URL。您可以像往常一样选择测试，或者例如添加 ``-x``，如果您只想发送一个特定的失败。

        **为整个测试会话日志创建 URL**:

        .. code-block:: bash

            pytest --pastebin=all

        目前只实现了将内容粘贴到 https://bpaste.net/ 服务。

        .. versionchanged:: 5.2

        如果因任何原因创建 URL 失败，将生成警告，而不是使整个测试套件失败。

    .. tab:: 英文

        **Creating a URL for each test failure**:

        .. code-block:: bash

            pytest --pastebin=failed

        This will submit test run information to a remote Paste service and provide a URL for each failure.  You may select tests as usual or add for example ``-x`` if you only want to send one particular failure.

        **Creating a URL for a whole test session log**:

        .. code-block:: bash

            pytest --pastebin=all

        Currently only pasting to the https://bpaste.net/ service is implemented.

        .. versionchanged:: 5.2

        If creating the URL fails for any reason, a warning is generated instead of failing the entire test suite.

.. _jenkins: https://jenkins-ci.org
