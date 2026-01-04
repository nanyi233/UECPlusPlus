# 第07天：第二周回顾与迷你项目

## 每周回顾清单
- [ ] BP/C++集成已掌握
- [ ] 委托已理解
- [ ] 接口已实现
- [ ] 数据资产正常工作
- [ ] 函数库已创建
- [ ] 结构体和枚举已使用

## 迷你项目：交互系统

### 需求
构建一个完整的交互系统：
1. IInteractable接口
2. 多种可交互对象类型
3. 用于UI通知的事件委托
4. 用于交互配置的数据资产

### 架构
```
                    IInteractable
                         |
        +----------------+----------------+
        |                |                |
    APickupItem      AChest          ADoor
        |                |                |
    UItemData      UChestData       UDoorData
```

### 核心类

```cpp
// 交互接口
class IInteractable
{
    virtual void Interact(AActor* Caller) = 0;
    virtual FText GetInteractionText() = 0;
};

// 玩家的交互组件
UCLASS()
class UInteractionComponent : public UActorComponent
{
    UPROPERTY(BlueprintAssignable)
    FOnInteractableFound OnInteractableFound;
    
    UPROPERTY(BlueprintAssignable)
    FOnInteractableLost OnInteractableLost;
    
    void TryInteract();
    void UpdateInteractionTarget();
};
```

## 每日任务
1. 实现完整的交互系统
2. 创建3种不同的可交互类型
3. 连接到UI（显示交互提示）
4. 写周总结

## 第二周总结
在这里写下你的关键收获：
1. 
2. 
3. 

## 完成状态
- [ ] 完成所有目标
- [ ] 迷你项目可运行
- [ ] 第二周完成
