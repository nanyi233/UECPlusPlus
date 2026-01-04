# Day 05: Dialog & Notification System

## Learning Objectives
- [ ] Create modal dialog widgets
- [ ] Implement notification queue
- [ ] Add floating damage numbers
- [ ] Create loading screen

## Core Concepts (20% that matters)

### 1. Modal Dialog
```cpp
// UConfirmDialogWidget.h
DECLARE_DYNAMIC_MULTICAST_DELEGATE_OneParam(FOnDialogResult, bool, bConfirmed);

UCLASS()
class UConfirmDialogWidget : public UUserWidget
{
    GENERATED_BODY()
    
public:
    UPROPERTY(BlueprintAssignable)
    FOnDialogResult OnDialogResult;
    
    UFUNCTION(BlueprintCallable)
    void ShowDialog(const FText& Title, const FText& Message);
    
protected:
    UPROPERTY(meta = (BindWidget))
    UTextBlock* TitleText;
    
    UPROPERTY(meta = (BindWidget))
    UTextBlock* MessageText;
    
    UPROPERTY(meta = (BindWidget))
    UButton* ConfirmButton;
    
    UPROPERTY(meta = (BindWidget))
    UButton* CancelButton;
    
    UFUNCTION()
    void OnConfirmClicked();
    
    UFUNCTION()
    void OnCancelClicked();
};

// Usage
UConfirmDialogWidget* Dialog = CreateWidget<UConfirmDialogWidget>(PC, DialogClass);
Dialog->OnDialogResult.AddDynamic(this, &AMyActor::HandleDialogResult);
Dialog->ShowDialog(NSLOCTEXT("", "", "Quit Game?"), 
    NSLOCTEXT("", "", "Are you sure you want to quit?"));
```

### 2. Notification System
```cpp
// UNotificationWidget.h
UCLASS()
class UNotificationWidget : public UUserWidget
{
    GENERATED_BODY()
    
public:
    void SetNotification(const FText& Message, ENotificationType Type);
    
protected:
    UPROPERTY(meta = (BindWidget))
    UTextBlock* MessageText;
    
    UPROPERTY(meta = (BindWidgetAnim), Transient)
    UWidgetAnimation* ShowHideAnimation;
    
    FTimerHandle HideTimerHandle;
    
    void HideNotification();
};

// UNotificationManager.h
UCLASS()
class UNotificationManager : public UObject
{
    GENERATED_BODY()
    
public:
    void Initialize(APlayerController* PC);
    
    UFUNCTION(BlueprintCallable)
    void ShowNotification(const FText& Message, ENotificationType Type = ENotificationType::Info);
    
private:
    UPROPERTY()
    TSubclassOf<UNotificationWidget> NotificationClass;
    
    UPROPERTY()
    UVerticalBox* NotificationContainer;
    
    TQueue<FNotificationData> NotificationQueue;
    
    void ProcessQueue();
};
```

### 3. Floating Damage Numbers
```cpp
// UDamageNumberWidget.h
UCLASS()
class UDamageNumberWidget : public UUserWidget
{
    GENERATED_BODY()
    
public:
    void ShowDamage(float Damage, bool bCritical);
    
protected:
    UPROPERTY(meta = (BindWidget))
    UTextBlock* DamageText;
    
    UPROPERTY(meta = (BindWidgetAnim), Transient)
    UWidgetAnimation* FloatUpAnimation;
    
    virtual void OnAnimationFinished_Implementation(const UWidgetAnimation* Animation) override;
};

// Spawning damage number
void AMyCharacter::SpawnDamageNumber(float Damage, FVector WorldLocation)
{
    APlayerController* PC = GetWorld()->GetFirstPlayerController();
    if (!PC) return;
    
    UDamageNumberWidget* Widget = CreateWidget<UDamageNumberWidget>(PC, DamageNumberClass);
    Widget->ShowDamage(Damage, Damage > 50.f);
    Widget->AddToViewport();
    
    // Convert world to screen
    FVector2D ScreenPos;
    PC->ProjectWorldLocationToScreen(WorldLocation, ScreenPos);
    Widget->SetPositionInViewport(ScreenPos);
}
```

### 4. Loading Screen
```cpp
// ULoadingScreenWidget.h
UCLASS()
class ULoadingScreenWidget : public UUserWidget
{
    GENERATED_BODY()
    
public:
    void SetProgress(float Progress);
    void SetLoadingText(const FText& Text);
    
protected:
    UPROPERTY(meta = (BindWidget))
    UProgressBar* LoadingBar;
    
    UPROPERTY(meta = (BindWidget))
    UTextBlock* LoadingText;
    
    UPROPERTY(meta = (BindWidget))
    UThrobber* LoadingThrobber;
};

// Show loading screen during level load
void AMyGameInstance::ShowLoadingScreen()
{
    if (LoadingWidget)
    {
        LoadingWidget->AddToViewport(9999);
    }
}
```

## Daily Task
1. Create confirmation dialog
2. Implement notification queue
3. Add floating damage numbers
4. Create loading screen

## Notes
```cpp
// Your notes here
```

## Completion Status
- [ ] Completed all objectives
- [ ] Submitted daily output
