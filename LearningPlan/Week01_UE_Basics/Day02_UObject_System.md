# 第02天：UObject系统基础

## 学习目标
- [ ] 理解UObject作为基类的作用
- [ ] 学习UCLASS、UPROPERTY、UFUNCTION宏
- [ ] 理解垃圾回收基础
- [ ] 创建你的第一个UObject类

## 核心概念（二八法则中的20%）

### 1. UObject层次结构
```
UObject
├── AActor
│   ├── APawn
│   │   └── ACharacter
│   └── APlayerController
├── UActorComponent
└── USubsystem
```

### 2. 核心宏
```cpp
UCLASS()
class YOURPROJECT_API UMyObject : public UObject
{
    GENERATED_BODY()
    
    UPROPERTY(EditAnywhere, BlueprintReadWrite)
    float MyValue;
    
    UFUNCTION(BlueprintCallable)
    void MyFunction();
};
```

### 3. 属性说明符
| 说明符 | 描述 |
|--------|------|
| EditAnywhere | 在编辑器中可编辑 |
| BlueprintReadWrite | 在蓝图中可读写 |
| VisibleAnywhere | 可见但不可编辑 |
| Replicated | 网络复制 |

## 每日任务
1. 创建一个自定义UObject类 `UMyDataObject`
2. 添加3个UPROPERTY变量（int、float、FString）
3. 添加1个打印日志的UFUNCTION
4. 编译并验证没有错误

## 笔记
```cpp
// 在这里写你的笔记
```

## 完成状态
- [ ] 完成所有目标
- [ ] 提交每日输出
