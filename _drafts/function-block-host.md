---
layout: post
title: "Function block_host/3"
date:   2023-12-11 21:40:00 -0800
categories: 
author: Frawkey
origin: "monerod log"
file: "src/p2p/net_node.inl:307"
namespace: nodetool
identifier: "block_host/3"
count: 81
---

Reading the monerod deamon log file, there may be entries like this:

~~~
[P2P8]  INFO    global  src/p2p/net_node.inl:307        Host 95.216.203.255 blocked.
~~~

The Git history indicates that:

> Add IP blocking for misbehaving nodes (adapted from Boolberry)

Here is the current signature:

~~~
  template<class t_payload_net_handler>
  bool node_server<t_payload_net_handler>::block_host(epee::net_utils::network_address addr, time_t seconds, bool add_only)
~~~

The implementation seems to go like this:

- set time limit, but not above maximum
- update a list of all blocked hosts
- check if it matches a subnet on the block list already
- scan existing connections and drop blocked address
- issue message on log file