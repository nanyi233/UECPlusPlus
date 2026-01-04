# 第06天：定时器与异步操作

## 学习目标
- [ ] 使用FTimerHandle系统
- [ ] 实现延迟动作
- [ ] 创建循环定时器
- [ ] 处理异步回调

## 核心概念（二八法则中的20%）

### 1. 基础定时器
```cpp
// 在头文件中
FTimerHandle RespawnTimerHandle;

// 设置带委托的定时器
GetWorldTimerManager().SetTimer(
    RespawnTimerHandle,
    this,
    &AMyActor::RespawnPlayer,
    5.0f,      // 延迟
    false      // 是否循环
);

// Lambda定时器
GetWorldTimerManager().SetTimer(
    TimerHandle,
    [this]() { 
        UE_LOG(LogTemp, Log, TEXT("定时器触发！")); 
    },
    2.0f,
    false
);
```

### 2. 定时器控制
```cpp
// 清除定时器
GetWorldTimerManager().ClearTimer(RespawnTimerHandle);

// 暂停/恢复
GetWorldTimerManager().PauseTimer(RespawnTimerHandle);
GetWorldTimerManager().UnPauseTimer(RespawnTimerHandle);

// 检查状态
bool bIsActive = GetWorldTimerManager().IsTimerActive(RespawnTimerHandle);
float Remaining = GetWorldTimerManager().GetTimerRemaining(RespawnTimerHandle);
```

### 3. 循环定时器
```cpp
// 循环定时器
GetWorldTimerManager().SetTimer(
    SpawnTimerHandle,
    this,
    &ASpawner::SpawnEnemy,
    2.0f,      // 间隔
    true,      // 循环
    0.5f       // 首次延迟（可选）
);

// 清除该对象的所有定时器
GetWorldTimerManager().ClearAllTimersForObject(this);
```

### 4. 带参数的定时器
```cpp
// 使用带参数的委托
FTimerDelegate TimerDelegate;
TimerDelegate.BindUFunction(this, FName("DoActionWithParams"), MyParam1, MyParam2);

GetWorldTimerManager().SetTimer(
    TimerHandle,
    TimerDelegate,
    1.0f,
    false
);

// 函数必须是UFUNCTION
UFUNCTION()
void DoActionWithParams(int32 Param1, FString Param2);
```

### 5. 异步任务
```cpp
// 稍后在游戏线程运行
AsyncTask(ENamedThreads::GameThread, [this]()
{
    // 安全访问游戏对象
    UpdateUI();
});

// 后台线程
AsyncTask(ENamedThreads::AnyBackgroundThreadNormalTask, []()
{
    // 重计算
});
```

## 每日任务
1. 创建带定时器的敌人生成器
2. 实现重生系统
3. 为技能添加冷却
4. 测试定时器暂停/恢复

## 笔记
```cpp
// 在这里写你的笔记
```

## 完成状态
- [ ] 完成所有目标
- [ ] 提交每日输出
