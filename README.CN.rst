.. image:: https://github.com/pytest-dev/pytest/raw/main/doc/en/img/pytest_logo_curves.svg
   :target: https://docs.pytest.org/en/stable/
   :align: center
   :height: 200
   :alt: pytest


------

.. image:: https://img.shields.io/pypi/v/pytest.svg
    :target: https://pypi.org/project/pytest/

.. image:: https://img.shields.io/conda/vn/conda-forge/pytest.svg
    :target: https://anaconda.org/conda-forge/pytest

.. image:: https://img.shields.io/pypi/pyversions/pytest.svg
    :target: https://pypi.org/project/pytest/

.. image:: https://codecov.io/gh/pytest-dev/pytest/branch/main/graph/badge.svg
    :target: https://codecov.io/gh/pytest-dev/pytest
    :alt: Code coverage Status

.. image:: https://github.com/pytest-dev/pytest/actions/workflows/test.yml/badge.svg
    :target: https://github.com/pytest-dev/pytest/actions?query=workflow%3Atest

.. image:: https://results.pre-commit.ci/badge/github/pytest-dev/pytest/main.svg
   :target: https://results.pre-commit.ci/latest/github/pytest-dev/pytest/main
   :alt: pre-commit.ci status

.. image:: https://www.codetriage.com/pytest-dev/pytest/badges/users.svg
    :target: https://www.codetriage.com/pytest-dev/pytest

.. image:: https://readthedocs.org/projects/pytest/badge/?version=latest
    :target: https://pytest.readthedocs.io/en/latest/?badge=latest
    :alt: Documentation Status

.. image:: https://img.shields.io/badge/Discord-pytest--dev-blue
    :target: https://discord.com/invite/pytest-dev
    :alt: Discord

.. image:: https://img.shields.io/badge/Libera%20chat-%23pytest-orange
    :target: https://web.libera.chat/#pytest
    :alt: Libera chat


``pytest`` 框架使得编写小型测试变得简单，同时也能扩展以支持复杂的应用程序和库的功能测试。

一个简单测试的示例：

.. code-block:: python

    # test_sample.py 的内容
    def inc(x):
        return x + 1


    def test_answer():
        assert inc(3) == 5


执行它::

    $ pytest
    ============================= 测试会话开始 =============================
    收集到 1 个项目

    test_sample.py F

    ================================== 失败 ===================================
    _________________________________ test_answer _________________________________

        def test_answer():
    >       assert inc(3) == 5
    E       assert 4 == 5
    E        +  where 4 = inc(3)

    test_sample.py:5: AssertionError
    ========================== 1 个失败在 0.04 秒内 ===========================


由于 ``pytest`` 的详细断言内省，仅使用简单的 ``assert`` 语句。请参见 `入门指南 <https://docs.pytest.org/en/stable/getting-started.html#our-first-test-run>`_ 获取更多示例。


特性
--------

- 详细的信息关于失败的 `assert 语句 <https://docs.pytest.org/en/stable/how-to/assert.html>`_（无需记住 ``self.assert*`` 名称）

- 测试模块和函数的 `自动发现 <https://docs.pytest.org/en/stable/explanation/goodpractices.html#python-test-discovery>`_

- 用于管理小型或参数化长期测试资源的 `模块化夹具 <https://docs.pytest.org/en/stable/explanation/fixtures.html>`_

- 可以开箱即用地运行 `unittest <https://docs.pytest.org/en/stable/how-to/unittest.html>`_（或 trial）测试套件

- Python 3.8+ 或 PyPy3

- 丰富的插件架构，拥有超过 1300 个 `外部插件 <https://docs.pytest.org/en/latest/reference/plugin_list.html>`_ 和蓬勃发展的社区


文档
-------------

有关完整文档，包括安装、教程和 PDF 文档，请参见 https://docs.pytest.org/en/stable/。


错误/请求
-------------

请使用 `GitHub 问题跟踪器 <https://github.com/pytest-dev/pytest/issues>`_ 提交错误或请求功能。


更新日志
---------

请查阅 `更新日志 <https://docs.pytest.org/en/stable/changelog.html>`__ 页面以获取每个版本的修复和增强。


支持 pytest
--------------

`Open Collective`_ 是一个在线资金筹集平台，旨在为开放和透明的社区提供支持。
它提供了筹集资金和以完全透明的方式分享财务的工具。

这是希望向项目直接进行一次性或每月捐款的个人和公司的首选平台。

请在 `pytest collective`_ 中查看更多详细信息。

.. _Open Collective: https://opencollective.com
.. _pytest collective: https://opencollective.com/pytest


企业版 pytest
---------------------

作为 Tidelift 订阅的一部分提供。

pytest 及其他数千个软件包的维护者正在与 Tidelift 合作，提供您用于构建应用程序的开源依赖项的商业支持和维护。
节省时间，降低风险，改善代码健康，同时为您使用的确切依赖项的维护者付费。

`了解更多。 <https://tidelift.com/subscription/pkg/pypi-pytest?utm_source=pypi-pytest&utm_medium=referral&utm_campaign=enterprise&utm_term=repo>`_

安全性
^^^^^^^^

pytest 从未与安全漏洞相关联，但如需报告安全漏洞，请使用 `Tidelift 安全联系 <https://tidelift.com/security>`_。
Tidelift 将协调修复和披露。


许可证
-------

版权所有 Holger Krekel 和其他人，2004。

根据 `MIT`_ 许可证条款分发，pytest 是自由和开源软件。

.. _`MIT`: https://github.com/pytest-dev/pytest/blob/main/LICENSE
