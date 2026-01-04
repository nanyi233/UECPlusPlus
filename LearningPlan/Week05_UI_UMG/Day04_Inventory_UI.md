# Day 04: Inventory UI

## Learning Objectives
- [ ] Create grid-based inventory
- [ ] Implement drag and drop
- [ ] Handle item stacking
- [ ] Create item tooltips

## Core Concepts (20% that matters)

### 1. Inventory Grid
```cpp
// UInventoryWidget.h
UCLASS()
class UInventoryWidget : public UUserWidget
{
    GENERATED_BODY()
    
public:
    UFUNCTION(BlueprintCallable)
    void InitializeInventory(int32 Rows, int32 Columns);
    
    UFUNCTION(BlueprintCallable)
    void RefreshInventory(const TArray<FInventoryItem>& Items);
    
protected:
    virtual void NativeConstruct() override;
    
    UPROPERTY(meta = (BindWidget))
    UUniformGridPanel* InventoryGrid;
    
    UPROPERTY(EditDefaultsOnly, Category = "UI")
    TSubclassOf<UInventorySlotWidget> SlotWidgetClass;
    
    UPROPERTY()
    TArray<UInventorySlotWidget*> SlotWidgets;
    
    UFUNCTION()
    void OnSlotClicked(int32 SlotIndex);
    
    UFUNCTION()
    void OnSlotRightClicked(int32 SlotIndex);
};

// UInventoryWidget.cpp
void UInventoryWidget::InitializeInventory(int32 Rows, int32 Columns)
{
    if (!InventoryGrid || !SlotWidgetClass) return;
    
    InventoryGrid->ClearChildren();
    SlotWidgets.Empty();
    
    for (int32 Row = 0; Row < Rows; Row++)
    {
        for (int32 Col = 0; Col < Columns; Col++)
        {
            UInventorySlotWidget* Slot = CreateWidget<UInventorySlotWidget>(
                this, SlotWidgetClass);
            
            int32 Index = Row * Columns + Col;
            Slot->SlotIndex = Index;
            Slot->OnSlotClicked.AddDynamic(this, &UInventoryWidget::OnSlotClicked);
            
            InventoryGrid->AddChildToUniformGrid(Slot, Row, Col);
            SlotWidgets.Add(Slot);
        }
    }
}
```

### 2. Drag and Drop
```cpp
// UDraggableItemWidget.h
UCLASS()
class UDraggableItemWidget : public UUserWidget
{
    GENERATED_BODY()
    
protected:
    virtual FReply NativeOnMouseButtonDown(const FGeometry& Geometry, 
        const FPointerEvent& MouseEvent) override;
    virtual void NativeOnDragDetected(const FGeometry& Geometry, 
        const FPointerEvent& MouseEvent, UDragDropOperation*& OutOperation) override;
};

// Implementation
FReply UDraggableItemWidget::NativeOnMouseButtonDown(
    const FGeometry& Geometry, const FPointerEvent& MouseEvent)
{
    if (MouseEvent.GetEffectingButton() == EKeys::LeftMouseButton)
    {
        return FReply::Handled().DetectDrag(TakeWidget(), EKeys::LeftMouseButton);
    }
    return Super::NativeOnMouseButtonDown(Geometry, MouseEvent);
}

void UDraggableItemWidget::NativeOnDragDetected(
    const FGeometry& Geometry, const FPointerEvent& MouseEvent, 
    UDragDropOperation*& OutOperation)
{
    UItemDragDropOperation* DragOp = NewObject<UItemDragDropOperation>();
    DragOp->Payload = ItemData;
    DragOp->DefaultDragVisual = this;
    DragOp->Pivot = EDragPivot::CenterCenter;
    
    OutOperation = DragOp;
}

// Drop target
virtual bool NativeOnDrop(const FGeometry& Geometry, const FDragDropEvent& DragDropEvent, 
    UDragDropOperation* InOperation) override
{
    if (UItemDragDropOperation* ItemDrag = Cast<UItemDragDropOperation>(InOperation))
    {
        // Handle item drop
        return true;
    }
    return false;
}
```

### 3. Item Tooltip
```cpp
// UItemTooltipWidget.h
UCLASS()
class UItemTooltipWidget : public UUserWidget
{
    GENERATED_BODY()
    
public:
    void SetItemInfo(const FInventoryItem& Item);
    
protected:
    UPROPERTY(meta = (BindWidget))
    UTextBlock* ItemNameText;
    
    UPROPERTY(meta = (BindWidget))
    UTextBlock* ItemDescriptionText;
    
    UPROPERTY(meta = (BindWidget))
    UImage* RarityBorder;
};

// Show tooltip on hover
virtual void NativeOnMouseEnter(const FGeometry& Geometry, 
    const FPointerEvent& MouseEvent) override
{
    if (TooltipWidget)
    {
        TooltipWidget->SetItemInfo(CurrentItem);
        TooltipWidget->SetVisibility(ESlateVisibility::Visible);
    }
}

virtual void NativeOnMouseLeave(const FPointerEvent& MouseEvent) override
{
    if (TooltipWidget)
    {
        TooltipWidget->SetVisibility(ESlateVisibility::Collapsed);
    }
}
```

## Daily Task
1. Create inventory grid system
2. Implement drag and drop
3. Add item tooltips
4. Test item operations

## Notes
```cpp
// Your notes here
```

## Completion Status
- [ ] Completed all objectives
- [ ] Submitted daily output
