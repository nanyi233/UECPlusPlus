# Day 03: Menu System

## Learning Objectives
- [ ] Create main menu widget
- [ ] Implement button navigation
- [ ] Handle input mode switching
- [ ] Create pause menu

## Core Concepts (20% that matters)

### 1. Main Menu Widget
```cpp
// UMainMenuWidget.h
UCLASS()
class UMainMenuWidget : public UUserWidget
{
    GENERATED_BODY()
    
public:
    UFUNCTION(BlueprintCallable)
    void ShowMainMenu();
    
protected:
    virtual void NativeConstruct() override;
    
    UPROPERTY(meta = (BindWidget))
    UButton* PlayButton;
    
    UPROPERTY(meta = (BindWidget))
    UButton* OptionsButton;
    
    UPROPERTY(meta = (BindWidget))
    UButton* QuitButton;
    
    UFUNCTION()
    void OnPlayClicked();
    
    UFUNCTION()
    void OnOptionsClicked();
    
    UFUNCTION()
    void OnQuitClicked();
};

// UMainMenuWidget.cpp
void UMainMenuWidget::NativeConstruct()
{
    Super::NativeConstruct();
    
    if (PlayButton)
    {
        PlayButton->OnClicked.AddDynamic(this, &UMainMenuWidget::OnPlayClicked);
    }
    if (OptionsButton)
    {
        OptionsButton->OnClicked.AddDynamic(this, &UMainMenuWidget::OnOptionsClicked);
    }
    if (QuitButton)
    {
        QuitButton->OnClicked.AddDynamic(this, &UMainMenuWidget::OnQuitClicked);
    }
}

void UMainMenuWidget::OnPlayClicked()
{
    UGameplayStatics::OpenLevel(this, FName("GameLevel"));
}

void UMainMenuWidget::OnQuitClicked()
{
    UKismetSystemLibrary::QuitGame(this, nullptr, EQuitPreference::Quit, false);
}
```

### 2. Pause Menu
```cpp
// UPauseMenuWidget.h
UCLASS()
class UPauseMenuWidget : public UUserWidget
{
    GENERATED_BODY()
    
public:
    UFUNCTION(BlueprintCallable)
    void PauseGame();
    
    UFUNCTION(BlueprintCallable)
    void ResumeGame();
    
protected:
    UPROPERTY(meta = (BindWidget))
    UButton* ResumeButton;
    
    UPROPERTY(meta = (BindWidget))
    UButton* MainMenuButton;
    
    UFUNCTION()
    void OnResumeClicked();
    
    UFUNCTION()
    void OnMainMenuClicked();
};

// UPauseMenuWidget.cpp
void UPauseMenuWidget::PauseGame()
{
    APlayerController* PC = GetOwningPlayer();
    if (PC)
    {
        PC->SetPause(true);
        
        FInputModeUIOnly InputMode;
        InputMode.SetWidgetToFocus(TakeWidget());
        PC->SetInputMode(InputMode);
        PC->SetShowMouseCursor(true);
    }
    
    AddToViewport(100);  // High ZOrder
}

void UPauseMenuWidget::ResumeGame()
{
    APlayerController* PC = GetOwningPlayer();
    if (PC)
    {
        PC->SetPause(false);
        
        FInputModeGameOnly InputMode;
        PC->SetInputMode(InputMode);
        PC->SetShowMouseCursor(false);
    }
    
    RemoveFromParent();
}
```

### 3. Input Mode Management
```cpp
// UI Only - for menus
FInputModeUIOnly UIMode;
UIMode.SetWidgetToFocus(Widget->TakeWidget());
UIMode.SetLockMouseToViewportBehavior(EMouseLockMode::DoNotLock);
PC->SetInputMode(UIMode);

// Game Only - for gameplay
FInputModeGameOnly GameMode;
PC->SetInputMode(GameMode);

// Game and UI - for things like inventory during gameplay
FInputModeGameAndUI GameAndUIMode;
GameAndUIMode.SetWidgetToFocus(Widget->TakeWidget());
GameAndUIMode.SetLockMouseToViewportBehavior(EMouseLockMode::LockAlways);
GameAndUIMode.SetHideCursorDuringCapture(true);
PC->SetInputMode(GameAndUIMode);
```

### 4. Button Styling
```cpp
// In NativeConstruct or function
FButtonStyle NewStyle;
NewStyle.Normal.DrawAs = ESlateBrushDrawType::Image;
NewStyle.Hovered.DrawAs = ESlateBrushDrawType::Image;
NewStyle.Pressed.DrawAs = ESlateBrushDrawType::Image;
// Set brushes, colors, etc.

PlayButton->SetStyle(NewStyle);
```

## Daily Task
1. Create main menu widget
2. Create pause menu with input mode switching
3. Add button hover/click sounds
4. Test complete menu flow

## Notes
```cpp
// Your notes here
```

## Completion Status
- [ ] Completed all objectives
- [ ] Submitted daily output
