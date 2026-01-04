# 第07天：第三周回顾与迷你项目

## 每周回顾清单
- [ ] Actor生命周期已理解
- [ ] 自定义组件已创建
- [ ] 角色移动正常工作
- [ ] 增强输入已配置
- [ ] 碰撞系统已实现
- [ ] 定时器功能正常

## 迷你项目：俯视射击角色

### 需求
构建一个完整的可玩角色：
1. 俯视摄像机设置
2. WASD移动配合鼠标瞄准
3. 带伤害的生命值组件
4. 带冷却的射击
5. 投射物的碰撞检测

### 架构
```
AShooterCharacter
├── UCameraComponent（俯视）
├── USpringArmComponent
├── UHealthComponent
└── UWeaponComponent
    └── FTimerHandle（射击冷却）

AProjectile
├── USphereComponent（碰撞）
├── UProjectileMovementComponent
└── UStaticMeshComponent
```

### 核心实现

```cpp
// 射击系统
void AShooterCharacter::Fire()
{
    if (!bCanFire) return;
    
    FVector SpawnLocation = GetActorLocation() + GetActorForwardVector() * 100.f;
    FRotator SpawnRotation = GetActorRotation();
    
    AProjectile* Projectile = GetWorld()->SpawnActor<AProjectile>(
        ProjectileClass, SpawnLocation, SpawnRotation);
    
    bCanFire = false;
    GetWorldTimerManager().SetTimer(
        FireCooldownHandle, 
        [this]() { bCanFire = true; }, 
        FireRate, 
        false);
}

// 鼠标瞄准
void AShooterCharacter::AimAtMouse()
{
    APlayerController* PC = Cast<APlayerController>(GetController());
    FVector WorldLocation, WorldDirection;
    
    PC->DeprojectMousePositionToWorld(WorldLocation, WorldDirection);
    
    FPlane GroundPlane(GetActorLocation(), FVector::UpVector);
    FVector Intersection = FMath::LinePlaneIntersection(
        WorldLocation, WorldLocation + WorldDirection * 10000.f, GroundPlane);
    
    FRotator LookRotation = (Intersection - GetActorLocation()).Rotation();
    SetActorRotation(FRotator(0, LookRotation.Yaw, 0));
}
```

## 每日任务
1. 实现完整的射击角色
2. 创建带碰撞的投射物
3. 添加带生命值的敌人目标
4. 写周总结

## 第三周总结
在这里写下你的关键收获：
1. 
2. 
3. 

## 完成状态
- [ ] 完成所有目标
- [ ] 迷你项目可运行
- [ ] 第三周完成
