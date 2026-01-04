# 第05天：碰撞与物理

## 学习目标
- [ ] 配置碰撞通道
- [ ] 实现重叠事件
- [ ] 使用射线检测和扫描
- [ ] 应用物理冲量

## 核心概念（二八法则中的20%）

### 1. 碰撞设置
```cpp
// 在构造函数中
UBoxComponent* TriggerBox = CreateDefaultSubobject<UBoxComponent>(TEXT("Trigger"));
TriggerBox->SetCollisionEnabled(ECollisionEnabled::QueryOnly);
TriggerBox->SetCollisionResponseToAllChannels(ECR_Ignore);
TriggerBox->SetCollisionResponseToChannel(ECC_Pawn, ECR_Overlap);
TriggerBox->SetGenerateOverlapEvents(true);

// 绑定事件
TriggerBox->OnComponentBeginOverlap.AddDynamic(this, &AMyActor::OnOverlapBegin);
TriggerBox->OnComponentEndOverlap.AddDynamic(this, &AMyActor::OnOverlapEnd);
```

### 2. 重叠事件
```cpp
UFUNCTION()
void OnOverlapBegin(
    UPrimitiveComponent* OverlappedComp,
    AActor* OtherActor,
    UPrimitiveComponent* OtherComp,
    int32 OtherBodyIndex,
    bool bFromSweep,
    const FHitResult& SweepResult);

void AMyActor::OnOverlapBegin(...)
{
    if (ACharacter* Character = Cast<ACharacter>(OtherActor))
    {
        UE_LOG(LogTemp, Log, TEXT("玩家进入触发区"));
    }
}
```

### 3. 射线检测
```cpp
// 简单射线检测
FHitResult HitResult;
FVector Start = GetActorLocation();
FVector End = Start + GetActorForwardVector() * 1000.f;

FCollisionQueryParams Params;
Params.AddIgnoredActor(this);

bool bHit = GetWorld()->LineTraceSingleByChannel(
    HitResult,
    Start,
    End,
    ECC_Visibility,
    Params
);

if (bHit)
{
    AActor* HitActor = HitResult.GetActor();
    FVector ImpactPoint = HitResult.ImpactPoint;
    FVector ImpactNormal = HitResult.ImpactNormal;
}

// 多重射线检测
TArray<FHitResult> HitResults;
GetWorld()->LineTraceMultiByChannel(HitResults, Start, End, ECC_Pawn);
```

### 4. 球体扫描
```cpp
FHitResult HitResult;
FCollisionShape Shape = FCollisionShape::MakeSphere(50.f);

bool bHit = GetWorld()->SweepSingleByChannel(
    HitResult,
    Start,
    End,
    FQuat::Identity,
    ECC_Pawn,
    Shape
);
```

### 5. 物理冲量
```cpp
// 对组件应用冲量
if (UPrimitiveComponent* PrimComp = Cast<UPrimitiveComponent>(HitResult.GetComponent()))
{
    FVector Impulse = GetActorForwardVector() * 1000.f;
    PrimComp->AddImpulse(Impulse);
    // 或者
    PrimComp->AddImpulseAtLocation(Impulse, HitResult.ImpactPoint);
}
```

## 每日任务
1. 创建带重叠事件的触发区
2. 实现用于交互的射线检测
3. 创建物理投射物
4. 测试所有碰撞场景

## 笔记
```cpp
// 在这里写你的笔记
```

## 完成状态
- [ ] 完成所有目标
- [ ] 提交每日输出
