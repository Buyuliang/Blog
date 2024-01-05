---
title: NET Driver
date: 2024年1月5日 10:29:07
tags: [NET]
categories: NET
---

``` c
  struct net_device_ops { 
      int         (*ndo_init)(struct net_device *dev);     /*当第一次注册网络设备的时候此函数会执行*/
      void        (*ndo_uninit)(struct net_device *dev);     /*卸载网络设备的时候此函数会执行*/
      int         (*ndo_open)(struct net_device *dev);     /*打开网络设备的时候此函数会执行，网络驱动程序需要实现此函数，非常重要*/
      int         (*ndo_stop)(struct net_device *dev); 
      netdev_tx_t (*ndo_start_xmit) (struct sk_buff *skb, 
                             struct net_device *dev);    /*当需要发送数据的时候此函数就会执行，此函数有一个参数为 sk_buff结构体指针，sk_buff结构体在Linux的网络驱动中非常重要，sk_buff保存了上层传递给网络驱动层的数据。也就是说，要发送出去的数据都存在了sk_buff中*/
      u16         (*ndo_select_queue)(struct net_device *dev, struct sk_buff *skb, void *accel_priv,select_queue_fallback_t fallback);     /*设备支持多传输队列的时候选择使用哪个队列*/
      void       (*ndo_change_rx_flags)(struct net_device *dev, 
                                 int flags); 
      void        (*ndo_set_rx_mode)(struct net_device *dev);    /*此函数用于改变地址过滤列表*/ 
      int         (*ndo_set_mac_address)(struct net_device *dev,  void *addr);     /*此函数用于修改网卡的 MAC 地址*/
      int         (*ndo_validate_addr)(struct net_device *dev);     /*验证 MAC 地址是否合法，*/
      int         (*ndo_do_ioctl)(struct net_device *dev, 
                              struct ifreq *ifr, int cmd);     /*用户程序调用 ioctl 的时候此函数就会执行，*/
      int         (*ndo_set_config)(struct net_device *dev, 
                                struct ifmap *map); 
      int         (*ndo_change_mtu)(struct net_device *dev, 
                            int new_mtu); /*更改MTU大小。*/
      int         (*ndo_neigh_setup)(struct net_device *dev, 
                                  struct neigh_parms *); 
     void       (*ndo_tx_timeout) (struct net_device *dev);     /*当发送超时的时候产生会执行，一般都是网络出问题了导
致发送超*/
... 
 #ifdef CONFIG_NET_POLL_CONTROLLER 
     void       (*ndo_poll_controller)(struct net_device *dev);     /*使用查询方式来处理网卡数据的收发。*/
     int         (*ndo_netpoll_setup)(struct net_device *dev, 
                              struct netpoll_info *info); 
     void       (*ndo_netpoll_cleanup)(struct net_device *dev); 
 #endif 
... 
     int         (*ndo_set_features)(struct net_device *dev, etdev_features_t features);     /*修改net_device的 features属性，设置相应的硬件属性*/
... 
 }; 
 ```