# docs
fastmall 文档


## zookeeper
```docker-compose
version: '3.1'

services:
  zoo1:
    image: zookeeper
    restart: always
    hostname: zoo1
    networks:
      - fastmall-net
    environment:
      ZOO_MY_ID: 1
      ZOO_SERVERS: server.1=zoo1:2888:3888;2181 server.2=zoo2:2888:3888;2181 server.3=zoo3:2888:3888;2181

  zoo2:
    image: zookeeper
    restart: always
    hostname: zoo2
    networks:
      - fastmall-net
    environment:
      ZOO_MY_ID: 2
      ZOO_SERVERS: server.1=zoo1:2888:3888;2181 server.2=zoo2:2888:3888;2181 server.3=zoo3:2888:3888;2181

  zoo3:
    image: zookeeper
    restart: always
    hostname: zoo3
    networks:
      - fastmall-net
    environment:
      ZOO_MY_ID: 3
      ZOO_SERVERS: server.1=zoo1:2888:3888;2181 server.2=zoo2:2888:3888;2181 server.3=zoo3:2888:3888;2181


networks:
  fastmall-net:
    external:
      name: fastmall
```


## fastmall
```docker-compose
version: '3.3'

services:
  customer:
    image: registry.cn-hongkong.aliyuncs.com/fastmall/customer
    environment:
      TZ: Asia/Shanghai
      ENV: prod
    restart: always
    networks:
      - fastmall-net
    scale: 3
    volumes:
      - ./customer_data/data:/app/data
      - ./customer_data/conf:/app/conf
    external_links:
      - zoo1
      - zoo2
      - zoo3

  order:
    image: registry.cn-hongkong.aliyuncs.com/fastmall/order
    environment:
      TZ: Asia/Shanghai
      ENV: prod
    restart: always
    networks:
      - fastmall-net
    scale: 3
    volumes:
      - ./order_data/data:/app/data
      - ./order_data/conf:/app/conf
    external_links:
      - zoo1
      - zoo2
      - zoo3

  cart:
    image: registry.cn-hongkong.aliyuncs.com/fastmall/cart
    environment:
      TZ: Asia/Shanghai
      ENV: prod
    restart: always
    networks:
      - fastmall-net
    scale: 3
    volumes:
      - ./cart_data/data:/app/data
      - ./cart_data/conf:/app/conf
    external_links:
      - zoo1
      - zoo2
      - zoo3

  goods:
    image: registry.cn-hongkong.aliyuncs.com/fastmall/goods
    environment:
      TZ: Asia/Shanghai
      ENV: prod
    restart: always
    networks:
      - fastmall-net
    scale: 3
    volumes:
      - ./goods_data/data:/app/data
      - ./goods_data/conf:/app/conf
    external_links:
      - zoo1
      - zoo2
      - zoo3

  customer-gateway:
    image: registry.cn-hongkong.aliyuncs.com/fastmall/customer-gateway
    environment:
      TZ: Asia/Shanghai
      ENV: prod
    restart: always
    networks:
      - fastmall-net
    ports:
      - 8081:80
    volumes:
      - ./customer_gateway_data/data:/app/data
      - ./customer_gateway_data/conf:/app/conf
    external_links:
      - zoo1
      - zoo2
      - zoo3

  manager-gateway:
    image: registry.cn-hongkong.aliyuncs.com/fastmall/manager-gateway
    environment:
      TZ: Asia/Shanghai
      ENV: prod
    restart: always
    networks:
      - fastmall-net
    ports:
      - 8082:80
    volumes:
      - ./manager_gateway_data/data:/app/data
      - ./manager_gateway_data/conf:/app/conf
    external_links:
      - zoo1
      - zoo2
      - zoo3


networks:
  fastmall-net:
    external:
      name: fastmall
```

