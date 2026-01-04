# 第07天：第一周回顾与迷你项目

## 每周回顾清单
- [ ] UE编辑器设置完成
- [ ] UObject系统已理解
- [ ] 内存管理已清楚
- [ ] 核心类型已掌握
- [ ] 日志/调试正常工作
- [ ] 模块系统已熟悉

## 迷你项目：数据管理器系统

### 需求
创建一个简单的数据管理系统：
1. 使用UObject作为基类
2. 使用TMap存储游戏数据
3. 为所有操作添加日志
4. 使用正确的内存管理

### 实现指南

```cpp
// UDataManager.h
UCLASS()
class YOURPROJECT_API UDataManager : public UObject
{
    GENERATED_BODY()
    
public:
    UFUNCTION(BlueprintCallable)
    void AddData(FName Key, int32 Value);
    
    UFUNCTION(BlueprintCallable)
    int32 GetData(FName Key) const;
    
    UFUNCTION(BlueprintCallable)
    void SaveAllData();
    
private:
    UPROPERTY()
    TMap<FName, int32> DataStore;
};
```

## 每日任务
1. 实现UDataManager类
2. 添加至少5个数据操作
3. 测试所有功能
4. 写一份本周学到的内容总结

## 第一周总结
在这里写下你的关键收获：
1. 
2. 
3. 

## 完成状态
- [ ] 完成所有目标
- [ ] 迷你项目可运行
- [ ] 第一周完成
