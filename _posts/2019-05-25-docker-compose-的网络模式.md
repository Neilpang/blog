---
tags:
  - docker
  - docker-compose
  - network
---


在 docker-compose 的原始版本中, 创建出来的服务都是全局的, 也就是说同一台机器上的所有 docker-compose 产生的container是可以互相访问的, 只要通过ip 就可以. 如果通过 `link` 也可以通过 `name` 访问.

在 docker-compose 的 `v2` 和 `v3` 版本中, 每一个 `docker-compose` 所创建的所有服务(container) 共享同一个网络. 同一个网络内的服务之间可以通过 `name` 互相访问. 但是不同`docker-compose` 产生的服务之间默认是不能访问的.

要想解决这个问题, 官方推荐的做法是让需要跨网络访问能力的conainer 加入到多个网络中.
参考这有说明:

https://docs.docker.com/compose/networking/


如果在一些简单的情况下, 我们不需要这么复杂, 只需要让 `docker-compose` 不创建独立网络, 而像以前一样使用系统的默认网络就好.


