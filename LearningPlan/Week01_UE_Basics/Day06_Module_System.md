# 第06天：模块系统与构建系统

## 学习目标
- [ ] 理解UE模块架构
- [ ] 学习Build.cs配置
- [ ] 添加模块依赖
- [ ] 创建一个简单模块

## 核心概念（二八法则中的20%）

### 1. 模块类型
```
主游戏模块（Primary Game Module）    - 你的主要游戏代码
插件模块（Plugin Module）            - 可复用的功能
引擎模块（Engine Module）            - 核心引擎功能
```

### 2. Build.cs配置
```csharp
using UnrealBuildTool;

public class MyProject : ModuleRules
{
    public MyProject(ReadOnlyTargetRules Target) : base(Target)
    {
        PCHUsage = PCHUsageMode.UseExplicitOrSharedPCHs;
        
        // 依赖项
        PublicDependencyModuleNames.AddRange(new string[] { 
            "Core", 
            "CoreUObject", 
            "Engine", 
            "InputCore" 
        });
        
        PrivateDependencyModuleNames.AddRange(new string[] { 
            "Slate", 
            "SlateCore" 
        });
    }
}
```

### 3. 常用模块依赖
| 模块 | 用途 |
|------|------|
| Core | 基础类型、容器 |
| CoreUObject | UObject系统 |
| Engine | 游戏引擎 |
| InputCore | 输入处理 |
| UMG | UI系统 |
| AIModule | AI功能 |

## 每日任务
1. 检查你项目的Build.cs
2. 添加一个新的模块依赖
3. 创建一个使用新模块的类
4. 记录模块结构

## 笔记
```cpp
// 在这里写你的笔记
```

## 完成状态
- [ ] 完成所有目标
- [ ] 提交每日输出
