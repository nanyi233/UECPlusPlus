# Day 04: Actor Spawning & Ownership

## Learning Objectives
- [ ] Understand actor ownership
- [ ] Implement server-authoritative spawning
- [ ] Handle actor relevancy
- [ ] Manage actor lifecycle in network

## Core Concepts (20% that matters)

### 1. Actor Ownership
```cpp
// Owner determines RPC routing
// Owned actors route Client RPCs to owner's connection

void AMyCharacter::SpawnWeapon()
{
    if (HasAuthority())
    {
        FActorSpawnParameters Params;
        Params.Owner = this;  // Critical for RPCs
        Params.Instigator = this;
        
        AWeapon* Weapon = GetWorld()->SpawnActor<AWeapon>(
            WeaponClass, GetActorLocation(), GetActorRotation(), Params);
        
        // Attach
        Weapon->AttachToComponent(GetMesh(), 
            FAttachmentTransformRules::SnapToTargetIncludingScale, 
            FName("weapon_socket"));
            
        CurrentWeapon = Weapon;
    }
}
```

### 2. Server-Authoritative Spawning
```cpp
// Only server should spawn replicated actors
void AMyGameMode::SpawnEnemy(FVector Location)
{
    // GameMode only exists on server
    AEnemy* Enemy = GetWorld()->SpawnActor<AEnemy>(
        EnemyClass, Location, FRotator::ZeroRotator);
    
    // Actor will be replicated to all clients automatically
}

// Client requests spawn
void AMyPlayerController::RequestSpawnProjectile(FVector Direction)
{
    Server_SpawnProjectile(Direction);
}

void AMyPlayerController::Server_SpawnProjectile_Implementation(FVector Direction)
{
    // Server spawns - will replicate
    AProjectile* Projectile = GetWorld()->SpawnActor<AProjectile>(
        ProjectileClass, GetPawn()->GetActorLocation(), Direction.Rotation());
    
    Projectile->SetOwner(GetPawn());
    Projectile->InitVelocity(Direction * ProjectileSpeed);
}
```

### 3. Net Relevancy
```cpp
// Control what actors replicate to which clients
AMyActor::AMyActor()
{
    bAlwaysRelevant = false;  // Use distance-based relevancy
    NetCullDistanceSquared = 150000000.f;  // ~12km
}

// Custom relevancy
virtual bool IsNetRelevantFor(const AActor* RealViewer, const AActor* ViewTarget, 
    const FVector& SrcLocation) const override
{
    // Always relevant to owner
    if (GetOwner() == RealViewer)
    {
        return true;
    }
    
    // Distance check
    float DistSq = (GetActorLocation() - SrcLocation).SizeSquared();
    return DistSq < NetCullDistanceSquared;
}
```

### 4. OnRep for Spawned References
```cpp
UPROPERTY(ReplicatedUsing = OnRep_CurrentWeapon)
AWeapon* CurrentWeapon;

void AMyCharacter::OnRep_CurrentWeapon()
{
    // Called on clients when weapon is assigned
    if (CurrentWeapon)
    {
        // Client-side setup
        CurrentWeapon->AttachToComponent(GetMesh(),
            FAttachmentTransformRules::SnapToTargetIncludingScale,
            FName("weapon_socket"));
        
        UpdateWeaponUI(CurrentWeapon);
    }
}
```

### 5. Handling Actor Destruction
```cpp
void AProjectile::OnHit(AActor* HitActor)
{
    if (HasAuthority())
    {
        // Apply damage on server
        if (HitActor)
        {
            UGameplayStatics::ApplyDamage(HitActor, Damage, GetInstigatorController(), 
                this, nullptr);
        }
        
        // Multicast effect before destroy
        NetMulticast_SpawnHitEffect(GetActorLocation());
        
        // Destroy on server - will replicate
        Destroy();
    }
}
```

## Daily Task
1. Implement server-authoritative weapon spawn
2. Configure actor relevancy
3. Handle weapon attachment replication
4. Test spawn/destroy flow

## Notes
```cpp
// Your notes here
```

## Completion Status
- [ ] Completed all objectives
- [ ] Submitted daily output
