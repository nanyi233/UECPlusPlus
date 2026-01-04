# Day 01: Networking Basics

## Learning Objectives
- [ ] Understand client-server architecture
- [ ] Learn network roles (Authority, Remote)
- [ ] Implement property replication
- [ ] Create server/client logic separation

## Core Concepts (20% that matters)

### 1. Network Architecture
```
Dedicated Server Model:
┌─────────────────┐
│  Server (DS)    │  ← Has Authority
│  - Game State   │
│  - All Actors   │
└────────┬────────┘
         │ Replicated
    ┌────┴────┐
    ▼         ▼
┌───────┐ ┌───────┐
│Client1│ │Client2│  ← Proxies
└───────┘ └───────┘
```

### 2. Network Role Checks
```cpp
// Check if we have authority (server)
if (HasAuthority())
{
    // Server-only logic
}

// Check local control
if (IsLocallyControlled())
{
    // This client controls this pawn
}

// Get network role
ENetRole Role = GetLocalRole();
// ROLE_Authority     - Server
// ROLE_AutonomousProxy - Owned by this client
// ROLE_SimulatedProxy  - Owned by other client
```

### 3. Property Replication
```cpp
// In header
UPROPERTY(Replicated)
float Health;

UPROPERTY(ReplicatedUsing = OnRep_Health)
float ReplicatedHealth;

// Notify function
UFUNCTION()
void OnRep_Health();

// In cpp - must implement this
void AMyActor::GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& OutLifetimeProps) const
{
    Super::GetLifetimeReplicatedProps(OutLifetimeProps);
    
    DOREPLIFETIME(AMyActor, Health);
    DOREPLIFETIME_CONDITION(AMyActor, ReplicatedHealth, COND_OwnerOnly);
}

void AMyActor::OnRep_Health()
{
    // Called on clients when Health changes
    UpdateHealthUI();
}
```

### 4. Replication Conditions
```cpp
// Common conditions
DOREPLIFETIME(AMyActor, Prop);                           // Always
DOREPLIFETIME_CONDITION(AMyActor, Prop, COND_OwnerOnly); // Only to owner
DOREPLIFETIME_CONDITION(AMyActor, Prop, COND_SkipOwner); // Except owner
DOREPLIFETIME_CONDITION(AMyActor, Prop, COND_InitialOnly); // Once on spawn
```

### 5. Actor Replication Setup
```cpp
AMyActor::AMyActor()
{
    bReplicates = true;
    bAlwaysRelevant = false;
    NetUpdateFrequency = 66.f;  // Updates per second
    MinNetUpdateFrequency = 33.f;
    
    // For movement
    SetReplicateMovement(true);
}
```

## Daily Task
1. Create replicated actor
2. Add health with OnRep
3. Test in PIE multiplayer
4. Observe replication behavior

## Notes
```cpp
// Your notes here
```

## Completion Status
- [ ] Completed all objectives
- [ ] Submitted daily output
