# IceCubesApp → Android Architecture Mapping

## Visual Overview

```
┌─────────────────────────────────────────────────────────────────────────┐
│                           IceCubesApp (iOS)                              │
│                        Swift + SwiftUI + MVVM                            │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
                                    │  PORT TO
                                    ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                          IceCubesApp (Android)                           │
│                  Kotlin + Jetpack Compose + Clean Arch                   │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## Module Structure Comparison

### iOS Package Organization
```
IceCubesApp/
├── Models/                    # Data models, API structures
├── NetworkClient/             # Mastodon API client
├── Env/                       # Environment, DI, state
├── DesignSystem/              # UI components, theming
├── Account/                   # Profile views
├── AppAccount/                # Multi-account management
├── Timeline/                  # Timeline views, caching
├── StatusKit/                 # Status display/composition
├── Notifications/             # Push & in-app notifications
├── Conversations/             # Direct messages
├── Explore/                   # Search, trending
├── Lists/                     # List management
└── MediaUI/                   # Media viewer
```

### Android Module Organization (Proposed)
```
IceCubesApp-Android/
├── app/                       # Main application
├── core/
│   ├── model/                 # ← Models (data classes)
│   ├── network/               # ← NetworkClient (Retrofit)
│   ├── database/              # Room database for caching
│   ├── datastore/             # Preferences storage
│   ├── common/                # ← Env utilities
│   └── designsystem/          # ← DesignSystem (Compose)
└── feature/
    ├── timeline/              # ← Timeline (Compose + Paging)
    ├── status/                # ← StatusKit (Compose)
    ├── account/               # ← Account (profile views)
    ├── auth/                  # ← AppAccount (OAuth)
    ├── notifications/         # ← Notifications (FCM)
    ├── conversations/         # ← Conversations (DMs)
    ├── explore/               # ← Explore (search)
    ├── lists/                 # ← Lists
    └── media/                 # ← MediaUI (ExoPlayer)
```

---

## Technology Stack Mapping

### UI Framework
```
iOS:                           Android:
┌──────────────┐              ┌──────────────┐
│   SwiftUI    │   ─────►     │    Compose   │
│  Declarative │              │  Declarative │
│   @State     │              │ mutableStateOf│
│   @Binding   │              │    remember  │
│  @Observable │              │  StateFlow   │
└──────────────┘              └──────────────┘
```

### Networking
```
iOS:                           Android:
┌──────────────┐              ┌──────────────┐
│  URLSession  │              │   Retrofit   │
│    async/    │   ─────►     │  Coroutines  │
│    await     │              │   suspend    │
└──────────────┘              └──────────────┘
```

### Async/Reactive
```
iOS:                           Android:
┌──────────────┐              ┌──────────────┐
│   Combine    │              │     Flow     │
│ AsyncSequence│   ─────►     │  StateFlow   │
│    Task      │              │ viewModelScope│
└──────────────┘              └──────────────┘
```

### Database
```
iOS:                           Android:
┌──────────────┐              ┌──────────────┐
│    Bodega    │              │     Room     │
│   (SQLite)   │   ─────►     │   (SQLite)   │
│  SwiftData   │              │     DAO      │
└──────────────┘              └──────────────┘
```

### Image Loading
```
iOS:                           Android:
┌──────────────┐              ┌──────────────┐
│     Nuke     │              │     Coil     │
│   Caching    │   ─────►     │   Caching    │
│   Compose    │              │   Compose    │
└──────────────┘              └──────────────┘
```

### Secure Storage
```
iOS:                           Android:
┌──────────────┐              ┌──────────────┐
│ KeychainSwift│              │  Encrypted   │
│   Keychain   │   ─────►     │SharedPrefs   │
│    Secure    │              │   Keystore   │
└──────────────┘              └──────────────┘
```

---

## Data Flow Architecture

### iOS (Current)
```
┌─────────────┐
│    View     │  SwiftUI View
│  (@State)   │
└──────┬──────┘
       │
       ▼
┌─────────────┐
│  ViewModel  │  @Observable class (some views)
│ (Optional)  │  or @State (modern views)
└──────┬──────┘
       │
       ▼
┌─────────────┐
│ Repository  │  Business logic
│  (Service)  │
└──────┬──────┘
       │
       ├─────────────┐
       ▼             ▼
┌─────────────┐ ┌─────────────┐
│   Network   │ │    Cache    │
│  (API call) │ │  (Bodega)   │
└─────────────┘ └─────────────┘
```

### Android (Proposed)
```
┌─────────────┐
│   Compose   │  @Composable function
│    Screen   │
└──────┬──────┘
       │ collectAsState()
       ▼
