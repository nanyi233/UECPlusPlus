# Day 07: Week 5 Review & Mini Project

## Weekly Review Checklist
- [ ] UMG basics understood
- [ ] Widget binding working
- [ ] Menu system complete
- [ ] Inventory UI functional
- [ ] Dialog/notification system implemented
- [ ] World space UI working

## Mini Project: Complete Game UI

### Requirements
Build a complete UI system with:
1. Main menu (Play, Options, Quit)
2. In-game HUD (Health, Ammo, Score)
3. Pause menu with options
4. Inventory with drag and drop
5. World space health bars
6. Notification system

### Architecture
```
UUIManagerSubsystem
├── ShowMainMenu()
├── ShowHUD()
├── ShowPauseMenu()
├── ShowInventory()
└── ShowNotification()

Widget Classes:
├── UMainMenuWidget
├── UHUDWidget
├── UPauseMenuWidget
├── UInventoryWidget
├── UWorldHealthBarWidget
└── UNotificationWidget
```

### Core Implementation

```cpp
// UUIManagerSubsystem.h
UCLASS()
class UUIManagerSubsystem : public UGameInstanceSubsystem
{
    GENERATED_BODY()
    
public:
    virtual void Initialize(FSubsystemCollectionBase& Collection) override;
    
    UFUNCTION(BlueprintCallable)
    void ShowMainMenu();
    
    UFUNCTION(BlueprintCallable)
    void ShowHUD();
    
    UFUNCTION(BlueprintCallable)
    void ShowPauseMenu();
    
    UFUNCTION(BlueprintCallable)
    void HidePauseMenu();
    
    UFUNCTION(BlueprintCallable)
    void ToggleInventory();
    
    UFUNCTION(BlueprintCallable)
    void ShowNotification(const FText& Message, ENotificationType Type);
    
    UFUNCTION(BlueprintPure)
    UHUDWidget* GetHUD() const { return HUDWidget; }
    
private:
    UPROPERTY()
    UMainMenuWidget* MainMenuWidget;
    
    UPROPERTY()
    UHUDWidget* HUDWidget;
    
    UPROPERTY()
    UPauseMenuWidget* PauseMenuWidget;
    
    UPROPERTY()
    UInventoryWidget* InventoryWidget;
    
    // Widget classes
    UPROPERTY(EditDefaultsOnly, Category = "UI Classes")
    TSubclassOf<UMainMenuWidget> MainMenuClass;
    
    // ... other classes
    
    void CreateWidgets(APlayerController* PC);
};
```

## Daily Task
1. Implement complete UI manager
2. Create all widget classes
3. Connect UI to gameplay
4. Write week summary

## Week 5 Summary
Write your key takeaways here:
1. 
2. 
3. 

## Completion Status
- [ ] Completed all objectives
- [ ] Mini project working
- [ ] Week 5 complete
