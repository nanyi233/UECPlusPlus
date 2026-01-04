# 第05天：蓝图函数库

## 学习目标
- [ ] 创建蓝图函数库
- [ ] 添加静态实用函数
- [ ] 实现WorldContextObject模式
- [ ] 使用meta说明符

## 核心概念（二八法则中的20%）

### 1. 函数库类
```cpp
// UMyBlueprintLibrary.h
UCLASS()
class UMyBlueprintLibrary : public UBlueprintFunctionLibrary
{
    GENERATED_BODY()
    
public:
    // 简单静态函数
    UFUNCTION(BlueprintCallable, Category = "MyUtils")
    static float CalculateDamage(float BaseDamage, float Multiplier);
    
    // 纯函数（无执行引脚）
    UFUNCTION(BlueprintPure, Category = "MyUtils")
    static FVector GetRandomPointInBox(FVector Center, FVector Extent);
    
    // 带世界上下文
    UFUNCTION(BlueprintCallable, Category = "MyUtils", 
              meta = (WorldContext = "WorldContextObject"))
    static AActor* SpawnActorAtRandomLocation(
        UObject* WorldContextObject, 
        TSubclassOf<AActor> ActorClass);
    
    // 带默认参数
    UFUNCTION(BlueprintCallable, Category = "MyUtils",
              meta = (AdvancedDisplay = "bDebug"))
    static void DoSomething(FString Name, bool bDebug = false);
};
```

### 2. 常用Meta说明符
| Meta | 用途 |
|------|------|
| WorldContext | 自动传递世界上下文 |
| DefaultToSelf | 默认为调用对象 |
| AdvancedDisplay | 在折叠视图中隐藏 |
| DisplayName | 自定义节点名称 |
| Keywords | 搜索关键词 |

### 3. 实现
```cpp
// UMyBlueprintLibrary.cpp
float UMyBlueprintLibrary::CalculateDamage(float BaseDamage, float Multiplier)
{
    return BaseDamage * Multiplier;
}

AActor* UMyBlueprintLibrary::SpawnActorAtRandomLocation(
    UObject* WorldContextObject, 
    TSubclassOf<AActor> ActorClass)
{
    if (UWorld* World = GEngine->GetWorldFromContextObject(
        WorldContextObject, EGetWorldErrorMode::LogAndReturnNull))
    {
        FVector Location = FMath::RandPointInBox(FBox(/* 边界 */));
        return World->SpawnActor<AActor>(ActorClass, Location, FRotator::ZeroRotator);
    }
    return nullptr;
}
```

## 每日任务
1. 创建UGameUtilityLibrary
2. 添加5个有用的静态函数
3. 在蓝图中测试所有函数
4. 记录每个函数的用途

## 笔记
```cpp
// 在这里写你的笔记
```

## 完成状态
- [ ] 完成所有目标
- [ ] 提交每日输出
