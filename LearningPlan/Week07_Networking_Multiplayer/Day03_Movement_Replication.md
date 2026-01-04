# Day 03: Character Movement Replication

## Learning Objectives
- [ ] Understand movement prediction
- [ ] Configure CharacterMovementComponent for network
- [ ] Handle custom movement modes
- [ ] Debug network movement issues

## Core Concepts (20% that matters)

### 1. Movement Replication Flow
```
Client Input → Local Prediction → Server Validation → Correction if needed
                    ↓
              Smooth visuals
```

### 2. CMC Network Configuration
```cpp
AMyCharacter::AMyCharacter()
{
    // Enable replication
    bReplicates = true;
    SetReplicateMovement(true);
    
    // Network smoothing
    GetCharacterMovement()->NetworkSmoothingMode = ENetworkSmoothingMode::Exponential;
    GetCharacterMovement()->NetworkSimulatedSmoothLocationTime = 0.1f;
    GetCharacterMovement()->NetworkSimulatedSmoothRotationTime = 0.1f;
    
    // For custom movement
    GetCharacterMovement()->SetIsReplicated(true);
}
```

### 3. Custom Movement in Network
```cpp
// For custom abilities like dash
void AMyCharacter::StartDash()
{
    if (IsLocallyControlled())
    {
        // Local prediction
        bIsDashing = true;
        GetCharacterMovement()->MaxWalkSpeed = DashSpeed;
        
        // Tell server
        Server_StartDash();
    }
}

void AMyCharacter::Server_StartDash_Implementation()
{
    if (!bIsDashing)
    {
        bIsDashing = true;
        GetCharacterMovement()->MaxWalkSpeed = DashSpeed;
        
        // Tell other clients
        NetMulticast_OnDashStarted();
    }
}

void AMyCharacter::NetMulticast_OnDashStarted_Implementation()
{
    if (!IsLocallyControlled())
    {
        bIsDashing = true;
        GetCharacterMovement()->MaxWalkSpeed = DashSpeed;
        PlayDashEffect();
    }
}
```

### 4. Handling Corrections
```cpp
// Override in Character to handle server corrections
virtual void OnRep_ReplicatedMovement() override
{
    Super::OnRep_ReplicatedMovement();
    
    // Additional handling for custom movement
    if (bIsDashing && !GetCharacterMovement()->IsFalling())
    {
        // Sync dash state
    }
}
```

### 5. Network Debug
```cpp
// Console commands for debugging
// p.NetShowCorrections 1  - Show corrections
// p.NetCorrectionLifetime 2  - How long to show

// In code
#if !UE_BUILD_SHIPPING
void AMyCharacter::DebugNetMovement()
{
    FString RoleStr = StaticEnum<ENetRole>()->GetNameStringByValue((int64)GetLocalRole());
    
    GEngine->AddOnScreenDebugMessage(-1, 0.f, FColor::Green,
        FString::Printf(TEXT("Role: %s, Velocity: %s"), 
            *RoleStr, *GetVelocity().ToString()));
}
#endif
```

## Daily Task
1. Configure character for network movement
2. Implement networked dash ability
3. Test movement prediction
4. Debug any corrections

## Notes
```cpp
// Your notes here
```

## Completion Status
- [ ] Completed all objectives
- [ ] Submitted daily output
