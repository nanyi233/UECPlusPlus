# 第02天：委托与事件

## 学习目标
- [ ] 理解UE中的委托类型
- [ ] 实现单播委托
- [ ] 实现多播委托
- [ ] 使用动态委托与蓝图配合

## 核心概念（二八法则中的20%）

### 1. 委托类型概述
```
DECLARE_DELEGATE           - 单个订阅者
DECLARE_MULTICAST_DELEGATE - 多个订阅者
DECLARE_DYNAMIC_DELEGATE   - 蓝图兼容
DECLARE_DYNAMIC_MULTICAST_DELEGATE - BP + 多个订阅者
```

### 2. 单播委托
```cpp
// 声明
DECLARE_DELEGATE_OneParam(FOnHealthChanged, float);

// 在类中
FOnHealthChanged OnHealthChanged;

// 绑定
OnHealthChanged.BindUObject(this, &AMyClass::HandleHealthChange);

// 执行
OnHealthChanged.ExecuteIfBound(NewHealth);
```

### 3. 多播委托
```cpp
// 声明
DECLARE_MULTICAST_DELEGATE_TwoParams(FOnGameEvent, FName, int32);

// 在类中
FOnGameEvent OnGameEvent;

// 绑定（多个订阅者）
OnGameEvent.AddUObject(this, &AMyClass::HandleEvent);
FDelegateHandle Handle = OnGameEvent.AddLambda([](FName N, int32 I) { });

// 执行
OnGameEvent.Broadcast(EventName, EventData);

// 解绑
OnGameEvent.Remove(Handle);
```

### 4. 动态委托（蓝图）
```cpp
// 声明 - 必须在头文件中
DECLARE_DYNAMIC_MULTICAST_DELEGATE_OneParam(FOnScoreChanged, int32, NewScore);

// 在UCLASS中
UPROPERTY(BlueprintAssignable, Category = "Events")
FOnScoreChanged OnScoreChanged;

// 广播
OnScoreChanged.Broadcast(100);
```

## 每日任务
1. 创建带OnGameStart委托的GameManager
2. 实现OnScoreChanged动态委托
3. 创建3个订阅者类
4. 测试委托通信

## 笔记
```cpp
// 在这里写你的笔记
```

## 完成状态
- [ ] 完成所有目标
- [ ] 提交每日输出
