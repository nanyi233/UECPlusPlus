# Day 02: Widget Binding & Data Flow

## Learning Objectives
- [ ] Implement property binding
- [ ] Use widget event dispatchers
- [ ] Create data-driven widgets
- [ ] Handle widget animations

## Core Concepts (20% that matters)

### 1. Property Binding
```cpp
// UInventorySlotWidget.h
UCLASS()
class UInventorySlotWidget : public UUserWidget
{
    GENERATED_BODY()
    
public:
    // Function binding for dynamic values
    UFUNCTION()
    FText GetItemNameText() const;
    
    UFUNCTION()
    FSlateBrush GetItemIcon() const;
    
    // Set data
    void SetItemData(const FInventoryItem& Item);
    
protected:
    UPROPERTY(meta = (BindWidget))
    UTextBlock* ItemNameText;
    
    UPROPERTY(meta = (BindWidget))
    UImage* ItemIcon;
    
    UPROPERTY(meta = (BindWidget))
    UTextBlock* ItemCountText;
    
private:
    FInventoryItem CurrentItem;
};

// In Blueprint: Bind ItemNameText to GetItemNameText()
```

### 2. Event Dispatchers
```cpp
// UInventorySlotWidget.h
DECLARE_DYNAMIC_MULTICAST_DELEGATE_OneParam(FOnSlotClicked, int32, SlotIndex);
DECLARE_DYNAMIC_MULTICAST_DELEGATE_TwoParams(FOnItemDropped, int32, FromSlot, int32, ToSlot);

UCLASS()
class UInventorySlotWidget : public UUserWidget
{
    GENERATED_BODY()
    
public:
    UPROPERTY(BlueprintAssignable, Category = "Events")
    FOnSlotClicked OnSlotClicked;
    
    UPROPERTY(BlueprintAssignable, Category = "Events")
    FOnItemDropped OnItemDropped;
    
    UPROPERTY(EditAnywhere, BlueprintReadWrite)
    int32 SlotIndex;
    
protected:
    virtual FReply NativeOnMouseButtonDown(const FGeometry& Geometry, 
        const FPointerEvent& MouseEvent) override;
};

// Implementation
FReply UInventorySlotWidget::NativeOnMouseButtonDown(
    const FGeometry& Geometry, const FPointerEvent& MouseEvent)
{
    if (MouseEvent.GetEffectingButton() == EKeys::LeftMouseButton)
    {
        OnSlotClicked.Broadcast(SlotIndex);
        return FReply::Handled();
    }
    return Super::NativeOnMouseButtonDown(Geometry, MouseEvent);
}
```

### 3. Widget Animations
```cpp
// In header
UPROPERTY(meta = (BindWidgetAnim), Transient)
UWidgetAnimation* FadeInAnimation;

UPROPERTY(meta = (BindWidgetAnimOptional), Transient)
UWidgetAnimation* SlideAnimation;

// Playing animations
void UMyWidget::ShowWithAnimation()
{
    SetVisibility(ESlateVisibility::Visible);
    
    if (FadeInAnimation)
    {
        PlayAnimation(FadeInAnimation);
    }
}

void UMyWidget::HideWithAnimation()
{
    if (FadeInAnimation)
    {
        PlayAnimationReverse(FadeInAnimation);
        
        FTimerHandle TimerHandle;
        GetWorld()->GetTimerManager().SetTimer(TimerHandle, [this]()
        {
            SetVisibility(ESlateVisibility::Collapsed);
        }, FadeInAnimation->GetEndTime(), false);
    }
}

// Animation events
virtual void OnAnimationFinished_Implementation(const UWidgetAnimation* Animation) override;
```

### 4. ListView Data Binding
```cpp
// Item object
UCLASS(BlueprintType)
class UInventoryItemObject : public UObject
{
    GENERATED_BODY()
public:
    UPROPERTY(BlueprintReadWrite)
    FInventoryItem ItemData;
};

// Entry widget
UCLASS()
class UInventoryEntryWidget : public UUserWidget, public IUserObjectListEntry
{
    GENERATED_BODY()
    
protected:
    virtual void NativeOnListItemObjectSet(UObject* ListItemObject) override;
};
```

## Daily Task
1. Create inventory slot widget
2. Implement click events
3. Add show/hide animations
4. Test data binding

## Notes
```cpp
// Your notes here
```

## Completion Status
- [ ] Completed all objectives
- [ ] Submitted daily output
