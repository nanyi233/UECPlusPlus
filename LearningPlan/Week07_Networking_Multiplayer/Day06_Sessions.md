# Day 06: Online Subsystem & Sessions

## Learning Objectives
- [ ] Configure Online Subsystem
- [ ] Create and join sessions
- [ ] Implement server browser
- [ ] Handle session callbacks

## Core Concepts (20% that matters)

### 1. Online Subsystem Setup
```cpp
// In Build.cs
PublicDependencyModuleNames.AddRange(new string[] { 
    "OnlineSubsystem", 
    "OnlineSubsystemUtils" 
});

// In DefaultEngine.ini for NULL (LAN)
[OnlineSubsystem]
DefaultPlatformService=NULL

[OnlineSubsystemNull]
bEnabled=true
```

### 2. Session Creation
```cpp
// UOnlineSessionManager.h
UCLASS()
class UOnlineSessionManager : public UObject
{
    GENERATED_BODY()
    
public:
    void CreateSession(int32 MaxPlayers, bool bIsLAN, FString MapName);
    void FindSessions(bool bIsLAN);
    void JoinSession(int32 SessionIndex);
    void DestroySession();
    
    // Delegates
    FOnCreateSessionCompleteDelegate OnCreateSessionCompleteDelegate;
    FOnFindSessionsCompleteDelegate OnFindSessionsCompleteDelegate;
    FOnJoinSessionCompleteDelegate OnJoinSessionCompleteDelegate;
    
private:
    TSharedPtr<FOnlineSessionSearch> SessionSearch;
    FDelegateHandle CreateSessionHandle;
    FDelegateHandle FindSessionsHandle;
    FDelegateHandle JoinSessionHandle;
};

// Implementation
void UOnlineSessionManager::CreateSession(int32 MaxPlayers, bool bIsLAN, FString MapName)
{
    IOnlineSubsystem* OnlineSub = IOnlineSubsystem::Get();
    if (!OnlineSub) return;
    
    IOnlineSessionPtr Sessions = OnlineSub->GetSessionInterface();
    if (!Sessions.IsValid()) return;
    
    FOnlineSessionSettings SessionSettings;
    SessionSettings.bIsLANMatch = bIsLAN;
    SessionSettings.bUsesPresence = true;
    SessionSettings.NumPublicConnections = MaxPlayers;
    SessionSettings.bShouldAdvertise = true;
    SessionSettings.bAllowJoinInProgress = true;
    SessionSettings.Set(FName("MapName"), MapName, EOnlineDataAdvertisementType::ViaOnlineService);
    
    CreateSessionHandle = Sessions->AddOnCreateSessionCompleteDelegate_Handle(
        FOnCreateSessionCompleteDelegate::CreateUObject(this, &UOnlineSessionManager::OnCreateSessionComplete));
    
    Sessions->CreateSession(0, NAME_GameSession, SessionSettings);
}

void UOnlineSessionManager::OnCreateSessionComplete(FName SessionName, bool bWasSuccessful)
{
    IOnlineSubsystem* OnlineSub = IOnlineSubsystem::Get();
    IOnlineSessionPtr Sessions = OnlineSub->GetSessionInterface();
    Sessions->ClearOnCreateSessionCompleteDelegate_Handle(CreateSessionHandle);
    
    if (bWasSuccessful)
    {
        // Travel to map as listen server
        UGameplayStatics::OpenLevel(GetWorld(), FName("GameMap"), true, "listen");
    }
}
```

### 3. Session Finding
```cpp
void UOnlineSessionManager::FindSessions(bool bIsLAN)
{
    IOnlineSubsystem* OnlineSub = IOnlineSubsystem::Get();
    IOnlineSessionPtr Sessions = OnlineSub->GetSessionInterface();
    
    SessionSearch = MakeShareable(new FOnlineSessionSearch());
    SessionSearch->bIsLanQuery = bIsLAN;
    SessionSearch->MaxSearchResults = 20;
    SessionSearch->QuerySettings.Set(SEARCH_PRESENCE, true, EOnlineComparisonOp::Equals);
    
    FindSessionsHandle = Sessions->AddOnFindSessionsCompleteDelegate_Handle(
        FOnFindSessionsCompleteDelegate::CreateUObject(this, &UOnlineSessionManager::OnFindSessionsComplete));
    
    Sessions->FindSessions(0, SessionSearch.ToSharedRef());
}

void UOnlineSessionManager::OnFindSessionsComplete(bool bWasSuccessful)
{
    IOnlineSubsystem* OnlineSub = IOnlineSubsystem::Get();
    IOnlineSessionPtr Sessions = OnlineSub->GetSessionInterface();
    Sessions->ClearOnFindSessionsCompleteDelegate_Handle(FindSessionsHandle);
    
    if (bWasSuccessful && SessionSearch.IsValid())
    {
        // Populate server browser UI
        for (int32 i = 0; i < SessionSearch->SearchResults.Num(); i++)
        {
            FOnlineSessionSearchResult& Result = SessionSearch->SearchResults[i];
            FString MapName;
            Result.Session.SessionSettings.Get(FName("MapName"), MapName);
            
            // Add to UI list
        }
    }
}
```

### 4. Joining Session
```cpp
void UOnlineSessionManager::JoinSession(int32 SessionIndex)
{
    if (!SessionSearch.IsValid() || SessionIndex >= SessionSearch->SearchResults.Num())
        return;
    
    IOnlineSubsystem* OnlineSub = IOnlineSubsystem::Get();
    IOnlineSessionPtr Sessions = OnlineSub->GetSessionInterface();
    
    JoinSessionHandle = Sessions->AddOnJoinSessionCompleteDelegate_Handle(
        FOnJoinSessionCompleteDelegate::CreateUObject(this, &UOnlineSessionManager::OnJoinSessionComplete));
    
    Sessions->JoinSession(0, NAME_GameSession, SessionSearch->SearchResults[SessionIndex]);
}

void UOnlineSessionManager::OnJoinSessionComplete(FName SessionName, EOnJoinSessionCompleteResult::Type Result)
{
    IOnlineSubsystem* OnlineSub = IOnlineSubsystem::Get();
    IOnlineSessionPtr Sessions = OnlineSub->GetSessionInterface();
    Sessions->ClearOnJoinSessionCompleteDelegate_Handle(JoinSessionHandle);
    
    if (Result == EOnJoinSessionCompleteResult::Success)
    {
        FString ConnectString;
        if (Sessions->GetResolvedConnectString(SessionName, ConnectString))
        {
            APlayerController* PC = UGameplayStatics::GetPlayerController(GetWorld(), 0);
            PC->ClientTravel(ConnectString, TRAVEL_Absolute);
        }
    }
}
```

## Daily Task
1. Configure Online Subsystem
2. Implement session creation
3. Create session finder
4. Test LAN multiplayer

## Notes
```cpp
// Your notes here
```

## Completion Status
- [ ] Completed all objectives
- [ ] Submitted daily output
