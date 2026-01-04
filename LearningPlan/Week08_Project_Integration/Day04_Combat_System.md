# Day 04: Combat & Weapon System

## Learning Objectives
- [ ] Build modular weapon system
- [ ] Implement combo attacks
- [ ] Create projectile system
- [ ] Add damage feedback

## Core Concepts (20% that matters)

### 1. Weapon Base Class
```cpp
// AWeaponBase.h
UCLASS(Abstract)
class AWeaponBase : public AActor
{
    GENERATED_BODY()
    
public:
    AWeaponBase();
    
    virtual void BeginAttack();
    virtual void EndAttack();
    
    UFUNCTION(BlueprintCallable)
    void SetOwnerCharacter(ACharacter* NewOwner);
    
    // Stats
    UPROPERTY(EditDefaultsOnly, BlueprintReadOnly, Category = "Weapon")
    UWeaponData* WeaponData;
    
protected:
    UPROPERTY(VisibleAnywhere)
    USkeletalMeshComponent* WeaponMesh;
    
    UPROPERTY(VisibleAnywhere)
    UBoxComponent* DamageCollision;
    
    UPROPERTY()
    ACharacter* OwnerCharacter;
    
    UFUNCTION()
    void OnDamageOverlap(UPrimitiveComponent* OverlappedComp, AActor* OtherActor,
        UPrimitiveComponent* OtherComp, int32 OtherBodyIndex, bool bFromSweep,
        const FHitResult& SweepResult);
    
    UPROPERTY()
    TSet<AActor*> HitActorsThisSwing;
};

// Implementation
void AWeaponBase::BeginAttack()
{
    HitActorsThisSwing.Empty();
    DamageCollision->SetCollisionEnabled(ECollisionEnabled::QueryOnly);
}

void AWeaponBase::EndAttack()
{
    DamageCollision->SetCollisionEnabled(ECollisionEnabled::NoCollision);
}

void AWeaponBase::OnDamageOverlap(...)
{
    if (OtherActor == OwnerCharacter) return;
    if (HitActorsThisSwing.Contains(OtherActor)) return;
    
    HitActorsThisSwing.Add(OtherActor);
    
    // Apply damage
    if (IDamageable* Damageable = Cast<IDamageable>(OtherActor))
    {
        FDamageInfo DamageInfo;
        DamageInfo.Amount = WeaponData->BaseDamage;
        DamageInfo.Source = OwnerCharacter;
        DamageInfo.HitLocation = SweepResult.ImpactPoint;
        
        Damageable->TakeDamage(DamageInfo);
    }
}
```

### 2. Combo System
```cpp
// UCombatComponent.h
UCLASS()
class UCombatComponent : public UActorComponent
{
    GENERATED_BODY()
    
public:
    UFUNCTION(BlueprintCallable)
    void TryAttack();
    
    UFUNCTION(BlueprintCallable)
    void OnAttackAnimNotify(EAttackNotify NotifyType);
    
protected:
    UPROPERTY(EditDefaultsOnly, Category = "Combat")
    TArray<UAnimMontage*> ComboMontages;
    
    UPROPERTY(EditDefaultsOnly, Category = "Combat")
    float ComboWindowTime = 0.5f;
    
private:
    int32 CurrentComboIndex = 0;
    bool bCanContinueCombo = false;
    bool bComboInputQueued = false;
    FTimerHandle ComboResetHandle;
    
    void PlayComboAttack(int32 Index);
    void ResetCombo();
    void TryContinueCombo();
};

// Implementation
void UCombatComponent::TryAttack()
{
    if (CurrentComboIndex == 0)
    {
        PlayComboAttack(0);
    }
    else if (bCanContinueCombo)
    {
        bComboInputQueued = true;
    }
}

void UCombatComponent::OnAttackAnimNotify(EAttackNotify NotifyType)
{
    switch (NotifyType)
    {
    case EAttackNotify::EnableDamage:
        if (AWeaponBase* Weapon = OwnerCharacter->GetCurrentWeapon())
        {
            Weapon->BeginAttack();
        }
        break;
        
    case EAttackNotify::DisableDamage:
        if (AWeaponBase* Weapon = OwnerCharacter->GetCurrentWeapon())
        {
            Weapon->EndAttack();
        }
        break;
        
    case EAttackNotify::ComboWindowOpen:
        bCanContinueCombo = true;
        break;
        
    case EAttackNotify::ComboWindowClose:
        bCanContinueCombo = false;
        TryContinueCombo();
        break;
        
    case EAttackNotify::AttackEnd:
        if (!bComboInputQueued)
        {
            ResetCombo();
        }
        break;
    }
}

void UCombatComponent::TryContinueCombo()
{
    if (bComboInputQueued && CurrentComboIndex < ComboMontages.Num() - 1)
    {
        PlayComboAttack(CurrentComboIndex + 1);
        bComboInputQueued = false;
    }
}
```

### 3. Projectile System
```cpp
// AProjectileBase.h
UCLASS()
class AProjectileBase : public AActor
{
    GENERATED_BODY()
    
public:
    AProjectileBase();
    
    UFUNCTION(BlueprintCallable)
    void InitializeProjectile(FVector Direction, float Speed, float Damage);
    
protected:
    UPROPERTY(VisibleAnywhere)
    USphereComponent* CollisionComponent;
    
    UPROPERTY(VisibleAnywhere)
    UProjectileMovementComponent* MovementComponent;
    
    UPROPERTY(VisibleAnywhere)
    UStaticMeshComponent* MeshComponent;
    
    UPROPERTY(EditDefaultsOnly)
    UParticleSystem* ImpactEffect;
    
    UPROPERTY(EditDefaultsOnly)
    USoundBase* ImpactSound;
    
    float ProjectileDamage;
    
    UFUNCTION()
    void OnHit(UPrimitiveComponent* HitComp, AActor* OtherActor,
        UPrimitiveComponent* OtherComp, FVector NormalImpulse, const FHitResult& Hit);
};

void AProjectileBase::OnHit(...)
{
    // Apply damage
    if (OtherActor && OtherActor != GetOwner())
    {
        UGameplayStatics::ApplyDamage(OtherActor, ProjectileDamage, 
            GetInstigatorController(), this, nullptr);
    }
    
    // Spawn effects
    if (ImpactEffect)
    {
        UGameplayStatics::SpawnEmitterAtLocation(GetWorld(), ImpactEffect, Hit.ImpactPoint);
    }
    
    if (ImpactSound)
    {
        UGameplayStatics::PlaySoundAtLocation(this, ImpactSound, Hit.ImpactPoint);
    }
    
    Destroy();
}
```

## Daily Task
1. Implement weapon base class
2. Create combo system
3. Build projectile system
4. Test combat flow

## Notes
```cpp
// Your notes here
```

## Completion Status
- [ ] Completed all objectives
- [ ] Submitted daily output
