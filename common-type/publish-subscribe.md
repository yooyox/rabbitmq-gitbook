---
description: fanout类型交换机
---

# 😎 发布订阅

* ## 1.需求
  * 向多个消费者，投送一条消息。交换机将消息，广播到所有绑定的队列。
  *

      ![](https://lh5.googleusercontent.com/jx7MCq54EPq1Ry1n41ZJOrNuwKlA31ZN-kcHibw4iVaWQa905LZyLLgdzA7oIEdQeLuhoHJZlmotPZO\_BHzFgq86bh2hoBZSJPous4MuLEsUQ2OAu1mvyrbggMPZZF9MbMqhEOg-LqQvL-rIC9UcYmg)
*   ## 2.实现

    * ### 2.1 生产者
      * #### 思路
        * 将消息发送到fanout类型的交换机。
      * #### 代码
        *

            ![](https://lh3.googleusercontent.com/wasz67zAt4BtViNO3ShKIDjDoZp5F473H\_S5lEWRlYTa71h3Qgou9CVuWlfK60JH9Hfwg61ZeEXMWw3dhjTixLkmL9g9oBiIMTnTDATttuib1HVyEDmpunmdgNRyfg3ag5iO2VX52OphhZGt6hjECpY)
      * #### 说明
        * 1\. 声明交换机的方法：`AMQP.Exchange.DeclareOk exchangeDeclare(String exchange, String type ) throws java.io.IOException`
          * `exchange`：交换机的名称。
          * `type`：交换机的类型。
          * 如果已存在同名的交换机，并且参数与已存在的交换机相同，则忽略声明请求。
        * 2\. `Fanout`（扇型）是 RabbitMQ 中的一种交换机类型。它能将消息广播到所有绑定到该交换机的队列，不考虑路由键（routing key）的匹配。
    * ### 2.2 消费者
      * #### 思路
        * 将队列和fanout交换机进行绑定。
      * #### 代码
        *

            ![](https://lh3.googleusercontent.com/v6HPaZTr5FcYkDeoQ5uZgluXRXDLIsG9CO7mSPlB9zSQe0ICLTu6utb1zJw1HUKIbkVWb4X6XdmhSY4vmk7OYVOOLiR4YjmcRZtf0iclLbYBs0KyQJo-nsW-MhPdJCIi9xDOjAgoKXgTH6kh9R5Z114)
      * #### 说明
        * 1\. 当我们不向queueDeclare()提供任何参数时 ，会创建一个具有随机生成名称的、非持久的、独占的、自动删除的队列。
        * 2\. 绑定队列和交换机的方法：`AMQP.Queue.BindOk queueBind(String queue, String exchange, String routingKey ) throws java.io.IOException`
          * routingKey：路由消息到队列的绑定键。对于 Fanout（扇型）交换机，绑定键不起作用，消息会广播到所有绑定的队列。
    * ### 2.3 结果
      * 生产者发了一条消息，两个消费者都能接收到
      *

          ![](https://lh3.googleusercontent.com/rJyOCL\_yq\_L28cTyoh5LhD6o4xA6MhfxlWKpiJVOuhiH9G9GlgsThC6fXZBRRG6b3ZNfOng7q9CgXKz7DnucLCi-JpewB64vlvMs3HXhEHk-psTj6A4Zcges0hFqEtIYJbxvLIhpAyABWwENqpSpAGE)
      *

          ![](https://lh5.googleusercontent.com/EtkZV7xdRLZLkz0NTD0OBbUV9MlciKGpqhKY02TLlREI\_7gRZF5cU6U\_7J0Vp30HAVJLB87I4rMsjSsn6kjOsXbpReovnTvNKDRfTAxisOW3kptmhfHq4b8S3cOvV2XPwdvjRRQw8\_XFtzTCBH\_9PRk)
      *

          ![](https://lh6.googleusercontent.com/AWye272YtT8uHtWIszHhBeREGULAIVj1X\_X0uL5jpcD3cInvFAC\_MzYR5\_BLeQry9tdX6qj3vVMgn3HuvOIYqjQXI\_wJWw-njRs-9Zv3Z4kRYWJWDGK1M75HaQ0cTElBI89xT5O9tgMcovXgH1U\_3GM)

