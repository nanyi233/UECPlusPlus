# 第01天：蓝图与C++集成基础

## 学习目标
- [ ] 理解蓝图vs C++的权衡
- [ ] 学习BlueprintCallable、BlueprintImplementableEvent
- [ ] 创建可在蓝图中使用的C++类
- [ ] 将C++变量暴露给蓝图

## 核心概念（二八法则中的20%）

### 1. 何时使用什么
| 特性 | 蓝图 | C++ |
|------|------|-----|
| 快速原型 | ✓ | |
| 性能关键 | | ✓ |
| 复杂数学 | | ✓ |
| 设计师迭代 | ✓ | |
| 核心系统 | | ✓ |

### 2. 暴露给蓝图
```cpp
UCLASS(Blueprintable)  // 可创建BP子类
class YOURPROJECT_API AMyActor : public AActor
{
    GENERATED_BODY()
    
public:
    // 在编辑器中可编辑，在BP中可读写
    UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "Config")
    float Health = 100.f;
    
    // 可从蓝图调用
    UFUNCTION(BlueprintCallable, Category = "Actions")
    void TakeDamage(float Amount);
    
    // 在蓝图中重写
    UFUNCTION(BlueprintImplementableEvent, Category = "Events")
    void OnHealthChanged();
    
    // C++实现，BP可选重写
    UFUNCTION(BlueprintNativeEvent, Category = "Events")
    void OnDeath();
    virtual void OnDeath_Implementation();
};
```

### 3. 关键说明符
| 说明符 | 用途 |
|--------|------|
| BlueprintCallable | 从BP调用 |
| BlueprintPure | 无副作用，无执行引脚 |
| BlueprintImplementableEvent | BP必须实现 |
| BlueprintNativeEvent | C++默认实现，BP可重写 |

## 每日任务
1. 创建一个带5个暴露属性的C++ Actor
2. 添加3个BlueprintCallable函数
3. 创建一个蓝图子类
4. 在编辑器中测试集成

## 笔记
```cpp
// 在这里写你的笔记
```

## 完成状态
- [ ] 完成所有目标
- [ ] 提交每日输出
