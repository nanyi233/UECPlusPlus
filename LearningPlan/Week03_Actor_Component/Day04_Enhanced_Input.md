# 第04天：增强输入系统

## 学习目标
- [ ] 设置增强输入
- [ ] 创建输入动作
- [ ] 创建输入映射上下文
- [ ] 在C++中实现输入

## 核心概念（二八法则中的20%）

### 1. 增强输入设置
```cpp
// 在Build.cs中
PublicDependencyModuleNames.AddRange(new string[] { 
    "EnhancedInput" 
});
```

### 2. 输入动作（在编辑器中创建）
```
Content/Input/
├── IA_Move.uasset        (InputAction - Axis2D)
├── IA_Look.uasset        (InputAction - Axis2D)
├── IA_Jump.uasset        (InputAction - Digital)
├── IA_Interact.uasset    (InputAction - Digital)
└── IMC_Default.uasset    (InputMappingContext)
```

### 3. C++集成
```cpp
// AMyCharacter.h
#include "InputActionValue.h"

UCLASS()
class AMyCharacter : public ACharacter
{
    GENERATED_BODY()
    
protected:
    // 输入动作
    UPROPERTY(EditAnywhere, BlueprintReadOnly, Category = "Input")
    UInputAction* MoveAction;
    
    UPROPERTY(EditAnywhere, BlueprintReadOnly, Category = "Input")
    UInputAction* LookAction;
    
    UPROPERTY(EditAnywhere, BlueprintReadOnly, Category = "Input")
    UInputAction* JumpAction;
    
    UPROPERTY(EditAnywhere, BlueprintReadOnly, Category = "Input")
    UInputMappingContext* DefaultMappingContext;
    
    // 输入处理函数
    void Move(const FInputActionValue& Value);
    void Look(const FInputActionValue& Value);
    
    virtual void SetupPlayerInputComponent(UInputComponent* Input) override;
};

// AMyCharacter.cpp
void AMyCharacter::BeginPlay()
{
    Super::BeginPlay();
    
    // 添加输入映射上下文
    if (APlayerController* PC = Cast<APlayerController>(Controller))
    {
        if (UEnhancedInputLocalPlayerSubsystem* Subsystem = 
            ULocalPlayer::GetSubsystem<UEnhancedInputLocalPlayerSubsystem>(PC->GetLocalPlayer()))
        {
            Subsystem->AddMappingContext(DefaultMappingContext, 0);
        }
    }
}

void AMyCharacter::SetupPlayerInputComponent(UInputComponent* PlayerInputComponent)
{
    if (UEnhancedInputComponent* EnhancedInput = 
        Cast<UEnhancedInputComponent>(PlayerInputComponent))
    {
        // 移动
        EnhancedInput->BindAction(MoveAction, ETriggerEvent::Triggered, 
            this, &AMyCharacter::Move);
        
        // 视角
        EnhancedInput->BindAction(LookAction, ETriggerEvent::Triggered, 
            this, &AMyCharacter::Look);
        
        // 跳跃
        EnhancedInput->BindAction(JumpAction, ETriggerEvent::Started, 
            this, &ACharacter::Jump);
        EnhancedInput->BindAction(JumpAction, ETriggerEvent::Completed, 
            this, &ACharacter::StopJumping);
    }
}

void AMyCharacter::Move(const FInputActionValue& Value)
{
    FVector2D MoveVector = Value.Get<FVector2D>();
    
    if (Controller)
    {
        const FRotator Rotation = Controller->GetControlRotation();
        const FRotator YawRotation(0, Rotation.Yaw, 0);
        
        const FVector ForwardDir = FRotationMatrix(YawRotation).GetUnitAxis(EAxis::X);
        const FVector RightDir = FRotationMatrix(YawRotation).GetUnitAxis(EAxis::Y);
        
        AddMovementInput(ForwardDir, MoveVector.Y);
        AddMovementInput(RightDir, MoveVector.X);
    }
}
```

## 每日任务
1. 在编辑器中创建输入动作
2. 创建输入映射上下文
3. 在C++中实现输入绑定
4. 测试WASD + 鼠标视角

## 笔记
```cpp
// 在这里写你的笔记
```

## 完成状态
- [ ] 完成所有目标
- [ ] 提交每日输出
