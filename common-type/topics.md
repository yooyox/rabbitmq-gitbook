---
description: topic类型交换机
---

# 😆 主题

* ## 1. 需求
  * 向多个消费者，投送一条消息。把队列的绑定键（\*代表一个单词，#代表零个或多个单词）和消息的路由键（由点分隔的单词列表）进行比较，根据通配符进行模糊匹配，匹配成功就会被转发到队列中。
  *

      ![](https://lh4.googleusercontent.com/oMiB\_h2Brb6P1bNZiofs\_ybpkmtw0RRxF1jRucuJkZQklVcbPxngTGo3Yd8B9bQx-bSwHSFB\_oW8RB\_73ViSqbT6QuinGqQiWg372MkfACsDz-z-ud3IhJvPVdDUq6dI2d\_NOf79YdS5wXPa4AXRLZU)
  *

      ![](https://lh5.googleusercontent.com/2WS8kr5Gs\_7o6O-DlH7p3ubxLWb9RcqLC1zmvhwtv-8LTKTk7NQazkEqe5UJadxsFKB6eQy\_7eBavXYFckZnikoqanOEERsO\_TgLkpSLuo1qGMvFCq\_jT8ngUh\_RaQJKEOTOs5zdkpV35IRDVkaIYoc)
* ## 2. 实现
  * ### 2.1 生产者
    * #### 思路
      * 将消息发送到topic类型的交换机。发送不同路由键的消息，观察队列的接受情况。
    * #### 代码
      *

          ![](https://lh4.googleusercontent.com/hPp-4vAtLSpzQf-wEysnKx8npAgJzT9Ctb9CNPRN7IwGyxsePp6yLuWCP6qjLKeKGR6VVpfJDeuQbsd2ldv6Vb7kr51if2tNOg83TYKs7Fncr-rVj\_ABodA\_q\_SrdkNysIPQXlVaK0eJCG4SIRlu0Xc)
  * ### 2.2 消费者
    * #### 思路
      * 两个队列连接同一个topic交换机。队列1的绑定键是\*.orange.\*。队列2的绑定键是\*.\*.rabbit、lazy.#
    * #### 代码
      *

          ![](https://lh4.googleusercontent.com/hZoKw-EI2UTWwU2xLhypUfPsahoBPneSupmsxZZuDVRAJd4PUUf0d7Za9x5i6Fd0lcdyaBdVm9p4MtBSaTl3ER9l5SFefRiQNUuH2vZ0WfWBv0AuK75hpxANbDq050lkqrcOFcMcguXyXnWdsE2UTyc)
      *

          ![](https://lh6.googleusercontent.com/Zj3J413qv1NIlh8-jNuoQ2DZ3is9pIyCBpiFgHYJsyX8aXgJZx7rwv66\_LmMEHWdfz\_0OBrdlLvhVCduo5uYCBpBYGFvh3hwsYHK7lZhRrq9kPH1xzbl1f-gSO3j95-\_gCtegi57X5hFOhcWfM1xQHw)
  *   ### 2.3 结果

      *

          ![](https://lh6.googleusercontent.com/lGiiaT7MQRLGQ5LBeegIXV8DckshB5YhYhXB2sbcnUT1w-YzvTdnNptPKyhyNME9bOfUPt\_wKpJAJXGU-1Az98ztqZTj\_k\_EerAQyJkfoUO4nXVGLyl53pIGPA93shA6i2-hAmP4FpuyPOFwzCmsIVI)
      *

          ![](https://lh6.googleusercontent.com/YKpGJu1DERMUsFNDIJBFVh4umB8GuHuA2SB9LXprmYHdP4hz5NOXmOC5Ub5djM\_K\_iaY7CqeCm52PnMJfbz-OaZFfGdhQmomAWPrb3XTRwvN14k4xELZW76OUR-TBJoFEzH2\_WPgpFwQbUcBDtEZb5w)
      *

          ![](https://lh6.googleusercontent.com/fd0gqM6IiUROVs4Yjb1ZOZHtrEPcEesaTGJjRqBljZUZcBX\_fWEpbM6tchFBWu7CWqfKtfzHO-puOeAElWUQgCwX3PsN6i-VPWxNz0deNzv9Qcbnbi5DLgefVEqa9QNl\_AUHF8ZTn1hgFQM0X7pk5fY)

