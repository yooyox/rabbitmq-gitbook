---
description: 默认交换机
---

# 😃 简单模式

* ## 1. 需求
  * 1个生产者，发送1条消息给队列。1个消费者，接收消息并打印。
  *

      ![](https://files.gitbook.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FSFBuFDF5VzeONIn6xons%2Fuploads%2FiLin9NBooF9w0pLqtciV%2Fimage.png?alt=media\&token=f1c7222b-0c59-4edc-8c42-cbb629946828)
* ## 2. 实现
  * ### 2.1 生产者
    * #### 思路
      *

          ![](https://files.gitbook.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FSFBuFDF5VzeONIn6xons%2Fuploads%2FWjd6U8gkuzvQ6NAjZVJL%2Fimage.png?alt=media\&token=39f13263-95c2-4c97-8d67-b4e268f75dc8)
      * 生产者连接到RabbitMQ，发送一条消息，然后退出。
    * #### 代码
      *

          ![](https://files.gitbook.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2FSFBuFDF5VzeONIn6xons%2Fuploads%2Fqorq4evxzEMxp3zTpjOV%2Fimage.png?alt=media\&token=34aa7039-de18-4d0f-b5e4-385c9357c2e4)
    * #### 说明
      * 1.Connection和Channel接口都实现了`AutoCloseable`接口。所以可以使用`try-with-resources`语句，在代码块结束时自动关闭连接和信道，而不是在代码中明确地关闭它们。
      * 2.通过Channel可以进行与RabbitMQ的交互，而不需要频繁地创建和关闭Connection。一个连接可以有多个Channel。
      * 3.声明队列的方法：
        * `AMQP.Queue.DeclareOk queueDeclare( String queue, boolean durable, boolean exclusive, boolean autoDelete, java.util.Map<String, Object> arguments ) throws java.io.IOException`
        * 在发送和接收消息之前，需要先声明队列，用来确保目标队列是存在的。
        * `queue`：队列名。
        * `durable`：队列是否持久化。true：RabbitMQ重启后仍然存在。false：RabbitMQ服务器重启后不存在。
        * `exclusive`：是否排他。true：队列只能被当前Connection使用，并在Connection关闭后自动删除。
        * `autoDelete`：是否自动删除。true：在没有消费者使用时自动删除。 arguments：其他参数。可以配置队列的其他属性，如消息的过期时间、队列最大长度。
      * 4.发送消息的方法：
        * `void basicPublish( String exchange, String routingKey, com.rabbitmq.client.AMQP.BasicProperties props, byte[] body ) throws java.io.IOException`
        * `exchange`：交换机名称。如果为空字符串，使用默认交换机。消息的路由键与队列名进行匹配，匹配成功就将消息转发到该队列。 routingKey：消息的路由键。Exchange根据路由键将消息发送到匹配的队列。
        * `props`：消息的属性。如持久化、过期时间。
        * `body`：消息的字节数组。
  * ### 2.2 消费者
    * #### 思路
      *

          ![](https://lh3.googleusercontent.com/e\_1LUjQQDRXz\_6Igg6Jtm\_DoLPInW\_-Ivl7e7XQwCtNQnzoMW4JkXX-pRfELF8ysFUVwJQKDiEYpLrNzcsQAhDo-z9RUYlIRO9lWT4MERd9qo-FvEUrSPWkx5QXpbGtEtm-OlzdhUcTZoEfI0wXyJbs)
      * 消费者监听来自RabbitMQ的消息。收到消息后把消息打印出来。
    * #### 代码
      *

          ![](https://lh3.googleusercontent.com/1eKScImsNrD0g\_SFHFnRczoJhNewfMBxwTClH6IsYWmSiTBKEw5uAw0RVni7uC2xAeOSuUwR3MiMtAATLGDyknXEvWGh3kjPpdlwSbo8yv2qtmkQ8\_eaKDH1g3b8HTuHHJl3J3gNiDIy4tHv7YWEMJM)
    * #### 说明
      * 1.和生产者不同，消费者不使用 `try-with-resource` 语句来自动关闭通道和连接。我们希望消费者能一直异步监听消息。
      * 2.接收消息的方法
        * `String basicConsume(String queue, boolean autoAck, com.rabbitmq.client.DeliverCallback deliverCallback, com.rabbitmq.client.CancelCallback cancelCallback ) throws java.io.IOException`
        * `queue`：消费的队列名。
        * `autoAck`：是否自动消息确认。true：消息一旦被投递给消费者，RabbitMQ认为该消息已经被消费并处理完成， 将立即将该消息从队列中移除。false：需要手动调用channel.basicAck()来确认消息。
        * `deliverCallback`：消息接收到时的回调函数，用于处理接收到的消息。
        * `cancelCallback`：取消接收消息的回调函数。
  * ### 2.3 运行结果
    * 1.运行生产者代码，hello队列会有一条消息
      *

          ![](https://lh4.googleusercontent.com/pY0ChR3CozXWN9qukXfpEL2WETRS4DjzYEb99-L0DxQE7QrADA5q2nAG8LA\_diIW-esQ2JvoTVuCbib-DTZuKRWEGo97nrrWufyE7KroAkj4GstikSB6AaM42Q74xdtQUX4y4U2FWymWgQmwpxEtL0k)
    *   2.运行消费者代码，hello队列中的消息会被消费。控制台打印出被消费的消息

        *

            ![](https://lh3.googleusercontent.com/\_Nf3AF8Testc0VRXxMPTWccnX6z3\_nVd\_7Ts\_5Q3OwonY-xh1HbF9iJ095HRCZr5pfLf3UhN9pspEK7YNvVPe0lSX0m8XQZEhwVqhcDNUzaFYUt4YcNFpCB\_T2E\_VmRPj2oa1fPi1oJECG\_rnzCwibk)
        *

            ![](https://lh3.googleusercontent.com/T8uUNZzNge-BIj\_\_nX5KOpDb6BzCP0bIwo\_Ikl5LM0pGOHyuHXtV1QnV2YortPdOnUnA7LvkcsZxWsClctAQiDH27Xhqq3KgNLNRede9JEPRciCna9yiOq-xZ0\_w3M\_JCn1WbnBZWNt2EpQemHm8Z2o)

