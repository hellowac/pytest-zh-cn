.. _features:

.. sidebar:: **下次公开培训和活动**

    **Next Open Trainings and Events**

    - `使用 Python 进行专业测试 <https://python-academy.com/courses/python_course_testing.html>`_, 通过 `Python Academy <https://www.python-academy.com/>`_ (3 天深入培训), **2025 年 3 月 4 日至 6 日**, 莱比锡 (DE) / 远程

    另请参阅 :doc:`以前的演讲和博客文章 <talks>`


    - `Professional Testing with Python <https://python-academy.com/courses/python_course_testing.html>`_, via `Python Academy <https://www.python-academy.com/>`_ (3 day in-depth training), **March 4th -- 6th 2025**, Leipzig (DE) / Remote

    Also see :doc:`previous talks and blogposts <talks>`

pytest:帮助你编写更好的程序
=======================================

**pytest: helps you write better programs**

.. toctree::
    :hidden:

    getting-started
    how-to/index
    reference/index
    explanation/index
    example/index

.. toctree::
    :caption: 关于项目
    :hidden:

    changelog
    contributing
    backwards-compatibility
    sponsor
    tidelift
    license
    contact

.. toctree::
    :caption: 游泳的链接
    :hidden:

    pytest @ PyPI <https://pypi.org/project/pytest/>
    pytest @ GitHub <https://github.com/pytest-dev/pytest/>
    Issue Tracker <https://github.com/pytest-dev/pytest/issues>
    PDF Documentation <https://media.readthedocs.org/pdf/pytest/latest/pytest.pdf>

.. module:: pytest

.. tabs::

    .. tab:: 中文

        ``pytest`` 框架可以轻松编写小型、可读的测试，并且可以扩展以支持应用程序和库的复杂功能测试。

        ``pytest`` 需要: Python 3.8+ 或 PyPy3。

        **PyPI 包名称**: :pypi:`pytest`

    .. tab:: 英文

        The ``pytest`` framework makes it easy to write small, readable tests, and can
        scale to support complex functional testing for applications and libraries.


        ``pytest`` requires: Python 3.8+ or PyPy3.

        **PyPI package name**: :pypi:`pytest`

一个简单的例子
---------------

**A quick example**

.. tabs::

    .. tab:: 中文

        .. code-block:: python

            # test_sample.py 的内容
            def inc(x):
                return x + 1


            def test_answer():
                assert inc(3) == 5


        执行它:

        .. code-block:: pytest

            $ pytest
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 1 item

            test_sample.py F                                                     [100%]

            ================================= FAILURES =================================
            _______________________________ test_answer ________________________________

                def test_answer():
            >       assert inc(3) == 5
            E       assert 4 == 5
            E        +  where 4 = inc(3)

            test_sample.py:6: AssertionError
            ========================= short test summary info ==========================
            FAILED test_sample.py::test_answer - assert 4 == 5
            ============================ 1 failed in 0.12s =============================

        由于 ``pytest`` 的详细断言自省，因此仅使用简单的 ``assert`` 语句。
        有关使用 pytest 的基本介绍，请参阅: :ref:`入门 <getstarted>`。

    .. tab:: 英文

        .. code-block:: python

            # content of test_sample.py
            def inc(x):
                return x + 1


            def test_answer():
                assert inc(3) == 5


        To execute it:

        .. code-block:: pytest

            $ pytest
            =========================== test session starts ============================
            platform linux -- Python 3.x.y, pytest-8.x.y, pluggy-1.x.y
            rootdir: /home/sweet/project
            collected 1 item

            test_sample.py F                                                     [100%]

            ================================= FAILURES =================================
            _______________________________ test_answer ________________________________

                def test_answer():
            >       assert inc(3) == 5
            E       assert 4 == 5
            E        +  where 4 = inc(3)

            test_sample.py:6: AssertionError
            ========================= short test summary info ==========================
            FAILED test_sample.py::test_answer - assert 4 == 5
            ============================ 1 failed in 0.12s =============================

        Due to ``pytest``'s detailed assertion introspection, only plain ``assert`` statements are used.
        See :ref:`Get started <getstarted>` for a basic introduction to using pytest.       


功能    
--------

**Features**

