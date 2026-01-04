# 第06天：UE中的结构体与枚举

## 学习目标
- [ ] 创建USTRUCT类型
- [ ] 创建UENUM类型
- [ ] 在蓝图中使用结构体
- [ ] 实现结构体运算符

## 核心概念（二八法则中的20%）

### 1. USTRUCT声明
```cpp
// FCharacterStats.h
USTRUCT(BlueprintType)
struct FCharacterStats
{
    GENERATED_BODY()
    
    UPROPERTY(EditAnywhere, BlueprintReadWrite)
    float Health = 100.f;
    
    UPROPERTY(EditAnywhere, BlueprintReadWrite)
    float MaxHealth = 100.f;
    
    UPROPERTY(EditAnywhere, BlueprintReadWrite)
    float Stamina = 100.f;
    
    UPROPERTY(EditAnywhere, BlueprintReadWrite)
    int32 Level = 1;
    
    // 构造函数
    FCharacterStats() {}
    FCharacterStats(float InHealth, float InStamina) 
        : Health(InHealth), MaxHealth(InHealth), Stamina(InStamina) {}
    
    // 运算符重载
    bool operator==(const FCharacterStats& Other) const
    {
        return Health == Other.Health && Level == Other.Level;
    }
    
    // 工具函数
    float GetHealthPercent() const { return Health / MaxHealth; }
};
```

### 2. UENUM声明
```cpp
// EWeaponType.h
UENUM(BlueprintType)
enum class EWeaponType : uint8
{
    None        UMETA(DisplayName = "无武器"),
    Sword       UMETA(DisplayName = "剑"),
    Axe         UMETA(DisplayName = "斧"),
    Bow         UMETA(DisplayName = "弓"),
    Magic       UMETA(DisplayName = "魔杖")
};

// 位掩码枚举
UENUM(BlueprintType, meta = (Bitflags, UseEnumValuesAsMaskValuesInEditor = "true"))
enum class ECharacterAbilities : uint8
{
    None        = 0,
    CanJump     = 1 << 0,
    CanRun      = 1 << 1,
    CanSwim     = 1 << 2,
    CanFly      = 1 << 3
};
ENUM_CLASS_FLAGS(ECharacterAbilities);
```

### 3. 在类中使用
```cpp
UCLASS()
class AMyCharacter : public ACharacter
{
    GENERATED_BODY()
    
    UPROPERTY(EditAnywhere, BlueprintReadWrite)
    FCharacterStats Stats;
    
    UPROPERTY(EditAnywhere, BlueprintReadWrite)
    EWeaponType CurrentWeapon;
    
    UPROPERTY(EditAnywhere, BlueprintReadWrite, meta = (Bitmask, BitmaskEnum = "ECharacterAbilities"))
    int32 Abilities;
};
```

## 每日任务
1. 创建FCharacterStats结构体
2. 创建EWeaponType和ECharacterState枚举
3. 在Character类中使用它们
4. 在蓝图编辑器中测试编辑

## 笔记
```cpp
// 在这里写你的笔记
```

## 完成状态
- [ ] 完成所有目标
- [ ] 提交每日输出
