# Day 07: Week 6 Review & Mini Project

## Weekly Review Checklist
- [ ] AI Controller working
- [ ] Navigation setup complete
- [ ] Behavior Tree functional
- [ ] EQS queries created
- [ ] Combat behaviors implemented
- [ ] Crowd AI basics understood

## Mini Project: Enemy AI System

### Requirements
Build a complete enemy AI system with:
1. Multiple enemy types (Melee, Ranged)
2. Patrol → Chase → Attack behavior
3. Cover-seeking for ranged enemies
4. Squad coordination
5. Death and respawn handling

### Architecture
```
AAIEnemyBase
├── UHealthComponent
├── UBlackboardComponent
└── Behavior Tree

AAIMeleeEnemy : AAIEnemyBase
├── Chase → Attack at close range
└── Strafe around target

AAIRangedEnemy : AAIEnemyBase
├── Find cover position (EQS)
├── Attack from distance
└── Retreat when low health

UAISquadManager (World Subsystem)
├── Coordinate attack timing
├── Share target information
└── Manage formations
```

### Behavior Tree Structure
```
Root
├── Selector
│   ├── Sequence [Dead]
│   │   ├── Decorator: IsDead
│   │   └── Task: HandleDeath
│   │
│   ├── Sequence [Combat]
│   │   ├── Decorator: HasTarget
│   │   ├── Selector
│   │   │   ├── Sequence [Attack]
│   │   │   │   ├── Decorator: InAttackRange
│   │   │   │   └── Task: Attack
│   │   │   │
│   │   │   └── Task: MoveToTarget
│   │   
│   └── Sequence [Patrol]
│       ├── Task: FindPatrolPoint
│       └── Task: MoveToLocation
```

### Core Implementation

```cpp
// Squad coordination
void UAISquadManager::OnTargetAcquired(AActor* Target, AAIController* Reporter)
{
    // Share target with squad members
    int32 SquadID = GetSquadID(Reporter);
    TArray<AAIController*> SquadMembers = GetSquadMembers(SquadID);
    
    for (AAIController* Member : SquadMembers)
    {
        if (Member != Reporter)
        {
            UBlackboardComponent* BB = Member->GetBlackboardComponent();
            if (BB && !BB->GetValueAsObject(FName("TargetActor")))
            {
                BB->SetValueAsObject(FName("TargetActor"), Target);
            }
        }
    }
    
    // Assign roles
    AssignCombatRoles(SquadID, Target);
}

void UAISquadManager::AssignCombatRoles(int32 SquadID, AActor* Target)
{
    TArray<AAIController*> SquadMembers = GetSquadMembers(SquadID);
    
    // First two engage directly
    // Others flank
    for (int32 i = 0; i < SquadMembers.Num(); i++)
    {
        UBlackboardComponent* BB = SquadMembers[i]->GetBlackboardComponent();
        if (i < 2)
        {
            BB->SetValueAsEnum(FName("CombatRole"), static_cast<uint8>(ECombatRole::Engage));
        }
        else
        {
            BB->SetValueAsEnum(FName("CombatRole"), static_cast<uint8>(ECombatRole::Flank));
        }
    }
}
```

## Daily Task
1. Implement complete enemy AI system
2. Create two enemy types
3. Add squad coordination
4. Write week summary

## Week 6 Summary
Write your key takeaways here:
1. 
2. 
3. 

## Completion Status
- [ ] Completed all objectives
- [ ] Mini project working
- [ ] Week 6 complete
