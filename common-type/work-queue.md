---
description: 默认交换机
---

# 😊 工作队列

* ## 1. 需求
  * 1个生产者，多次发送消息给队列。多个消费者，一起消费队列中的消息。
  *

      ![](https://lh6.googleusercontent.com/WX16URjhf1IrFl7xeVy00UfycOyJ8zoMXTw4P4oJHoGRyiObsx8H3v06\_VZ69TbeLYq0qUfMDM9XZehgnkxh8IQnCNEDr6IWu2KNDHOPwjbUK-3\_vz0PB05RUyvt276XX--3vPQ8zdhjDjFyU1\_2Jhs)
* ## 2. 实现
  * ### 2.1 生产者
    * #### 思路
      * 从控制台发送消息
    * #### 代码
      *

          ![](https://lh5.googleusercontent.com/2xwc\_oclzpEM5Wg8Gn0MLi3qOFgXtbG\_xV9mspXuxuqA1eqZiuRxkiz9zK1eYrqucKhlYZpbaKegODRKsL0U7EJJXZLt4i15sBWWAGTEJJ3uigHp4L0GSreS5osaZKj85ZW2n4awmM8N5OSFb66Uc\_8)
  * ### 2.2 消费者
    * #### 思路
      * 处理消息时，每遇到一个点就伪造一秒钟的处理时长
    * #### 代码
      *

          ![](https://lh3.googleusercontent.com/Fd9MnqGzkMU\_A-fPLyxXNv57PUCi1CjkGdhI14D6TWOi\_uvyx\_\_0CSQQt\_tBIaVwwGbrII-Xg1II7ICR3BrpgI-9IvYxNzY9\_tStctl1Omkm74SK4-b3e95LmD3KZinCctRSpiC9yaHwqmJB9mg3xYM)
  * ### 2.3 结果
    * 默认情况下，RabbitMQ 会将每条消息按顺序发送给下一个消费者。不会考虑每个消费者处理消息的时长。
    *

        ![](https://lh6.googleusercontent.com/ppRXAbdARMCQ5w2I3APST-yGmuCfp2cOzRgZndOn\_rR4BIwSrzabXZk6QsGSVMv8E7vtRGDA\_CBuYAl5XyWXU9-dxWcm1qnc1c\_k4ekNDa9whzBI6yWRKVkXq0eNTKkOZL4UFULcvrbHVCfPdyMkQhI)
    *

        ![](https://lh6.googleusercontent.com/fHOGm9qdIypl\_5YtaMetoJky\_xVFe\_LMKWfWQD1k0cNg1VW03KtvLCVCFRb6DFFW3s\_rx9zcACccCItHf-3rD39HtDXp4JVhEWLZGKvs4QYjzJQ1PgLV1pW5zsguKVoZy5OuhbZvWklZ8spGK33YoWg)
    *

        ![](https://lh6.googleusercontent.com/cy4W3XFPgbcFygRS\_ESjHRooruKNjweb0pCQeobcEo\_o48qoSqNU5anqdAoEsnVpJxBzNw2uhjXijy3Uit\_5O2I1SZfKy9YHHGeEDR2tSO14XV2N7fiF22G4hK0EfpEL87nkkK2CsIBgERP4jTKRQd4)
* ## 3.问题——消费者死亡导致的消息丢失
  * ### 3.1 现象
    * 在我们现在的代码里，`channel.basicConsume()`中设置了自动应答为`true`，一旦 RabbitMQ 将消息传递给消费者，消息就会从队列中被删除。如果这时终止一个消费者，该消费者未处理完的消息将会丢失。
  * ### 3.2 期望
    * 如果终止一个消费者，没处理完的消息重新回归队列，并被重新投送给另一个消费者。
  * ### 3.3 解决
    * 进行手动消息确认。消费者在接收、处理完消息以后，发送`ack`告诉RabbitMQ可以删除该消息了。如果RabbitMQ没有收到`ack`，就会将消息重新排队和投递。
  * ### 3.4 实现
    * 关闭自动应答，设置手动应答。
    *

        ![](https://lh3.googleusercontent.com/II3LbJdDYw-vgUpOA7HiE2VJ2g1P6rfHX9J5Ydmf3\_Z4tVW4YPq2\_jnB\_odUa1zRqe\_0zRKC7E64B0CLQ\_lN3wajkAZplUlStBE\_PxtdWSMOAleEXkQ\_bIxzOtHuPRKY8SQ3EPiBj9ADqXKVobAW63A)
    * 生产者发消息，消费者1正在处理消息的时候就断开，此时1没处理完的消息会给2处理。
    *

        ![](https://lh6.googleusercontent.com/j5DbQ411IQufWA6Ium0ZYNc0pC-z3QqJNaMUdZZNgk0TjnRC5JSRbfaBgbXFQROm80GEAMR0QbnGTnBawHtLzLUIwSHK4G\_bBSQVeJl3ynPZwXWIjIAloqL0NURbWbC6sqniAWDyKBN\_W8vMjA9eABs)
    *

        ![](https://lh6.googleusercontent.com/6FpqtGVVIl0xCEtRC0Y8osqZ3wgXLqVc--lWR3N1Sk0KG-NZmdqoA01hI0jmJ24cehvRhjaH5LSP1P4gmII9GzvAmvyAKHaCUuZvxlp82nW16xbAorQULzl3eyTsLc89JQaIoxiRleLTLSg65SMrJCU)
    *

        ![](https://lh5.googleusercontent.com/Kxpy7sx0qgCMsGvG6PN8TXmSSYte5\_miqGW2MtZBl-2VcKHZQ68cbvGhM0U8CZFn600x0gLRVB46tqlYziOpoOdxVp9zukFG70YuVzOJYjKEsxCgbCNLeq2U7B8hI7aYpC4-xJaun7ef3Yten466Zmg)
* ## 4.问题——RabbitMQ服务器停止导致的消息丢失
  * ### 4.1 现象
    * 当 RabbitMQ 退出或崩溃时，重启后它会忘记队列和消息。
  * ### 4.2 期望
    * 重启RabbitMQ后，消息和队列还在。
  * ### 4.3 解决
    * 将队列和消息都持久化
  * ### 4.4 实现
    * 队列持久化
      * `channel.queueDeclare()`中，设置`durable = true`。如果队列已经被定义为非持久化，可以将它删除，再设置为持久化。
      *

          ![](https://lh6.googleusercontent.com/Im7MDhF-sF-bSAqDYyvw7-xn1C5QCqkgBKiYxAlncyDCyVaD8xb3x-yO6l3AbpGiE37OSERZdLa9zmzFeZoHIVnnJBToCsGmHsUBYku5dW4kl23pRB0-9Jbx-rEPlBhtIlNJA07JES\_eEcBZ8IMc6bs)
    * 消息持久化
      * `channel.basicPublish()`中，设置`props`为`MessageProperties.PERSISTENT_TEXT_PLAIN`。
* ## 5. 问题——均匀发消息导致的不公平
  * ### 5.1 现象
    * RabbitMQ分发消息时，没有考虑消费者的处理速度。比如有两个消费者，当奇数消息大，偶数消息小时，一个消费者会一直很忙，另一个消费者几乎不做任何工作。
  * ### 5.2 期望
    * 在消费者没处理完一条消息前，不要向它发送新消息。而是把新消息发给其他空闲的消费者。
  * ### 5.3 解决
    *

        ![](https://lh6.googleusercontent.com/DoF09uPkjqDlAZ\_xlx5IO-1LRUdQE33mh-VLt6B0csEr1bziOOK4IovP6sVbwNHy2UdKzJQdooUfom5NmpyvuJNvaLrxIWzbPfNGqKa\_Ht\_\_CkTOo8\_bGEvwShxi\_OwGbAFNs5Y-ncIk1FwUCn79Fs8)
    * 限制RabbitMQ发送到消费者的信道消息堆积数。轮询消费者，信道消息堆积数满了就跳过该消费者。
  * ### 5.4 实现
    * `channel.basicQos(消息堆积数);`
