# 第05天：日志与调试

## 学习目标
- [ ] 掌握UE_LOG宏
- [ ] 创建自定义日志类别
- [ ] 使用Visual Studio调试器配合UE
- [ ] 学习控制台命令

## 核心概念（二八法则中的20%）

### 1. UE_LOG系统
```cpp
// 基础日志
UE_LOG(LogTemp, Warning, TEXT("Hello %s"), *MyString);
UE_LOG(LogTemp, Error, TEXT("Value: %d"), MyInt);

// 日志级别
// Fatal, Error, Warning, Display, Log, Verbose, VeryVerbose
```

### 2. 自定义日志类别
```cpp
// 在.h文件中
DECLARE_LOG_CATEGORY_EXTERN(LogMyGame, Log, All);

// 在.cpp文件中
DEFINE_LOG_CATEGORY(LogMyGame);

// 使用
UE_LOG(LogMyGame, Warning, TEXT("自定义日志消息"));
```

### 3. 屏幕调试
```cpp
// 快速调试消息
GEngine->AddOnScreenDebugMessage(-1, 5.f, FColor::Red, 
    TEXT("调试消息"));

// 调试绘制
DrawDebugLine(GetWorld(), Start, End, FColor::Green, false, 5.f);
DrawDebugSphere(GetWorld(), Location, 50.f, 12, FColor::Blue);
```

### 4. 控制台命令
```cpp
// 创建控制台命令
UFUNCTION(Exec)
void MyConsoleCommand(float Value);

// 在编辑器中：~ 键打开控制台
// 输入：MyConsoleCommand 5.0
```

## 每日任务
1. 为你的项目创建自定义日志类别
2. 在前几天的类中添加日志
3. 实现位置的调试可视化
4. 创建一个控制台命令

## 笔记
```cpp
// 在这里写你的笔记
```

## 完成状态
- [ ] 完成所有目标
- [ ] 提交每日输出
