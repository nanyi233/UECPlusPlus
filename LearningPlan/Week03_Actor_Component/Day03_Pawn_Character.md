# 第03天：APawn与ACharacter

## 学习目标
- [ ] 理解Pawn vs Character
- [ ] 设置角色移动
- [ ] 实现控制系统
- [ ] 创建自定义Character类

## 核心概念（二八法则中的20%）

### 1. 类层次结构
```
AActor
└── APawn                    - 基础可控制Actor
    └── ACharacter           - 带移动的人形角色
        └── AYourCharacter   - 你的游戏角色
```

### 2. 自定义Character
```cpp
// AMyCharacter.h
UCLASS()
class AMyCharacter : public ACharacter
{
    GENERATED_BODY()
    
public:
    AMyCharacter();
    
    virtual void SetupPlayerInputComponent(UInputComponent* Input) override;
    
protected:
    virtual void BeginPlay() override;
    
    // 组件
    UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = "Camera")
    UCameraComponent* CameraComponent;
    
    UPROPERTY(VisibleAnywhere, BlueprintReadOnly, Category = "Camera")
    USpringArmComponent* SpringArm;
    
    // 移动函数
    void MoveForward(float Value);
    void MoveRight(float Value);
    void StartJump();
    void StopJump();
    void StartSprint();
    void StopSprint();
    
private:
    bool bIsSprinting;
};

// AMyCharacter.cpp
AMyCharacter::AMyCharacter()
{
    // 设置弹簧臂
    SpringArm = CreateDefaultSubobject<USpringArmComponent>(TEXT("SpringArm"));
    SpringArm->SetupAttachment(RootComponent);
    SpringArm->TargetArmLength = 300.f;
    SpringArm->bUsePawnControlRotation = true;
    
    // 设置摄像机
    CameraComponent = CreateDefaultSubobject<UCameraComponent>(TEXT("Camera"));
    CameraComponent->SetupAttachment(SpringArm, USpringArmComponent::SocketName);
    
    // 配置移动
    GetCharacterMovement()->bOrientRotationToMovement = true;
    GetCharacterMovement()->MaxWalkSpeed = 600.f;
}
```

### 3. 角色移动
```cpp
// 移动配置
UCharacterMovementComponent* Movement = GetCharacterMovement();

Movement->MaxWalkSpeed = 600.f;
Movement->MaxWalkSpeedCrouched = 300.f;
Movement->JumpZVelocity = 500.f;
Movement->AirControl = 0.2f;
Movement->GravityScale = 1.0f;

// 移动模式
Movement->SetMovementMode(MOVE_Walking);
Movement->SetMovementMode(MOVE_Flying);
Movement->SetMovementMode(MOVE_Swimming);
```

### 4. 控制
```cpp
// 控制一个Pawn
APlayerController* PC = UGameplayStatics::GetPlayerController(this, 0);
PC->Possess(MyPawn);

// 取消控制
PC->UnPossess();

// 获取被控制的Pawn
APawn* ControlledPawn = PC->GetPawn();
```

## 每日任务
1. 创建自定义Character类
2. 设置带弹簧臂的摄像机
3. 实现基础移动
4. 测试控制/取消控制

## 笔记
```cpp
// 在这里写你的笔记
```

## 完成状态
- [ ] 完成所有目标
- [ ] 提交每日输出
