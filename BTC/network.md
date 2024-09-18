# basic layer

Application Layer:  BitCoin Block chain

network layer: P2P Overlay network

# basic process

1. If app want to join the BitCoin Network, it should have a seed node address firstly.
2. After the app have connected the seed node, and seed node would send the other nodes address that it has as response. The protocol used between nodes is TCP that contribute to go through the firewall. 
3. If app quits, more work don't need to do, and other app would remove it after a period of timeout.

# Design Principle

Simple, robust, but not efficient. 

Nodes maintain the set of neighbour nodes. The method of the message transport is flooding.

Node maintain a set of trade that wait to write into blockchain, the node add the trade to the set that recieved at first time, then send it to the neighbour nodes. The trade should be valid, it means legal signature and so on.

## race condition

confict trade, A->B or A->C. Either A->B prior to A->C or A->C prior to A->B is possible. If the trade have writed to blockchian, the trade would remove from the set. If the conficted trade have writed to blockchian, The trae would also remove from the set.

## max block size

Bandwidth is bottlebeck. Max block size is limited up to 1MB. 

## best effort

Not all nodes recieve the trade, and the order of trade that they recieved is not same. The latency maybe very long at some times. Some nodes don't abide the rule of BitCoin protocol, they may not send the trade, and may send the illegal trade.
