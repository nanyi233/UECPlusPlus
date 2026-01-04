# 第04天：虚幻引擎核心类型

## 学习目标
- [ ] 掌握FString、FName、FText
- [ ] 学习TArray、TMap、TSet
- [ ] 理解FVector、FRotator、FTransform
- [ ] 练习类型转换

## 核心概念（二八法则中的20%）

### 1. 字符串类型
```cpp
FString MyString = TEXT("Hello World");  // 可变字符串
FName MyName = FName("MyName");          // 不可变，哈希化
FText MyText = NSLOCTEXT("", "", "UI Text");  // 本地化文本

// 类型转换
FName NameFromString = FName(*MyString);
FString StringFromName = MyName.ToString();
```

### 2. 容器类型
```cpp
// 动态数组
TArray<int32> Numbers;
Numbers.Add(1);
Numbers.RemoveAt(0);

// 哈希表
TMap<FString, int32> ScoreMap;
ScoreMap.Add(TEXT("Player1"), 100);

// 哈希集合
TSet<FName> UniqueNames;
UniqueNames.Add(FName("Name1"));
```

### 3. 数学类型
```cpp
FVector Position(100.f, 200.f, 0.f);
FRotator Rotation(0.f, 90.f, 0.f);  // 俯仰角, 偏航角, 翻滚角
FTransform Transform(Rotation, Position, FVector::OneVector);

// 常用操作
float Distance = FVector::Dist(A, B);
FVector Direction = (B - A).GetSafeNormal();
```

## 每日任务
1. 创建一个使用所有字符串类型的数据类
2. 实现基于TMap的背包系统
3. 编写向量数学运算函数
4. 测试所有类型转换是否正确

## 笔记
```cpp
// 在这里写你的笔记
```

## 完成状态
- [ ] 完成所有目标
- [ ] 提交每日输出
