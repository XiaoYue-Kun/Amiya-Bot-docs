# 生命周期

通过 `on_message` 注册的功能函数，在每次被正常执行时，都会历经一个完整的生命周期。你可以介入这些周期来对业务进行进一步的调整。

AmiyaBot 目前提供了三个全局的生命周期响应函数注册器。

## handler_middleware

在 Message 对象构建完毕，准备进行功能筛选前需要执行的方法。允许对 Message 对象进行修改后返回。

```python
@bot.handler_middleware
async def _(data: Message):
    if ...:
        data.text = ...
        return data
```

::: tip 提示<br>
在这个周期阶段，功能尚未开始筛选。
:::
::: warning 注意<br>
可以注册多个，按加载顺序逐个调用。后者执行时参数 Message 会受前者的返回影响。
:::

## before_bot_reply

在执行 `on_message` 功能函数前需要执行的方法，返回布尔值，若为 False，则不往下执行功能函数。

```python
@bot.before_bot_reply
async def _(data: Message, factory_name: str):
    if ...:
        return False
    return True
```

::: tip 提示<br>
在这个周期阶段，功能函数已经筛选完毕并已经产生了候选优胜者。
:::
::: warning 注意<br>
可以注册多个，按加载顺序逐个调用。全部执行完成后当有其中一个返回 False，则不往下执行功能函数。
:::

## after_bot_reply

在 `on_message` 功能函数执行完毕且发送了消息后需要执行的方法。

```python
@bot.after_bot_reply
async def _(data: Chain, factory_name: str):
    # Chain 对象是 bot 当前发送的消息的构建对象
    pass
```

::: tip 提示<br>
在这个周期阶段，功能函数已经执行完毕。
:::
::: warning 注意<br>
可以注册多个，按加载顺序逐个调用。
:::
