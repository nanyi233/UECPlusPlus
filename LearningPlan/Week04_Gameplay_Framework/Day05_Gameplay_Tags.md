# Day 05: Gameplay Tags

## Learning Objectives
- [ ] Understand Gameplay Tag hierarchy
- [ ] Create tag configuration
- [ ] Use tags for ability blocking
- [ ] Implement tag queries

## Core Concepts (20% that matters)

### 1. Tag Hierarchy
```
// Example tag structure in .ini or Data Table
Character.State.Dead
Character.State.Stunned
Character.State.Sprinting
Ability.Attack.Melee
Ability.Attack.Ranged
Ability.Skill.Dash
Damage.Type.Physical
Damage.Type.Fire
Damage.Type.Ice
```

### 2. Tag Configuration (DefaultGameplayTags.ini)
```ini
[/Script/GameplayTags.GameplayTagsSettings]
+GameplayTagList=(Tag="Character.State.Dead",DevComment="Character is dead")
+GameplayTagList=(Tag="Character.State.Stunned",DevComment="Character is stunned")
+GameplayTagList=(Tag="Ability.Attack.Melee",DevComment="Melee attack abilities")
```

### 3. Using Tags in C++
```cpp
// Header declarations
namespace MyGameTags
{
    UE_DECLARE_GAMEPLAY_TAG_EXTERN(Character_State_Dead);
    UE_DECLARE_GAMEPLAY_TAG_EXTERN(Character_State_Stunned);
    UE_DECLARE_GAMEPLAY_TAG_EXTERN(Ability_Attack_Melee);
}

// CPP definitions
namespace MyGameTags
{
    UE_DEFINE_GAMEPLAY_TAG(Character_State_Dead, "Character.State.Dead");
    UE_DEFINE_GAMEPLAY_TAG(Character_State_Stunned, "Character.State.Stunned");
    UE_DEFINE_GAMEPLAY_TAG(Ability_Attack_Melee, "Ability.Attack.Melee");
}

// Usage
FGameplayTag DeadTag = MyGameTags::Character_State_Dead;

// Or request dynamically
FGameplayTag StunnedTag = FGameplayTag::RequestGameplayTag(FName("Character.State.Stunned"));
```

### 4. Tag Containers and Queries
```cpp
// Container operations
FGameplayTagContainer MyTags;
MyTags.AddTag(MyGameTags::Character_State_Stunned);
MyTags.RemoveTag(MyGameTags::Character_State_Stunned);

// Queries
bool bHasTag = MyTags.HasTag(MyGameTags::Character_State_Dead);
bool bHasAny = MyTags.HasAny(OtherContainer);
bool bHasAll = MyTags.HasAll(RequiredTags);

// Parent matching
FGameplayTag ParentTag = FGameplayTag::RequestGameplayTag("Character.State");
bool bHasChildOfState = MyTags.HasTag(ParentTag);  // True if has any State.* tag
```

### 5. Tags in Abilities
```cpp
// In ability constructor
UGA_MeleeAttack::UGA_MeleeAttack()
{
    AbilityTags.AddTag(MyGameTags::Ability_Attack_Melee);
    
    // Block activation if these tags present
    ActivationBlockedTags.AddTag(MyGameTags::Character_State_Dead);
    ActivationBlockedTags.AddTag(MyGameTags::Character_State_Stunned);
    
    // Cancel this ability if these tags added
    CancelAbilitiesWithTag.AddTag(MyGameTags::Ability_Attack_Melee);
    
    // Tags added while active
    ActivationOwnedTags.AddTag(FGameplayTag::RequestGameplayTag("Character.State.Attacking"));
}
```

## Daily Task
1. Set up gameplay tag configuration
2. Create native C++ tag definitions
3. Implement ability blocking with tags
4. Test tag hierarchy matching

## Notes
```cpp
// Your notes here
```

## Completion Status
- [ ] Completed all objectives
- [ ] Submitted daily output
