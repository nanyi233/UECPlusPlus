# Day 02: Remote Procedure Calls (RPCs)

## Learning Objectives
- [ ] Understand RPC types
- [ ] Implement Server RPCs
- [ ] Implement Client RPCs
- [ ] Handle RPC validation

## Core Concepts (20% that matters)

### 1. RPC Types
```
Server RPC  - Client → Server (request action)
Client RPC  - Server → Owning Client (send result)
NetMulticast - Server → All Clients (broadcast)
```

### 2. Server RPC
```cpp
// Declaration - called by client, runs on server
UFUNCTION(Server, Reliable, WithValidation)
void Server_TakeDamage(float Damage);

// Implementation
void AMyCharacter::Server_TakeDamage_Implementation(float Damage)
{
    // This runs on server
    Health -= Damage;
    
    if (Health <= 0)
    {
        // Broadcast death to all clients
        NetMulticast_OnDeath();
    }
}

// Validation - prevents cheating
bool AMyCharacter::Server_TakeDamage_Validate(float Damage)
{
    // Return false to reject RPC and disconnect client
    return Damage >= 0 && Damage <= 1000.f;
}
```

### 3. Client RPC
```cpp
// Declaration - runs on owning client only
UFUNCTION(Client, Reliable)
void Client_ShowDamageNumber(float Damage, FVector Location);

// Implementation
void AMyCharacter::Client_ShowDamageNumber_Implementation(float Damage, FVector Location)
{
    // This runs on owning client
    SpawnDamageWidget(Damage, Location);
}
```

### 4. NetMulticast RPC
```cpp
// Declaration - runs on all clients
UFUNCTION(NetMulticast, Reliable)
void NetMulticast_OnDeath();

// Unreliable for frequent calls (animations, effects)
UFUNCTION(NetMulticast, Unreliable)
void NetMulticast_PlayEffect(FVector Location);

// Implementation
void AMyCharacter::NetMulticast_OnDeath_Implementation()
{
    // Runs on server AND all clients
    PlayDeathAnimation();
    DisableCollision();
}
```

### 5. Complete RPC Pattern
```cpp
// Client wants to fire weapon
void AMyCharacter::Fire()
{
    if (!IsLocallyControlled()) return;
    
    // Play effects locally immediately (prediction)
    PlayMuzzleFlash();
    
    // Tell server to process
    Server_Fire(GetAimDirection());
}

void AMyCharacter::Server_Fire_Implementation(FVector Direction)
{
    // Validate and process on server
    if (CanFire())
    {
        // Do hit detection
        FHitResult Hit;
        if (DoLineTrace(Hit, Direction))
        {
            ApplyDamage(Hit.GetActor(), 25.f);
        }
        
        // Tell all clients about the shot
        NetMulticast_OnFire(Direction);
        
        // Update ammo (replicated property)
        Ammo--;
    }
}

void AMyCharacter::NetMulticast_OnFire_Implementation(FVector Direction)
{
    // Skip on locally controlled (already did prediction)
    if (!IsLocallyControlled())
    {
        PlayMuzzleFlash();
    }
    
    // Play sound for everyone
    PlayFireSound();
}
```

## Daily Task
1. Implement Server RPC for damage
2. Create Client RPC for UI feedback
3. Add NetMulticast for effects
4. Test RPC flow in multiplayer

## Notes
```cpp
// Your notes here
```

## Completion Status
- [ ] Completed all objectives
- [ ] Submitted daily output