.. tabs::

    .. tab:: 中文

        - 有关失败的 :ref:`assert 语句 <assert>` 的详细信息（无需记住 ``self.assert*`` 名称）

        - :ref:`自动发现 <test discovery>` 测试模块和函数

        - :ref:`模块化装置 <fixture>` 用于管理小型或参数化的长寿命测试资源

        - 可以开箱即用地运行 :ref:`unittest <unittest>`（包括试用版）测试套件

        - Python 3.8+ 或 PyPy 3

        - 丰富的插件架构，拥有超过 1300 多个 :ref:`外部插件 <plugin-list>` 和蓬勃发展的社区

    .. tab:: 英文

        - Detailed info on failing :ref:`assert statements <assert>` (no need to remember ``self.assert*`` names)

        - :ref:`Auto-discovery <test discovery>` of test modules and functions

        - :ref:`Modular fixtures <fixture>` for managing small or parametrized long-lived test resources

        - Can run :ref:`unittest <unittest>` (including trial) test suites out of the box

        - Python 3.8+ or PyPy 3

        - Rich plugin architecture, with over 1300+ :ref:`external plugins <plugin-list>` and thriving community


文档
-------------

**Documentation**

.. tabs::

    .. tab:: 中文

        * :ref:`入门 <get-started>` - 只需 20 分钟即可安装 pytest 并掌握其基础知识
        * :ref:`操作指南 <how-to>` - 分步指南，涵盖广泛的用例和需求
        * :ref:`参考指南 <reference>` - 包括完整的 pytest API 参考、插件列表等
        * :ref:`说明 <explanation>` - 背景、关键主题讨论、高级问题的答案

    .. tab:: 英文

        * :ref:`Get started <get-started>` - install pytest and grasp its basics in just twenty minutes
        * :ref:`How-to guides <how-to>` - step-by-step guides, covering a vast range of use-cases and needs
        * :ref:`Reference guides <reference>` - includes the complete pytest API reference, lists of plugins and more
        * :ref:`Explanation <explanation>` - background, discussion of key topics, answers to higher-level questions


bug/请求
-------------

**Bugs/Requests**

.. tabs::

    .. tab:: 中文

        请使用 `GitHub 问题跟踪器 <https://github.com/pytest-dev/pytest/issues>`_ 提交错误或请求功能。

    .. tab:: 英文

        Please use the `GitHub issue tracker <https://github.com/pytest-dev/pytest/issues>`_ to submit bugs or request features.


支持pytest
--------------

**Support pytest**

.. tabs::

    .. tab:: 中文

        `Open Collective`_ 是一个面向开放透明社区的在线融资平台。
        它提供工具来筹集资金并完全透明地分享您的财务状况。

        它是个人和公司向该项目直接进行一次性或每月捐赠的首选平台。

        有关更多详细信息，请参阅 `pytest collective`_。

    .. tab:: 英文

        `Open Collective`_ is an online funding platform for open and transparent communities.
        It provides tools to raise money and share your finances in full transparency.

        It is the platform of choice for individuals and companies that want to make one-time or
        monthly donations directly to the project.

        See more details in the `pytest collective`_.

.. _Open Collective: https://opencollective.com
.. _pytest collective: https://opencollective.com/pytest


企业版pytest
---------------------

**pytest for enterprise**

.. tabs::

    .. tab:: 中文

        作为 Tidelift 订阅的一部分提供。

        pytest 和数千个其他软件包的维护者正在与 Tidelift 合作，为您用于构建应用程序的开源依赖项提供商业支持和
        维护。
        节省时间、降低风险并改善代码健康状况，同时向您使用的确切依赖项的维护者付费。

        `了解更多. <https://tidelift.com/subscription/pkg/pypi-pytest?utm_source=pypi-pytest&utm_medium=referral&utm_campaign=enterprise&utm_term=repo>`_

    .. tab:: 英文

        Available as part of the Tidelift Subscription.

        The maintainers of pytest and thousands of other packages are working with Tidelift to deliver commercial support and
        maintenance for the open source dependencies you use to build your applications.
        Save time, reduce risk, and improve code health, while paying the maintainers of the exact dependencies you use.

        `Learn more. <https://tidelift.com/subscription/pkg/pypi-pytest?utm_source=pypi-pytest&utm_medium=referral&utm_campaign=enterprise&utm_term=repo>`_

安全
~~~~~~~~

**Security**

.. tabs::

    .. tab:: 中文

        pytest 从未与安全漏洞相关联，但无论如何，要报告安全漏洞，请使用 `Tidelift 安全联系人 <https://tidelift.com/security>`_。
        Tidelift 将协调修复和披露。

    .. tab:: 英文

        pytest has never been associated with a security vulnerability, but in any case, to report a
        security vulnerability please use the `Tidelift security contact <https://tidelift.com/security>`_.
        Tidelift will coordinate the fix and disclosure.
