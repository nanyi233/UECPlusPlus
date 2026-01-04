# 第01天：AActor基础

## 学习目标
- [ ] 理解AActor生命周期
- [ ] 掌握生成/销毁模式
- [ ] 学习BeginPlay、Tick函数
- [ ] 实现Actor间通信

## 核心概念（二八法则中的20%）

### 1. Actor生命周期
```
构造函数 -> PostInitializeComponents -> BeginPlay -> Tick -> EndPlay -> Destroyed
```

### 2. 基础Actor类
```cpp
// AMyActor.h
UCLASS()
class AMyActor : public AActor
{
    GENERATED_BODY()
    
public:
    AMyActor();
    
protected:
    virtual void BeginPlay() override;
    virtual void Tick(float DeltaTime) override;
    virtual void EndPlay(const EEndPlayReason::Type EndPlayReason) override;
    
    UPROPERTY(VisibleAnywhere, BlueprintReadOnly)
    USceneComponent* SceneRoot;
    
    UPROPERTY(VisibleAnywhere, BlueprintReadOnly)
    UStaticMeshComponent* MeshComponent;
};

// AMyActor.cpp
AMyActor::AMyActor()
{
    PrimaryActorTick.bCanEverTick = true;
    
    SceneRoot = CreateDefaultSubobject<USceneComponent>(TEXT("SceneRoot"));
    RootComponent = SceneRoot;
    
    MeshComponent = CreateDefaultSubobject<UStaticMeshComponent>(TEXT("Mesh"));
    MeshComponent->SetupAttachment(SceneRoot);
}

void AMyActor::BeginPlay()
{
    Super::BeginPlay();
    UE_LOG(LogTemp, Log, TEXT("Actor BeginPlay"));
}
```

### 3. 生成Actor
```cpp
// 基础生成
FActorSpawnParameters SpawnParams;
SpawnParams.Owner = this;
SpawnParams.SpawnCollisionHandlingOverride = 
    ESpawnActorCollisionHandlingMethod::AdjustIfPossibleButAlwaysSpawn;

AMyActor* NewActor = GetWorld()->SpawnActor<AMyActor>(
    AMyActor::StaticClass(),
    SpawnLocation,
    SpawnRotation,
    SpawnParams
);

// 使用类引用生成
UPROPERTY(EditAnywhere)
TSubclassOf<AActor> ActorToSpawn;

AActor* Spawned = GetWorld()->SpawnActor<AActor>(ActorToSpawn, Transform);
```

### 4. 查找Actor
```cpp
// 查找单个Actor
AMyActor* Found = Cast<AMyActor>(
    UGameplayStatics::GetActorOfClass(GetWorld(), AMyActor::StaticClass())
);

// 查找所有Actor
TArray<AActor*> FoundActors;
UGameplayStatics::GetAllActorsOfClass(GetWorld(), AMyActor::StaticClass(), FoundActors);

// 使用标签查找
UGameplayStatics::GetAllActorsWithTag(GetWorld(), FName("Enemy"), FoundActors);
```

## 每日任务
1. 创建一个生成器Actor
2. 实现定时器生成/销毁
3. 记录所有生命周期事件
4. 测试不同的生成方法

## 笔记
```cpp
// 在这里写你的笔记
```

## 完成状态
- [ ] 完成所有目标
- [ ] 提交每日输出
