# User System

## Scenario
- 注册、登录、查询、用户信息修改
   
    哪个需求量更大 --- 查询。打开微信，就是对自己的用户信息进行了一次查询。
- 100M DAU. 这意味着什么？我们要确保查询的峰值的时候，算算峰值QPS, 数据库不会爆掉。
    - 100M * 100 / (24 * 60 * 60) 约等于 100 K
    - 100就是平均每个用户，每天，查询用户信息相关的操作次数（查看好友，发信息，更新主页）
    - 峰值就估算为 100 K * 3 = 300 K

### 数据库QPS性能
| MySQL | MongoDB | Redis / Memcached  |
|-------|---------|--------------------|
|  1k   |   10k   |   100K ~ 1000K     |

## Service
- AuthService 注册登录
- UserService 用户信息存储与查询
- FriendshipService 好友关系

## Storage 
- Cache
    - Memcached 不支持数据持久化
    - Redis 支持数据持久化

- LRU Cache: Least Recently Used Cache
- LFU Cache: Least Frequently Used Cache

