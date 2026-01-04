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
教学稿件：UObject 系统基础（建议 45–60 分钟）
0. 开场（2 分钟）
今天我们要学习 Unreal Engine 里最核心的一套对象体系：UObject 系统。只要你要做“能被编辑器识别、能被反射、能被蓝图调用、能被垃圾回收管理”的对象，最终几乎都绕不开 UObject。

今天的学习目标与Day02_UObject_System.md一致：

理解 UObject 作为基类的作用

学习 UCLASS、UPROPERTY、UFUNCTION 宏

理解垃圾回收基础

创建你的第一个 UObject 类

1. UObject 到底解决了什么问题？（8 分钟）
在普通 C++ 里，一个类只是“内存 + 函数”。但在 UE 里，我们希望对象具备额外能力：

反射（Reflection）：引擎能“看懂”你的类有哪些属性、函数

编辑器集成：属性能在 Details 面板显示、可编辑

蓝图交互：函数能在蓝图里调用，属性能在蓝图读写

序列化（Serialization）：保存/加载、拷贝、打包资源

GC（垃圾回收）：无需手动 delete，由引擎回收

这些能力的“基础设施”就是 UObject 系统。

你可以把 UObject 理解为：“带反射能力、被引擎托管生命周期的 C++ 对象基类”。

2. 认识 UE 的类层次：你处在什么位置？（5 分钟）
展示层次结构（可直接引用你的学习计划）：

UObject
├── AActor
│   ├── APawn
│   │   └── ACharacter
│   └── APlayerController
├── UActorComponent
└── USubsystem 
讲解要点：

UObject：最底层的“可反射对象”

AActor：能放进关卡、参与世界 Tick、拥有 Transform

UActorComponent：给 Actor 附加能力（移动、渲染、音频等）

USubsystem：全局系统（例如 GameInstance Subsystem）

今天我们先做一个“纯数据/逻辑对象”——自定义 UObject，这是理解系统最好的起点。

3. 三大核心宏：UCLASS UPROPERTY UFUNCTION（15 分钟）
3.1 UCLASS()：告诉引擎“这是可反射的类”
你在 UE 写 UObject 类时，基本长这样（与你的学习计划一致）：

UCLASS()
class YOURPROJECT_API UMyObject : public UObject
{
    GENERATED_BODY()
}; 
讲解要点：

UCLASS()：为 UnrealHeaderTool（UHT）提供元信息，生成反射代码

GENERATED_BODY()：把 UHT 生成的内容插到这里（别手写、别删）

YOURPROJECT_API：模块导出宏，让其他模块也能链接到这个类

3.2 UPROPERTY()：让成员变量“被引擎识别”
如果没有 UPROPERTY：

编辑器看不到

蓝图看不到

GC 可能追踪不到引用（非常关键）

示例（按你学习计划里的说明符风格来讲）：

UPROPERTY(EditAnywhere, BlueprintReadWrite)
float MyValue; 
解释常见说明符（可以对照表格讲）：

EditAnywhere：编辑器里可编辑（细节面板）

VisibleAnywhere：可见但不可编辑

BlueprintReadWrite：蓝图可读可写

Replicated：用于网络同步（今天先记住有这个概念）

重点强调一句：

“UE 里的对象引用，优先用 UPROPERTY 标记，否则 GC 可能不认。”

3.3 UFUNCTION()：让函数“能被蓝图/反射系统调用”
示例：

UFUNCTION(BlueprintCallable)
void MyFunction(); 
解释：

BlueprintCallable：蓝图里能调用（出现一个节点）

（扩展提一句）BlueprintPure：纯函数节点，不执行流

（扩展提一句）BlueprintImplementableEvent：C++ 声明，蓝图实现

4. 垃圾回收（GC）基础：你必须知道的 4 件事（10 分钟）
把 GC 讲清楚，今天就成功一半。

4.1 UE 的 GC 管谁？
主要管理：UObject 体系对象