┌─────────────┐
│  ViewModel  │  StateFlow<UiState>
│ (Hilt/Koin) │  viewModelScope
└──────┬──────┘
       │
       ▼
┌─────────────┐
│   Use Case  │  Business logic (optional)
│ (Optional)  │
└──────┬──────┘
       │
       ▼
┌─────────────┐
│ Repository  │  interface + implementation
│             │
└──────┬──────┘
       │
       ├─────────────┐
       ▼             ▼
┌─────────────┐ ┌─────────────┐
│   Network   │ │  Database   │
│ (Retrofit)  │ │   (Room)    │
└─────────────┘ └─────────────┘
```

---

## Code Example Comparison

### Timeline Screen

#### iOS (SwiftUI)
```swift
struct TimelineView: View {
    @Environment(Client.self) private var client
    @State private var statuses: [Status] = []
    @State private var isLoading = false
    
    var body: some View {
        List(statuses) { status in
            StatusRow(status: status)
        }
        .task {
            await loadTimeline()
        }
        .refreshable {
            await loadTimeline()
        }
    }
    
    private func loadTimeline() async {
        isLoading = true
        defer { isLoading = false }
        
        do {
            statuses = try await client.getHomeTimeline()
        } catch {
            // Handle error
        }
    }
}
```

#### Android (Compose)
```kotlin
@Composable
fun TimelineScreen(
    viewModel: TimelineViewModel = hiltViewModel()
) {
    val uiState by viewModel.uiState.collectAsState()
    
    when (val state = uiState) {
        is TimelineUiState.Loading -> LoadingIndicator()
        is TimelineUiState.Success -> {
            LazyColumn {
                items(state.statuses) { status ->
                    StatusRow(status = status)
                }
            }
        }
        is TimelineUiState.Error -> ErrorView(state.message)
    }
}

class TimelineViewModel @Inject constructor(
    private val repository: TimelineRepository
) : ViewModel() {
    private val _uiState = MutableStateFlow<TimelineUiState>(Loading)
    val uiState: StateFlow<TimelineUiState> = _uiState.asStateFlow()
    
    init {
        loadTimeline()
    }
    
    private fun loadTimeline() {
        viewModelScope.launch {
            repository.getHomeTimeline()
                .catch { _uiState.value = Error(it.message) }
                .collect { _uiState.value = Success(it) }
        }
    }
}
```

---

## Dependency Injection

### iOS (Environment)
```swift
@Observable
class Client {
    // Networking logic
}

struct MyApp: App {
    @State var client = Client()
    
    var body: some Scene {
        WindowGroup {
            ContentView()
                .environment(client)
        }
    }
}

struct SomeView: View {
    @Environment(Client.self) private var client
    // Use client
}
```

### Android (Hilt)
```kotlin
@Module
@InstallIn(SingletonComponent::class)
object NetworkModule {
    @Provides
    @Singleton
    fun provideApiClient(): ApiClient {
        return ApiClient()
    }
}

@HiltViewModel
class SomeViewModel @Inject constructor(
    private val apiClient: ApiClient
) : ViewModel() {
    // Use apiClient
}
```

---

## Navigation

### iOS (NavigationStack)
```swift
struct ContentView: View {
    @State private var path = NavigationPath()
    
