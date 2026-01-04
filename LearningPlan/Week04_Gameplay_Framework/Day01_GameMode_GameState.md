# 第01天：游戏模式与游戏状态

## 学习目标
- [ ] 理解GameMode vs GameState
- [ ] 创建自定义游戏模式
- [ ] 管理游戏规则
- [ ] 实现比赛流程

## 核心概念（二八法则中的20%）

### 1. Gameplay框架
```
AGameModeBase           - 仅服务器，游戏规则
├── AGameMode           - 比赛状态支持
    └── AYourGameMode   - 你的游戏规则

AGameStateBase          - 复制的游戏状态
├── AGameState          - 比赛状态信息
    └── AYourGameState  - 你的游戏数据
```

### 2. 自定义游戏模式
```cpp
// AMyGameMode.h
UCLASS()
class AMyGameMode : public AGameModeBase
{
    GENERATED_BODY()
    
public:
    AMyGameMode();
    
    virtual void InitGame(const FString& MapName, const FString& Options, 
        FString& ErrorMessage) override;
    virtual void StartPlay() override;
    virtual void PostLogin(APlayerController* NewPlayer) override;
    virtual void Logout(AController* Exiting) override;
    
    // 出生点
    virtual AActor* ChoosePlayerStart_Implementation(AController* Player) override;
    
    // 自定义规则
    UFUNCTION(BlueprintCallable)
    void StartMatch();
    
    UFUNCTION(BlueprintCallable)
    void EndMatch(bool bPlayerWon);
    
    UPROPERTY(EditDefaultsOnly, Category = "Game Rules")
    int32 ScoreToWin = 10;
    
    UPROPERTY(EditDefaultsOnly, Category = "Game Rules")
    float MatchTimeLimit = 300.f;
};

// AMyGameMode.cpp
AMyGameMode::AMyGameMode()
{
    // 设置默认类
    DefaultPawnClass = AMyCharacter::StaticClass();
    PlayerControllerClass = AMyPlayerController::StaticClass();
    GameStateClass = AMyGameState::StaticClass();
    PlayerStateClass = AMyPlayerState::StaticClass();
}
```

### 3. 自定义游戏状态
```cpp
// AMyGameState.h
UCLASS()
class AMyGameState : public AGameStateBase
{
    GENERATED_BODY()
    
public:
    UPROPERTY(ReplicatedUsing = OnRep_MatchState, BlueprintReadOnly)
    EMatchState CurrentMatchState;
    
    UPROPERTY(Replicated, BlueprintReadOnly)
    int32 TeamAScore;
    
    UPROPERTY(Replicated, BlueprintReadOnly)
    int32 TeamBScore;
    
    UPROPERTY(BlueprintAssignable)
    FOnScoreChanged OnScoreChanged;
    
    UFUNCTION(BlueprintCallable)
    void AddScore(int32 TeamIndex, int32 Amount);
    
protected:
    UFUNCTION()
    void OnRep_MatchState();
    
    virtual void GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& OutProps) const override;
};
```

### 4. 访问游戏框架
```cpp
// 获取GameMode（仅服务器）
AMyGameMode* GM = Cast<AMyGameMode>(GetWorld()->GetAuthGameMode());

// 获取GameState（所有客户端）
AMyGameState* GS = GetWorld()->GetGameState<AMyGameState>();

// 从任意Actor获取
if (AGameModeBase* GM = UGameplayStatics::GetGameMode(this))
{
    // 使用GM
}
```

## 每日任务
1. 创建自定义GameMode
2. 创建自定义GameState
3. 实现分数追踪
4. 测试比赛开始/结束流程

## 笔记
```cpp
// 在这里写你的笔记
```

## 完成状态
- [ ] 完成所有目标
- [ ] 提交每日输出
