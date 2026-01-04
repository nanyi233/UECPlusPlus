# 第04天：数据资产与软引用

## 学习目标
- [ ] 创建UDataAsset类
- [ ] 理解资产加载策略
- [ ] 使用TSoftObjectPtr进行延迟加载
- [ ] 实现资产注册表查询

## 核心概念（二八法则中的20%）

### 1. 主数据资产
```cpp
// UWeaponData.h
UCLASS(BlueprintType)
class UWeaponData : public UPrimaryDataAsset
{
    GENERATED_BODY()
    
public:
    UPROPERTY(EditDefaultsOnly, BlueprintReadOnly)
    FName WeaponName;
    
    UPROPERTY(EditDefaultsOnly, BlueprintReadOnly)
    float Damage;
    
    UPROPERTY(EditDefaultsOnly, BlueprintReadOnly)
    TSoftObjectPtr<UTexture2D> Icon;
    
    UPROPERTY(EditDefaultsOnly, BlueprintReadOnly)
    TSoftClassPtr<AActor> WeaponClass;
    
    // 用于资产管理器
    virtual FPrimaryAssetId GetPrimaryAssetId() const override;
};
```

### 2. 软引用
```cpp
// 声明
UPROPERTY(EditAnywhere)
TSoftObjectPtr<UTexture2D> LazyTexture;

UPROPERTY(EditAnywhere)
TSoftClassPtr<AActor> LazyActorClass;

// 加载
if (!LazyTexture.IsNull())
{
    UTexture2D* Texture = LazyTexture.LoadSynchronous();
    // 或者异步：
    // StreamableManager.RequestAsyncLoad(LazyTexture.ToSoftObjectPath(), 
    //     FStreamableDelegate::CreateUObject(this, &AMyActor::OnLoaded));
}
```

### 3. 资产管理器
```cpp
// 获取某类型的所有资产
UAssetManager& Manager = UAssetManager::Get();
TArray<FPrimaryAssetId> AssetIds;
Manager.GetPrimaryAssetIdList(FPrimaryAssetType("WeaponData"), AssetIds);

// 加载特定资产
FStreamableHandle Handle = Manager.LoadPrimaryAsset(
    AssetId, 
    TArray<FName>(),
    FStreamableDelegate::CreateLambda([](){ /* 已加载 */ })
);
```

## 每日任务
1. 创建UWeaponData资产类
2. 在编辑器中创建3个武器数据资产
3. 实现延迟加载系统
4. 使用数据资产创建武器选择器

## 笔记
```cpp
// 在这里写你的笔记
```

## 完成状态
- [ ] 完成所有目标
- [ ] 提交每日输出
