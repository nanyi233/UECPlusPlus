# 第03天：UE C++中的接口

## 学习目标
- [ ] 创建UInterface类
- [ ] 在Actor中实现接口
- [ ] 检查接口实现
- [ ] 使用接口实现解耦设计

## 核心概念（二八法则中的20%）

### 1. 接口声明
```cpp
// IInteractable.h
#pragma once
#include "CoreMinimal.h"
#include "UObject/Interface.h"
#include "Interactable.generated.h"

UINTERFACE(MinimalAPI, Blueprintable)
class UInteractable : public UInterface
{
    GENERATED_BODY()
};

class IInteractable
{
    GENERATED_BODY()
    
public:
    // 纯虚函数 - 必须实现
    virtual void Interact(AActor* Caller) = 0;
    
    // 可选，带默认实现
    virtual FString GetInteractionPrompt() { return TEXT("交互"); }
    
    // 蓝图可实现
    UFUNCTION(BlueprintCallable, BlueprintNativeEvent)
    bool CanInteract();
};
```

### 2. 实现接口
```cpp
// AMyChest.h
UCLASS()
class AMyChest : public AActor, public IInteractable
{
    GENERATED_BODY()
    
public:
    // 接口实现
    virtual void Interact(AActor* Caller) override;
    virtual FString GetInteractionPrompt() override;
    virtual bool CanInteract_Implementation() override;
};
```

### 3. 使用接口
```cpp
// 检查并调用
if (AActor* HitActor = GetHitActor())
{
    // 方法1：Cast检查
    if (IInteractable* Interactable = Cast<IInteractable>(HitActor))
    {
        Interactable->Interact(this);
    }
    
    // 方法2：Implements检查
    if (HitActor->Implements<UInteractable>())
    {
        IInteractable::Execute_CanInteract(HitActor);
    }
}
```

## 每日任务
1. 创建IInteractable接口
2. 创建IDamageable接口
3. 在单个Actor上实现两个接口
4. 使用接口创建交互系统

## 笔记
```cpp
// 在这里写你的笔记
```

## 完成状态
- [ ] 完成所有目标
- [ ] 提交每日输出