    var body: some View {
        NavigationStack(path: $path) {
            TimelineView()
                .navigationDestination(for: Status.self) { status in
                    StatusDetailView(status: status)
                }
        }
    }
}
```

### Android (Navigation Compose)
```kotlin
@Composable
fun AppNavigation() {
    val navController = rememberNavController()
    
    NavHost(navController, startDestination = "timeline") {
        composable("timeline") {
            TimelineScreen(
                onStatusClick = { statusId ->
                    navController.navigate("status/$statusId")
                }
            )
        }
        composable("status/{id}") { backStackEntry ->
            StatusDetailScreen(
                statusId = backStackEntry.arguments?.getString("id")
            )
        }
    }
}
```

---

## State Management Patterns

### iOS
```
@State          → Local, ephemeral view state
@Binding        → Two-way binding between views
@Observable     → Shared state (modern)
@Environment    → Dependency injection
Task { }        → Async operations
.task { }       → Lifecycle-aware async
```

### Android
```
remember { }            → Local, ephemeral composable state
mutableStateOf()        → Observable state
StateFlow               → Shared state (like @Observable)
Hilt/Koin               → Dependency injection
viewModelScope.launch   → Async operations
LaunchedEffect          → Lifecycle-aware async
```

---

## Feature Complexity Mapping

### Low Complexity (Direct Port)
- ✅ Models (Status, Account, etc.) → Kotlin data classes
- ✅ API endpoints → Retrofit interfaces
- ✅ Theme colors → Material 3 colors
- ✅ Simple UI components → Composables

### Medium Complexity (Adaptation Required)
- ⚠️ SwiftUI views → Compose screens (paradigm similar)
- ⚠️ Timeline caching → Room + Paging 3
- ⚠️ Image loading → Coil setup
- ⚠️ Navigation → Navigation Compose

### High Complexity (Significant Work)
- ⚠️ Real-time streaming → OkHttp WebSocket
- ⚠️ Push notifications → FCM + proxy adaptation
- ⚠️ HTML rendering → Jsoup + AnnotatedString
- ⚠️ Multi-account system → Android-specific architecture

---

## Testing Strategy Comparison

### iOS
```
Unit Tests:      XCTest
UI Tests:        XCTest + SwiftUI Preview
Mocking:         Protocol-based mocking
CI/CD:           Xcode Cloud / GitHub Actions
```

### Android
```
Unit Tests:      JUnit 5 + MockK
UI Tests:        Compose Testing + Espresso
Mocking:         MockK
CI/CD:           GitHub Actions
Integration:     MockWebServer
```

---

## Build System

### iOS
```
Build Tool:      Xcode + xcodebuild
Dependencies:    Swift Package Manager
Configuration:   .xcconfig files
Signing:         Xcode automatic signing
```

### Android
```
Build Tool:      Gradle + Kotlin DSL
Dependencies:    Version catalog
Configuration:   build.gradle.kts
Signing:         Keystore + gradle config
```

---

## Platform-Specific Features

### iOS Features to Adapt
- ❌ iCloud sync → Google Drive API or Firebase
- ❌ SF Symbols → Material Icons
- ❌ Apple IAP → Google Play Billing
- ❌ APNs → Firebase FCM
- ❌ Keychain → EncryptedSharedPreferences
- ❌ UIActivityViewController → Share sheet

### Android Unique Features to Add
- ✅ Material You dynamic colors
- ✅ Home screen widgets
- ✅ Picture-in-Picture
- ✅ Split-screen optimization
- ✅ Adaptive icons
- ✅ App shortcuts

---

## Timeline Comparison

### iOS Development (Actual)
```
Initial Release:  ~6 months (estimate)
Current State:    Mature, feature-rich
Team Size:        Small (1-2 developers)
Codebase:         390 Swift files, 13 packages
```

### Android Development (Estimated)
```
Timeline:         12 months (team) or 18-24 (solo)
Team Size:        2-3 developers (recommended)
Expected Result:  Feature parity with iOS
Codebase:         ~400 Kotlin files, 15+ modules
```

---

## Success Metrics

| Metric | iOS (Reference) | Android (Target) |
|--------|----------------|------------------|
| App Size | ~30MB | <50MB |
| Startup Time | <1s | <2s |
| Memory Usage | ~100MB | ~150MB |
| Frame Rate | 60fps | 60fps |
| Crash Rate | <0.5% | <0.5% |
| Store Rating | 4.7 stars | >4.3 stars |

---

## Migration Path

### Phase-by-Phase Equivalence

| Phase | iOS Equivalent | Android Work | Complexity |
|-------|----------------|--------------|------------|
| 1: Foundation | Models + Network packages | Direct port | Low |
| 2: Auth | AppAccount package | Adapt OAuth | Medium |
| 3: Timeline | Timeline + StatusKit | Rewrite UI | High |
| 4: Compose | StatusEditor | Rewrite UI | Medium |
| 5: Notifications | Notifications + Push proxy | Adapt + FCM | High |
| 6: Features | Account, Conversations, Explore, Lists | Port logic, new UI | Medium |
| 7: Polish | Platform features | Android-specific | Medium |
| 8: Quality | Testing | New tests | Medium |
| 9: Launch | App Store | Play Store | Low |

---

## Key Takeaways

### Similarities (Easy to Port)
✅ Declarative UI paradigm (SwiftUI ≈ Compose)
✅ Modern async/await patterns (Task ≈ Coroutines)
✅ Reactive state management (Combine ≈ Flow)
✅ Clean architecture principles
✅ Modular structure

### Differences (Require Adaptation)
⚠️ Language (Swift → Kotlin)
⚠️ Platform APIs (Apple → Android)
⚠️ UI components (SwiftUI → Compose)
⚠️ Build system (SPM → Gradle)
⚠️ Push notifications (APNs → FCM)

### New Opportunities
🎯 Material You theming
🎯 Widgets
🎯 Larger device market
🎯 Google Play distribution
🎯 Android-specific optimizations

---

*This document provides visual architecture mapping. See ANDROID_PORT_PLAN.md for detailed implementation guide.*
