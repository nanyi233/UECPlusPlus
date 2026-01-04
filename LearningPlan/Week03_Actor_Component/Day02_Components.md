# 第02天：UActorComponent系统

## 学习目标
- [ ] 创建自定义组件
- [ ] 理解组件注册
- [ ] 实现组件间通信
- [ ] 使用场景组件vs Actor组件

## 核心概念（二八法则中的20%）

### 1. 组件类型
```
UActorComponent          - 仅逻辑，无变换
└── USceneComponent      - 有变换，可附加
    └── UPrimitiveComponent - 有渲染/碰撞
        ├── UStaticMeshComponent
        ├── USkeletalMeshComponent
        └── UShapeComponent
```

### 2. 自定义Actor组件
```cpp
// UHealthComponent.h
UCLASS(ClassGroup=(Custom), meta=(BlueprintSpawnableComponent))
class UHealthComponent : public UActorComponent
{
    GENERATED_BODY()
    
public:
    UHealthComponent();
    
    UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "Health")
    float MaxHealth = 100.f;
    
    UPROPERTY(BlueprintAssignable, Category = "Health")
    FOnHealthChanged OnHealthChanged;
    
    UPROPERTY(BlueprintAssignable, Category = "Health")
    FOnDeath OnDeath;
    
    UFUNCTION(BlueprintCallable, Category = "Health")
    void TakeDamage(float Damage);
    
    UFUNCTION(BlueprintCallable, Category = "Health")
    void Heal(float Amount);
    
    UFUNCTION(BlueprintPure, Category = "Health")
    float GetHealthPercent() const;
    
protected:
    virtual void BeginPlay() override;
    
private:
    float CurrentHealth;
};

// UHealthComponent.cpp
void UHealthComponent::TakeDamage(float Damage)
{
    if (Damage <= 0) return;
    
    CurrentHealth = FMath::Max(CurrentHealth - Damage, 0.f);
    OnHealthChanged.Broadcast(CurrentHealth, MaxHealth);
    
    if (CurrentHealth <= 0.f)
    {
        OnDeath.Broadcast();
    }
}
```

### 3. 场景组件
```cpp
// UWeaponAttachComponent.h
UCLASS(ClassGroup=(Custom), meta=(BlueprintSpawnableComponent))
class UWeaponAttachComponent : public USceneComponent
{
    GENERATED_BODY()
    
public:
    UFUNCTION(BlueprintCallable)
    void AttachWeapon(AActor* Weapon);
    
    UFUNCTION(BlueprintCallable)
    void DetachWeapon();
    
private:
    UPROPERTY()
    AActor* AttachedWeapon;
};
```

### 4. 获取组件
```cpp
// 从Actor获取组件
UHealthComponent* Health = Actor->FindComponentByClass<UHealthComponent>();

// 获取所有组件
TArray<UHealthComponent*> HealthComps;
Actor->GetComponents<UHealthComponent>(HealthComps);

// 获取子级中的组件
UHealthComponent* ChildHealth = Actor->FindComponentByClass<UHealthComponent>();
```

## 每日任务
1. 创建UHealthComponent
2. 创建UInventoryComponent
3. 将两个组件添加到一个Actor
4. 实现组件间通信

## 笔记
```cpp
// 在这里写你的笔记
```

## 完成状态
- [ ] 完成所有目标
- [ ] 提交每日输出
