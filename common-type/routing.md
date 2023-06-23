---
description: direct类型交换机
---

# 😄 路由

* ## 1. 需求
  * 向多个消费者，投送一条消息。交换机将消息，转发给绑定键和消息的路由键完全匹配的队列。
  *

      ![](https://lh3.googleusercontent.com/WR0yTL0jSoIoO5l9Qz9ywdy0nIXevVNwiy2EPdjZWef6lpsVwNPgZbbGvmKWx5tVyZFKH9VW1L63-2AIifM638qV1YeFrbJTfikZxYs9UnKVVRkB7fJ6ADjkg3Ix69A2cRLfbraZrs\_-w3LIyZH0KhA)
* ## 2. 实现
  * ### 2.1 生产者
    * #### 思路
      * 将消息发送到direct类型的交换机
    * #### 代码
      *

          ![](https://lh6.googleusercontent.com/IaVcBXrUbdp5rfprwT-7Xbw0XqSRUT3ATa4L-e1s0caEaMBwaR0gCmG46PIXDJYZ8CgafDI8QavwmTS5q3n7r2DAmsYnjVjbPIKUsbseLuQ8LLbiLROKDNcwVaKOWjHrCCRjY6a31JJo4CpZ2\_0ZLMQ)
    * #### 说明
      * 生产者把消息发送到Direct类型的交换机。并发送路由键不同的消息，观察不同队列接收消息的情况。
  * ### 2.2 消费者
    * #### 思路
      * 创建两个消费者，分别连接两个队列。两个队列都和同一个Direct类型的交换机绑定，只是绑定键不同。
      * 第一个队列的绑定键是error。第二个队列的绑定键有3个，分别是info、error、warning。
    * #### 代码
      *

          ![](https://lh5.googleusercontent.com/mE2X7up6j-LTFnCZP2nO6elg3Ib7KJ90NqmmwxUu-H6M-9YpiTLZ9FRk5y\_fHyaLkAP332Bp5A2VUkRSSvein762vifq6EEg93YSPai1wVKaWgPygyretsvKbg8\_kXEHhcoaHnahoFB64z944715p2c)
      *

          ![](https://lh6.googleusercontent.com/Fgpqhohn4DGLPZDR4hvcvWwOR4DHj\_LgmV3LK32fsSqPLLqrDZQ37KhjmOOx4vn8Bljo\_\_EgEAFw2ZxsiSi1hdiaA1ua3zDMw4VnBGv19byYq1n2JC6xZQyEQ5Y9y69YRpXkm1SV\_x1m1i6qp0eMLWo)
  * ### 2.3. 结果
    * 设置发送的消息的路由键是error时，两个队列都能收到消息。
      *

          ![](https://lh5.googleusercontent.com/7sJC90RarcHpltAi8Y93bqp58kx3QyipxQUv7EXpnpBlgAgEG3RbQ3cDdO6CPkdPn75ZMJY6zMSdsYWuny\_KZxP9IkiQB-KeEnmIkXo6uF\_s6rOqy7PShSBs-ugzDmg33sJRjAdLDaEKT\_fAQCN1S7w)
      *

          ![](https://lh5.googleusercontent.com/P3qTovwij0\_KCq1p7OpV5r0bbbUtWbTPPeS\_XrevZ5X3O1a4H3biImZAEQhUvlBJGcHrnVdq4Dm0OojXFUJku2g52SFncuSGbpodVeMpG\_q8qnM0sfR8xpETXslWhYew7X\_DWQPleZh-aqf8Wdl9xhE)
      *

          ![](https://lh4.googleusercontent.com/ZNdCebFKSavyLPOQvCoG169H6PbXJRgTTwHl0dZJp3adlONXLB59BzubPzyNlIdntRy3MfHakBcPuYE0cvsrweVSObjxZ6Bq663EMKIrKMpBesKVlzZpyxJYYKjBL\_-O4CDkEzCU1hKwklr3NZ70RyE)
    *   设置消息的路由键是info时，只有第二个队列能收到消息。

        *

            ![](https://lh5.googleusercontent.com/4TQGXQDUYpPioxKkQ4Sv9fRuWjnLIMjzBbIpvtz-V867suKq9PJEYBDYiApn0XVq8qu-cDPAMoVkNwh2Rh2sxe8IPcmig01DXlVT72nuAKZHc2Ho52y622eRksHlwvtJT3xTNUO9u7a810EdE9AzVfI)
        *

            ![](https://lh5.googleusercontent.com/tdtpkB7uGaP3K7f8O1VlpA8lHNAZVFgYeeVnQL505V1NWOdyohkjdnmWEEQ3NVVOhCY0jwy5RdQLfBjdnbaV6zkCE0izvGiU4yKi2DJswjN3b2qyJ\_NQdyzsOFl0z29XulQi696uo9J\_lbX5fLDfMR4)
        *

            ![](https://lh5.googleusercontent.com/rfNzQVbxaR8WfxOOLgfDdDawUx-bs6gck8cg-nsqyJ15bS\_RmDmshVz-ao7S781e3JjVWoPrz-xDbwuEArGtFBhYsuylPORFtgfRv\_V-bJOj26Buo2rletfmmvJzQIoXsOWLt\_t2V7tbsswfDfVUbz0)

