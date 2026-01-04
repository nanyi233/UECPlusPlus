# Day 06: World Space UI

## Learning Objectives
- [ ] Create widget components
- [ ] Implement health bars above enemies
- [ ] Create interaction prompts
- [ ] Handle widget facing camera

## Core Concepts (20% that matters)

### 1. Widget Component Setup
```cpp
// In Actor class
UPROPERTY(VisibleAnywhere, BlueprintReadOnly)
UWidgetComponent* HealthBarComponent;

// Constructor
AEnemy::AEnemy()
{
    HealthBarComponent = CreateDefaultSubobject<UWidgetComponent>(TEXT("HealthBar"));
    HealthBarComponent->SetupAttachment(RootComponent);
    HealthBarComponent->SetWidgetSpace(EWidgetSpace::Screen);  // Always face camera
    // Or EWidgetSpace::World for true 3D
    HealthBarComponent->SetDrawSize(FVector2D(100.f, 20.f));
    HealthBarComponent->SetRelativeLocation(FVector(0.f, 0.f, 100.f));  // Above head
}
```

### 2. World Space Health Bar
```cpp
// UWorldHealthBarWidget.h
UCLASS()
class UWorldHealthBarWidget : public UUserWidget
{
    GENERATED_BODY()
    
public:
    UFUNCTION(BlueprintCallable)
    void SetHealthPercent(float Percent);
    
    UFUNCTION(BlueprintCallable)
    void SetOwnerActor(AActor* Owner);
    
protected:
    UPROPERTY(meta = (BindWidget))
    UProgressBar* HealthBar;
    
    UPROPERTY()
    AActor* OwnerActor;
    
    virtual void NativeTick(const FGeometry& MyGeometry, float InDeltaTime) override;
};

// Implementation
void UWorldHealthBarWidget::NativeTick(const FGeometry& MyGeometry, float InDeltaTime)
{
    Super::NativeTick(MyGeometry, InDeltaTime);
    
    // Hide when owner is dead or too far
    if (!OwnerActor || !OwnerActor->IsValidLowLevel())
    {
        SetVisibility(ESlateVisibility::Collapsed);
        return;
    }
    
    // Distance check
    APlayerController* PC = GetOwningPlayer();
    if (PC && PC->GetPawn())
    {
        float Distance = FVector::Dist(OwnerActor->GetActorLocation(), 
            PC->GetPawn()->GetActorLocation());
        
        if (Distance > 2000.f)
        {
            SetVisibility(ESlateVisibility::Collapsed);
        }
        else
        {
            SetVisibility(ESlateVisibility::Visible);
        }
    }
}
```

### 3. Interaction Prompt
```cpp
// AInteractableActor.h
UCLASS()
class AInteractableActor : public AActor
{
    GENERATED_BODY()
    
protected:
    UPROPERTY(VisibleAnywhere)
    UWidgetComponent* InteractionPrompt;
    
    UPROPERTY(EditDefaultsOnly)
    TSubclassOf<UUserWidget> PromptWidgetClass;
    
public:
    void ShowInteractionPrompt();
    void HideInteractionPrompt();
    
    UFUNCTION(BlueprintImplementableEvent)
    FText GetInteractionText();
};

// Implementation
void AInteractableActor::ShowInteractionPrompt()
{
    if (InteractionPrompt)
    {
        InteractionPrompt->SetVisibility(true);
        
        // Update prompt text
        if (UInteractionPromptWidget* Widget = 
            Cast<UInteractionPromptWidget>(InteractionPrompt->GetWidget()))
        {
            Widget->SetPromptText(GetInteractionText());
        }
    }
}
```

### 4. Billboard Widget (Always Face Camera)
```cpp
// Tick function for manual rotation
void AMyActor::Tick(float DeltaTime)
{
    Super::Tick(DeltaTime);
    
    if (WidgetComponent && WidgetComponent->GetWidgetSpace() == EWidgetSpace::World)
    {
        APlayerController* PC = GetWorld()->GetFirstPlayerController();
        if (PC && PC->PlayerCameraManager)
        {
            FRotator CameraRot = PC->PlayerCameraManager->GetCameraRotation();
            FRotator LookAtRot = FRotator(0.f, CameraRot.Yaw + 180.f, 0.f);
            WidgetComponent->SetWorldRotation(LookAtRot);
        }
    }
}
```

## Daily Task
1. Create enemy health bar component
2. Implement interaction prompt widget
3. Add distance-based visibility
4. Test world space UI

## Notes
```cpp
// Your notes here
```

## Completion Status
- [ ] Completed all objectives
- [ ] Submitted daily output
