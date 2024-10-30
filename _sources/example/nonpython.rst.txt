
.. _`non-python tests`:

使用非 Python 测试
====================================================

**Working with non-python tests**

.. _`yaml plugin`:

在 Yaml 文件中指定测试的基本示例
--------------------------------------------------------------

.. _`pytest-yamlwsgi`: https://pypi.org/project/pytest-yamlwsgi/

**A basic example for specifying tests in Yaml files**

.. tabs::

    .. tab:: 中文

        这里是一个示例的 ``conftest.py`` （摘自 Ali Afshar 的专用 `pytest-yamlwsgi`_ 插件）。这个 ``conftest.py`` 将收集 ``test*.yaml`` 文件，并将 YAML 格式的内容作为自定义测试执行：

        .. include:: nonpython/conftest.py
            :literal:

        你可以创建一个简单的示例文件：

        .. include:: nonpython/test_simple.yaml
            :literal:

        如果你安装了 :pypi:`PyYAML` 或兼容的 YAML 解析器，你现在可以执行测试规范：

        .. code-block:: pytest

            nonpython $ pytest test_simple.yaml
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project/nonpython
            collected 2 items

            test_simple.yaml F.                                                  [100%]

            ================================= FAILURES =================================
            ______________________________ usecase: hello ______________________________
            usecase execution failed
            spec failed: 'some': 'other'
            no further details known at this point.
            ========================= short test summary info ==========================
            FAILED test_simple.yaml::hello
            ======================= 1 failed, 1 passed in 0.12s ========================

        你得到了一个点表示通过的 ``sub1: sub1`` 检查和一个失败。
        显然，在上述 ``conftest.py`` 中，你会想要实现对 YAML 值更有趣的解释。你可以通过这种方式轻松编写你自己的领域特定测试语言。

        .. note::

            ``repr_failure(excinfo)`` 用于表示测试失败。
            如果你创建自定义收集节点，你可以返回你选择的错误表示字符串。
            它将作为（红色）字符串报告。

        ``reportinfo()`` 用于表示测试位置，并在 ``verbose`` 模式下报告时进行咨询：

        .. code-block:: pytest

            nonpython $ pytest -v
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y -- $PYTHON_PREFIX/bin/python
            cachedir: .pytest_cache
            rootdir: /home/sweet/project/nonpython
            collecting ... collected 2 items

            test_simple.yaml::hello FAILED                                       [ 50%]
            test_simple.yaml::ok PASSED                                          [100%]

            ================================= FAILURES =================================
            ______________________________ usecase: hello ______________________________
            usecase execution failed
            spec failed: 'some': 'other'
            no further details known at this point.
            ========================= short test summary info ==========================
            FAILED test_simple.yaml::hello
            ======================= 1 failed, 1 passed in 0.12s ========================

        在开发自定义测试收集和执行时，查看收集树也很有趣：

        .. code-block:: pytest

            nonpython $ pytest --collect-only
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project/nonpython
            collected 2 items

            <Package nonpython>
            <YamlFile test_simple.yaml>
                <YamlItem hello>
                <YamlItem ok>

            ======================== 2 tests collected in 0.12s ========================

    .. tab:: 英文

        Here is an example ``conftest.py`` (extracted from Ali Afshar's special purpose `pytest-yamlwsgi`_ plugin).   This ``conftest.py`` will  collect ``test*.yaml`` files and will execute the yaml-formatted content as custom tests:

        .. include:: nonpython/conftest.py
            :literal:

        You can create a simple example file:

        .. include:: nonpython/test_simple.yaml
            :literal:

        and if you installed :pypi:`PyYAML` or a compatible YAML-parser you can
        now execute the test specification:

        .. code-block:: pytest

            nonpython $ pytest test_simple.yaml
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project/nonpython
            collected 2 items

            test_simple.yaml F.                                                  [100%]

            ================================= FAILURES =================================
            ______________________________ usecase: hello ______________________________
            usecase execution failed
            spec failed: 'some': 'other'
            no further details known at this point.
            ========================= short test summary info ==========================
            FAILED test_simple.yaml::hello
            ======================= 1 failed, 1 passed in 0.12s ========================

        .. regendoc:wipe

        You get one dot for the passing ``sub1: sub1`` check and one failure.
        Obviously in the above ``conftest.py`` you'll want to implement a more
        interesting interpretation of the yaml-values.  You can easily write
        your own domain specific testing language this way.

        .. note::

            ``repr_failure(excinfo)`` is called for representing test failures.
            If you create custom collection nodes you can return an error
            representation string of your choice.  It
            will be reported as a (red) string.

        ``reportinfo()`` is used for representing the test location and is also
        consulted when reporting in ``verbose`` mode:

        .. code-block:: pytest

            nonpython $ pytest -v
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y -- $PYTHON_PREFIX/bin/python
            cachedir: .pytest_cache
            rootdir: /home/sweet/project/nonpython
            collecting ... collected 2 items

            test_simple.yaml::hello FAILED                                       [ 50%]
            test_simple.yaml::ok PASSED                                          [100%]

            ================================= FAILURES =================================
            ______________________________ usecase: hello ______________________________
            usecase execution failed
            spec failed: 'some': 'other'
            no further details known at this point.
            ========================= short test summary info ==========================
            FAILED test_simple.yaml::hello
            ======================= 1 failed, 1 passed in 0.12s ========================

        .. regendoc:wipe

        While developing your custom test collection and execution it's also
        interesting to just look at the collection tree:

        .. code-block:: pytest

            nonpython $ pytest --collect-only
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project/nonpython
            collected 2 items

            <Package nonpython>
            <YamlFile test_simple.yaml>
                <YamlItem hello>
                <YamlItem ok>

            ======================== 2 tests collected in 0.12s ========================