不直接管理：普通 C++ new 出来的裸对象、STL 容器里的裸指针等

4.2 UE 怎么判断“对象还活着”？
核心原则：可达性（Reachability）

如果一个 UObject 从“根对象（Root）”出发，沿着“引擎认识的引用链”能走到它，它就活着

引擎认识的引用链，最常见就是：被 UPROPERTY 标记的 UObject 指针/容器

4.3 典型坑：没写 UPROPERTY 导致对象被回收
你写了一个 UObject* 成员变量，但没加 UPROPERTY，GC 可能以为“没人引用它”，就把它回收了——然后你一用指针就崩。

结论（本节最重要的一句）：

“成员里保存 UObject 引用，默认都加 UPROPERTY。”

4.4 UObject 如何创建？
普通 C++ 你会 new，但 UObject 要用引擎的创建方式：

常用：NewObject<>()

有 Outer（归属对象）会更容易被正确管理

5. 实战：创建你的第一个 UObject —— UMyDataObject（15–20 分钟）
目标来自你的每日任务：创建 UMyDataObject，加 3 个属性 + 1 个打印日志函数。

5.1 头文件示例（讲解版）
#pragma once

#include "CoreMinimal.h"
#include "UObject/Object.h"
#include "MyDataObject.generated.h"

UCLASS(BlueprintType)
class YOURPROJECT_API UMyDataObject : public UObject
{
    GENERATED_BODY()

public:
    UPROPERTY(EditAnywhere, BlueprintReadWrite, Category="Data")
    int32 Count = 0;

    UPROPERTY(EditAnywhere, BlueprintReadWrite, Category="Data")
    float Ratio = 1.0f;

    UPROPERTY(EditAnywhere, BlueprintReadWrite, Category="Data")
    FString Title = TEXT("Default");

    UFUNCTION(BlueprintCallable, Category="Data")
    void PrintDebug() const;
}; 
讲解点：

BlueprintType：让这个类能在蓝图里作为一种“类型”使用（很常见）

Category：让属性在编辑器/蓝图里分组更清晰

默认值：直接在声明处给，方便可视化与调试

5.2 CPP 文件示例（打印日志）
#include "MyDataObject.h"

void UMyDataObject::PrintDebug() const
{
    UE_LOG(LogTemp, Log, TEXT("UMyDataObject => Count=%d, Ratio=%.2f, Title=%s"),
        Count, Ratio, *Title);
} 
讲解点：

UE_LOG 是 UE 常用日志方式

FString 打印要 *Title

5.3 如何创建并使用它（课堂演示建议）
你可以在任意能运行的地方创建（例如某个 Actor 的 BeginPlay）：

UMyDataObject* Data = NewObject<UMyDataObject>(this);
Data->Count = 42;
Data->PrintDebug(); 
强调：

NewObject<UMyDataObject>(this) 里的 this 作为 Outer，很常见

如果你要把 Data 存为成员变量，请用 UPROPERTY() 保存它

6. 收尾与检查清单（2 分钟）
让学生/自己对照检查（对应你的每日任务）：

是否创建了 UMyDataObject 类

是否包含 3 个 UPROPERTY（int32 float FString）

是否包含 1 个 UFUNCTION，并能 UE_LOG 打印

是否能编译通过

7. 课后思考（可选）
为什么 UE 必须用 UHT + 宏，而不是纯 C++ RTTI？

如果我把 UMyDataObject* 存在成员变量里但没写 UPROPERTY，会发生什么？为什么？

UObject 和 AActor 的核心差别是什么？我该在什么时候选择 UObject？

我做了什么：基于Day02_UObject_System.md的 4 个学习目标与“每日任务”，整理了一份可直接授课的教学稿件，包含讲解节奏、关键概念、代码示例与检查清单。
```

## 完成状态
- [ ] 完成所有目标
- [ ] 提交每日输出
