# 技术细节

本文讲述 `YetzirahLand** 使用的插件的部分技术细节

讲述技术细节的目的是为了帮助玩家更好理解游戏机制和玩法

本来没有这么做的打算，因为这样涉及开源自己编写的 **专用** 插件

---

服务器主要机制使用的是自主编写的插件 `EtheriDecompositor`

该插件已经在 [github](https://github.com/freeze-dolphin/EtherDecompositor) 开源

以下每个二级标题都是可点的指向相关源码的链接

## [箱子填充](https://github.com/freeze-dolphin/EtherDecompositor/blob/master/src/main/kotlin/io/sn/etherdec/modules/ChestAutoStuff.kt)

### 随机机制

本服的物品随机并不是采用类似抽奖的方式（每个物品设定一个概率 `x`，每次填充有 `x` 的概率出该物品）

`补给箱` 定时器计时结束之后，会从奖品池中随机抽取几个物品

假设现在配置文件编写如下：

```yaml
filling:
  fill-try-times: "5,12" # 尝试装填次数，最小 5，最大 12 次
  loot-table: # 注意，这并不代表服务器当前使用的配置
    - "0.50,1,va:COOKED_BEEF"
    - "0.50,3,va:BEEF"
    - "0.50,1,va:COOKED_CHICKEN"
    - "0.50,3,va:CHICKEN"
    - "0.50,1,va:COOKED_PORKCHOP"
    - "0.50,3,va:PORKCHOP"
```

那么每次计时结束都会尝试装填 `randint(5, 12)` 次

每次装填会去遍历这个 `loot-table`

`loot-table` 中的每一项都会被解析为 `概率,物品数量,物品标识符`

如果通过 [概率测试](https://github.com/freeze-dolphin/EtherDecompositor/blob/a875821cc26c0575a0ee78d3704c1bfb882231ab/src/main/kotlin/io/sn/etherdec/modules/ChestAutoStuff.kt#L195C19-L195C19)，这个物品将被摆放在箱子中的随机一个位置

如果未通过，将不摆放任何物品

摆放物品的时候有可能会把上一次装填的物品覆盖掉（两次装填都随机到了同一个格子里）

### 箱子锁区块



