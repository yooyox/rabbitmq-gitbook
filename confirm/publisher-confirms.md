---
description: 确认消息是否成功发送到RabbitMQ服务器
---

# 🤩 发布确认

* ## 1. 需求
  * 我们不确定生产者发布的消息，是不是已经到达RabbitMQ服务器。需要确认机制来确保。
* ## 2. 开启发布确认
  * `Channel channel = connection.createChannel();`\
    `channel.confirmSelect();`
  * 调用 `channel.confirmSelect()` 方法后，该 Channel 将进入发布确认模式，生产者会收到发布的消息是否成功投递到 RabbitMQ 服务器的确认信息。
* ## 3. 类型
  * ### 类型1——单个确认
    * 原理
      * 发一条消息就确认一次。如果没有得到确认，会阻止后面消息的发布。发送消息之后，调用 `channel.waitForConfirms()` 来等待发布确认的结果。该方法会阻塞当前线程，直到收到确认结果或超时。
      *

          ![](https://lh3.googleusercontent.com/WAH2x6VY5Snm8h-\_M6NDt\_FFC4\_zxmB4FhjaDlomvICoUJKk\_nGcVQto1hmDrwoXITzPzz9HCdFjQgKR37gaMTm7f01aDz5Xa1EtxFrIw\_ADeHB3MXI\_XkCPOUwQfk6b47q-kVTpO5hvmpwxdW8e1WA)
      *

          ![](https://lh6.googleusercontent.com/3q-CboFbxWIAZtyWNhtoEBDhOlvAEmpdw5cumVhpOzuGgKLoXH7wO88zKIefD33IA2f7C\_1Drp6bZN8sQTSZH8gO9zs5dSLLe41GvNO-mynkBe1lt42NPIbO7UKXWNdanIuJC-hjhWbPPDUf1GI-Z9c)
  * ### 类型2——批量确认
    * 原理
      * 发布一批消息，确认一次。生产者会按照一定的批量大小发布消息，并等待该批量消息的确认结果，然后再发布下一批消息。和单个确认相比，这种确认机制可以提高消息发布的效率，但是发生问题时，不知道这批消息中到底丢了哪些。
      *

          ![](https://lh4.googleusercontent.com/vm3nCXRZxhPfV7l0Rh84-tHVsA8bKUNpAL\_OzIYjfK4if\_CuRJqZrgPQhFMTt6TRmPViAHz6WwVYHiuc3g4PIgEUlHy99sMDPR2zRv79P2gWQvM0R7IpHjulD0P5wFD-TYihBjQniUTkw5kPY2M17YQ)
      *

          ![](https://lh3.googleusercontent.com/P-6AX7PJiXwnSaBj3NnE8oyIw\_Bj-EgpnYjxmdEi7iGrAtC74YaqTmL98tkxrHmsMh0nDAr\_tliUn7pnI5Ry0QM\_WZM9VBBMEdVC3s1N0OIfxl7M1BrT9-1JAYAaTk4FxjsmyIgqTmMsAl9urb\_SVaY)
  * ### 类型3——异步确认
    * 原理
      * 生产者发布消息后不需要等待确认结果，而是通过回调方法来处理消息的确认和未确认结果。生产者发布消息之前，注册确认监听器（`ConfirmListener`），在发布消息后，可以异步接收到相应的确认结果回调。
      * 有两种回调方法。一种是用于已确认的消息。另一种是用于丢失的消息。
      *

          ![](https://lh4.googleusercontent.com/SEtBU-P9eg-ssGfiOsO8yBDY5YH2GIcGvknPeibE\_1HAcn77dUn90xMr-48DX-kO11fWVuHk0AXFvWoG-IeLev3\_uTO69d4jQWHatlMoudA-ravWYcK\_E4tbd09k4KGYWxCn0C1MEOYQwfpXXuOJmU4)
      *

          ![](https://lh4.googleusercontent.com/ADE3kf6NPX81o92l9vJA-mwDyx79r0LJVbZ1LW9tT3uZkYCOkJ4n0whdVQ2zFGVmRjUCDmXAM-p62eVFOILgfv3Rqj62Hjllq7M2TdAi-zvebELQtYfEgJVorfdrCrOUpQWqYB7wUrGfFW5fWqsx9Fc)
      * 可以发现，这种确认机制是异步的，没有规律顺序的。
