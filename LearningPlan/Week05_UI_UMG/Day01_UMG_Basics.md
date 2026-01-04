# Day 01: UMG Basics

## Learning Objectives
- [ ] Understand UMG architecture
- [ ] Create UUserWidget in C++
- [ ] Add widgets to viewport
- [ ] Handle widget visibility

## Core Concepts (20% that matters)

### 1. UMG Module Setup
```csharp
// Build.cs
PublicDependencyModuleNames.AddRange(new string[] { 
    "UMG", 
    "Slate", 
    "SlateCore" 
});
```

### 2. Basic User Widget
```cpp
// UMyHUDWidget.h
UCLASS()
class UMyHUDWidget : public UUserWidget
{
    GENERATED_BODY()
    
public:
    UFUNCTION(BlueprintCallable)
    void UpdateHealth(float HealthPercent);
    
    UFUNCTION(BlueprintCallable)
    void UpdateScore(int32 NewScore);
    
protected:
    virtual void NativeConstruct() override;
    virtual void NativeDestruct() override;
    
    // Bind to Blueprint widgets
    UPROPERTY(meta = (BindWidget))
    UProgressBar* HealthBar;
    
    UPROPERTY(meta = (BindWidget))
    UTextBlock* ScoreText;
    
    UPROPERTY(meta = (BindWidgetOptional))
    UTextBlock* AmmoText;
};

// UMyHUDWidget.cpp
void UMyHUDWidget::NativeConstruct()
{
    Super::NativeConstruct();
    
    // Widget is now ready to use
    if (HealthBar)
    {
        HealthBar->SetPercent(1.0f);
    }
}

void UMyHUDWidget::UpdateHealth(float HealthPercent)
{
    if (HealthBar)
    {
        HealthBar->SetPercent(HealthPercent);
    }
}

void UMyHUDWidget::UpdateScore(int32 NewScore)
{
    if (ScoreText)
    {
        ScoreText->SetText(FText::AsNumber(NewScore));
    }
}
```

### 3. Creating and Adding to Viewport
```cpp
// In PlayerController or HUD class
void AMyPlayerController::CreateHUD()
{
    if (HUDWidgetClass && !HUDWidget)
    {
        HUDWidget = CreateWidget<UMyHUDWidget>(this, HUDWidgetClass);
        if (HUDWidget)
        {
            HUDWidget->AddToViewport(0);  // ZOrder
        }
    }
}

void AMyPlayerController::RemoveHUD()
{
    if (HUDWidget)
    {
        HUDWidget->RemoveFromParent();
        HUDWidget = nullptr;
    }
}
```

### 4. Widget Visibility
```cpp
// Show/Hide
HUDWidget->SetVisibility(ESlateVisibility::Visible);
HUDWidget->SetVisibility(ESlateVisibility::Hidden);      // Still ticks
HUDWidget->SetVisibility(ESlateVisibility::Collapsed);   // No space

// Check visibility
bool bIsVisible = HUDWidget->IsVisible();
ESlateVisibility Visibility = HUDWidget->GetVisibility();
```

## Daily Task
1. Create HUD widget class in C++
2. Create Blueprint widget inheriting from C++
3. Add health bar and score text
4. Test widget creation/destruction

## Notes
```cpp
// Your notes here
```

## Completion Status
- [ ] Completed all objectives
- [ ] Submitted daily output
