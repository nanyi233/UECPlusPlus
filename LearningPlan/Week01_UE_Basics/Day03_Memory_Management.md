# 第03天：内存管理与智能指针

## 学习目标
- [ ] 理解UE垃圾回收系统
- [ ] 学习TSharedPtr、TWeakPtr、TUniquePtr
- [ ] 理解UPROPERTY作为GC根的作用
- [ ] 知道何时使用原始指针vs智能指针

## 核心概念（二八法则中的20%）

### 1. 垃圾回收规则
```cpp
// UObject由GC管理 - 必须使用UPROPERTY
UPROPERTY()
UMyObject* MyObject;  // GC保护

UMyObject* DangerousPtr;  // 未受保护 - 可能被GC回收！
```

### 2. 智能指针
```cpp
// 用于非UObject类
TSharedPtr<FMyStruct> SharedPtr = MakeShared<FMyStruct>();
TWeakPtr<FMyStruct> WeakPtr = SharedPtr;
TUniquePtr<FMyStruct> UniquePtr = MakeUnique<FMyStruct>();
```

### 3. 关键规则
| 类型 | 使用场景 |
|------|----------|
| UPROPERTY() | UObject引用 |
| TSharedPtr | 非UObject的共享所有权 |
| TWeakPtr | 非拥有引用 |
| TUniquePtr | 独占所有权 |

### 4. 创建UObject
```cpp
// 创建UObject的正确方式
UMyObject* Obj = NewObject<UMyObject>(this);

// 对于Actor
AActor* Actor = GetWorld()->SpawnActor<AMyActor>();
```

## 每日任务
1. 创建一个同时包含UObject*和TSharedPtr成员的类
2. 编写创建和销毁对象的函数
3. 在编辑器中测试并观察GC行为
4. 记录你的发现

## 笔记
```cpp
// 在这里写你的笔记
```

## 完成状态
- [ ] 完成所有目标
- [ ] 提交每日输出
