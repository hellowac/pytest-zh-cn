.. _test-anatomy:

测试剖析
=================

**Anatomy of a test**

.. tabs::

    .. tab:: 中文

        简单来说，测试旨在查看特定行为的结果，并确保该结果与预期一致。
        行为不是可以通过经验测量的，这就是为什么编写测试可能具有挑战性的原因。

        “行为” 是指某个系统 **对特定情况和/或刺激的反应** 的方式。但究竟 *如何* 或 *为什么* 做某件事并不如 *做了什么* 那么重要。

        你可以将测试分为四个步骤：

        1. **准备**
        2. **执行**
        3. **断言**
        4. **清理**

        **准备** 是为我们的测试准备一切的地方。这几乎意味着除“**执行**”之外的所有内容。就像排好多米诺骨牌，以便“**执行**”可以在一个状态改变的步骤中发挥作用。这可能意味着准备对象、启动/终止服务、在数据库中输入记录，甚至定义查询的 URL、为尚不存在的用户生成一些凭据，或者等待某个进程完成。

        **执行** 是启动我们要测试的 **行为** 的单一状态改变操作。这个行为是执行被测系统（SUT）状态变化的操作，而我们可以查看的结果状态变化就是我们用来判断行为的依据。这通常以函数/方法调用的形式出现。

        **断言** 是我们查看结果状态并检查其在尘埃落定后是否如预期的地方。这里我们收集证据，判断行为是否符合我们的预期。在测试中的 ``assert`` 是我们对该测量/观察进行判断的地方。如果某个事物应该是绿色的，我们会说 ``assert thing == "green"``。

        **清理** 是测试自我收拾的地方，以便其他测试不被其意外影响。

        从本质上讲，测试最终是 **执行** 和 **断言** 步骤，而 **准备** 步骤只是提供上下文。 **行为** 存在于 **执行** 和 **断言** 之间。

    .. tab:: 英文

        In the simplest terms, a test is meant to look at the result of a particular
        behavior, and make sure that result aligns with what you would expect.
        Behavior is not something that can be empirically measured, which is why writing
        tests can be challenging.

        "Behavior" is the way in which some system **acts in response** to a particular
        situation and/or stimuli. But exactly *how* or *why* something is done is not
        quite as important as *what* was done.

        You can think of a test as being broken down into four steps:

        1. **Arrange**
        2. **Act**
        3. **Assert**
        4. **Cleanup**

        **Arrange** is where we prepare everything for our test. This means pretty
        much everything except for the "**act**". It's lining up the dominoes so that
        the **act** can do its thing in one, state-changing step. This can mean
        preparing objects, starting/killing services, entering records into a database,
        or even things like defining a URL to query, generating some credentials for a
        user that doesn't exist yet, or just waiting for some process to finish.

        **Act** is the singular, state-changing action that kicks off the **behavior**
        we want to test. This behavior is what carries out the changing of the state of
        the system under test (SUT), and it's the resulting changed state that we can
        look at to make a judgement about the behavior. This typically takes the form of
        a function/method call.

        **Assert** is where we look at that resulting state and check if it looks how
        we'd expect after the dust has settled. It's where we gather evidence to say the
        behavior does or does not align with what we expect. The ``assert`` in our test
        is where we take that measurement/observation and apply our judgement to it. If
        something should be green, we'd say ``assert thing == "green"``.

        **Cleanup** is where the test picks up after itself, so other tests aren't being
        accidentally influenced by it.

        At its core, the test is ultimately the **act** and **assert** steps, with the
        **arrange** step only providing the context. **Behavior** exists between **act**
        and **assert**.
