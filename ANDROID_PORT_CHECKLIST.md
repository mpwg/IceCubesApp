# Android Port - Phase Checklist

Use this checklist to track progress through the 9-phase implementation plan.

## 📦 Phase 1: Foundation (Months 1-2)

### 1.1 Project Setup
- [ ] Initialize Android Studio project with Kotlin
- [ ] Set up Gradle multi-module structure (core/, feature/, app/)
- [ ] Configure build variants (debug, staging, release)
- [ ] Set up GitHub Actions CI/CD pipeline
- [ ] Configure ktlint for code formatting
- [ ] Configure detekt for static analysis
- [ ] Set up version catalog (libs.versions.toml)
- [ ] Create .gitignore for Android artifacts

### 1.2 Core Module - Models & Networking
- [ ] Create core-model module
  - [ ] Port Status model to Kotlin data class
  - [ ] Port Account model to Kotlin data class
  - [ ] Port Notification model to Kotlin data class
  - [ ] Port MediaAttachment, Poll, Card models
  - [ ] Add kotlinx.serialization annotations
  - [ ] Add Jsoup for HTML parsing
- [ ] Create core-network module
  - [ ] Add Retrofit and OkHttp dependencies
  - [ ] Create MastodonApi interface (home timeline endpoint)
  - [ ] Create auth interceptor for OAuth tokens
  - [ ] Implement error handling and retry logic
  - [ ] Add JSON decoder with kotlinx.serialization
  - [ ] Write unit tests with MockWebServer
- [ ] Create core-common module
  - [ ] Add shared utilities and extensions
  - [ ] Add result wrapper classes

### 1.3 Design System
- [ ] Create core-designsystem module
  - [ ] Set up Material 3 theme (Theme.kt)
  - [ ] Define color schemes (light/dark)
  - [ ] Define typography scale
  - [ ] Create IceCubesColors (custom theme colors)
  - [ ] Create reusable Button components
  - [ ] Create reusable TextField components
  - [ ] Create Card component
  - [ ] Create LoadingIndicator component
  - [ ] Create ErrorView component
- [ ] Add Emoji2 for custom emoji support
- [ ] Create preview utilities for Compose

### 1.4 Validation
- [ ] App compiles without errors
- [ ] All unit tests pass
- [ ] Lint and static analysis pass
- [ ] Models serialize/deserialize correctly
- [ ] API client can make test request
- [ ] Design system previews render correctly

---

## 🔐 Phase 2: Authentication (Month 3)

### 2.1 OAuth Flow
- [ ] Create feature-auth module
- [ ] Implement instance input screen
- [ ] Create OAuth 2.0 flow with Custom Tabs
  - [ ] App registration endpoint call
  - [ ] Authorization URL generation
  - [ ] Deep link handling for redirect
  - [ ] Token exchange
- [ ] Add EncryptedSharedPreferences for token storage
- [ ] Implement token refresh logic
- [ ] Add instance discovery/validation

### 2.2 Multi-Account Support
- [ ] Create AccountManager service
  - [ ] Add/remove account
  - [ ] Switch active account
  - [ ] List all accounts
  - [ ] Get current account as Flow
- [ ] Create account selection UI
- [ ] Add account switcher component
- [ ] Implement secure token per account
- [ ] Add logout functionality

### 2.3 Validation
- [ ] Can log in to Mastodon instance
- [ ] Token stored securely
- [ ] Can add multiple accounts
- [ ] Can switch between accounts
- [ ] Token refresh works automatically
- [ ] Can log out and clear data

---

## 📱 Phase 3: Timeline & Status Display (Months 4-5)

### 3.1 Database & Caching
- [ ] Create core-database module
  - [ ] Set up Room database
  - [ ] Create StatusEntity and AccountEntity
  - [ ] Create DAOs for cache operations
  - [ ] Implement database migrations
- [ ] Create DataStore for preferences
  - [ ] Timeline position storage
  - [ ] Last sync timestamp

### 3.2 Timeline Implementation
- [ ] Create feature-timeline module
- [ ] Implement TimelineRepository
  - [ ] Network + cache strategy
  - [ ] Paging 3 integration
  - [ ] Mark as read logic
- [ ] Create TimelineViewModel with StateFlow
- [ ] Build timeline UI with Compose
  - [ ] LazyColumn with Paging
  - [ ] Pull-to-refresh
  - [ ] Loading states
  - [ ] Empty state
  - [ ] Error state
- [ ] Add timeline type selector (home/local/federated)
- [ ] Implement unread counter

### 3.3 Status Display
- [ ] Create feature-status module
- [ ] Build StatusRow composable
  - [ ] Account header (avatar, name, time)
  - [ ] Content text with HTML rendering
  - [ ] Media grid (images/videos)
  - [ ] Poll display
  - [ ] Card preview
  - [ ] Action buttons (reply, boost, favorite, share)
- [ ] Implement custom emoji rendering
- [ ] Add hashtag/mention click handlers
- [ ] Implement content warning UI
- [ ] Add sensitive media blur

### 3.4 Real-time Updates
- [ ] Implement OkHttp WebSocket client
- [ ] Create StreamingService
  - [ ] Auto-reconnection logic
  - [ ] Exponential backoff
  - [ ] Lifecycle awareness
- [ ] Parse streaming events
- [ ] Update timeline with live events
- [ ] Show "new posts" indicator

### 3.5 Validation
- [ ] Timeline loads and displays statuses
- [ ] Infinite scroll works smoothly (60fps)
- [ ] Images load with Coil
- [ ] Can navigate to different timeline types
- [ ] Offline mode shows cached content
- [ ] Position restored after app restart
- [ ] Live updates appear in real-time
- [ ] Custom emojis render correctly

---

## ✍️ Phase 4: Status Composition (Month 6)

### 4.1 Composer UI
- [ ] Create status composer screen
- [ ] Build multi-line text editor
  - [ ] Character counter
  - [ ] Auto-complete for @mentions
  - [ ] Auto-complete for #hashtags
- [ ] Add custom emoji picker dialog
- [ ] Implement media picker
  - [ ] Image selection (up to 4)
  - [ ] Video selection
  - [ ] Alt text editor per image
- [ ] Add poll creation UI
- [ ] Add visibility selector (public/unlisted/private/direct)
- [ ] Add content warning toggle and input
- [ ] Add language detection and selector

### 4.2 Posting Logic
- [ ] Implement media upload API calls
- [ ] Implement status post API call
- [ ] Add progress indicators for upload
- [ ] Handle post errors gracefully
- [ ] Add post success confirmation

### 4.3 Advanced Features
- [ ] Implement draft saving to Room
- [ ] Add reply context display
- [ ] Add quote post support
- [ ] Thread composer (multi-post)
- [ ] Scheduled posts (if API supports)

### 4.4 Validation
- [ ] Can compose and post text-only status
- [ ] Can attach and post images
- [ ] Can attach and post videos
- [ ] Alt text saved correctly
- [ ] Polls work end-to-end
- [ ] Content warnings work
- [ ] Drafts save and restore
- [ ] Character limit enforced

---

## 🔔 Phase 5: Notifications (Month 7)

### 5.1 Push Notifications
- [ ] Add Firebase to project
- [ ] Implement FCM token registration
- [ ] Create push notification proxy (adapt from iOS)
  - [ ] Encrypted payload support
  - [ ] Privacy-preserving architecture
  - [ ] Deploy to server
- [ ] Implement notification handling
  - [ ] Decrypt notification payload
  - [ ] Route to correct screen
  - [ ] Switch to correct account
- [ ] Add notification actions (reply, favorite)
- [ ] Implement notification grouping by type

### 5.2 In-App Notifications
- [ ] Create feature-notifications module
- [ ] Fetch notifications from API
- [ ] Implement notification grouping/stacking
- [ ] Build notification list UI
- [ ] Add notification type filters
- [ ] Implement mark as read
- [ ] Add notification badge counter

### 5.3 Validation
- [ ] Push notifications arrive on device
- [ ] Notifications encrypted end-to-end
- [ ] Can tap notification to open app
- [ ] Correct account selected on tap
- [ ] In-app notification list works
- [ ] Can filter by notification type
- [ ] Grouped notifications display correctly

---

## 🌟 Phase 6: Additional Features (Month 8)

### 6.1 Profile Management
- [ ] Create feature-account module (distinct from feature-auth)
- [ ] Build profile screen
  - [ ] Header image and avatar
  - [ ] Bio with HTML rendering
  - [ ] Fields display
  - [ ] Stats (posts, following, followers)
  - [ ] Posts/Replies/Media tabs
- [ ] Implement profile editing
  - [ ] Avatar/header upload
  - [ ] Bio editor
  - [ ] Fields editor
  - [ ] Privacy settings
- [ ] Add follow/unfollow actions
- [ ] Add block/mute actions
- [ ] Add follow list views

### 6.2 Direct Messages
- [ ] Create feature-conversations module
- [ ] Fetch conversations from API
- [ ] Build conversation list UI
- [ ] Build chat-style conversation UI
- [ ] Add inline message composer
- [ ] Support media in DMs

### 6.3 Search & Explore
- [ ] Create feature-explore module
- [ ] Build search interface
  - [ ] Search bar
  - [ ] Filters (accounts, posts, hashtags)
  - [ ] Recent searches
- [ ] Build trending content screens
  - [ ] Trending posts
  - [ ] Trending tags (with graphs)
  - [ ] Trending links
  - [ ] Trending accounts
- [ ] Add suggestions

### 6.4 Lists
- [ ] Create feature-lists module
- [ ] Fetch user lists from API
- [ ] Build list management UI (create, edit, delete)
- [ ] Add accounts to list UI
- [ ] Display list timeline

### 6.5 Validation
- [ ] Can view any profile
- [ ] Can edit own profile
- [ ] Can follow/unfollow accounts
- [ ] DMs send and receive
- [ ] Search works for all types
- [ ] Trending content displays
- [ ] Can create and manage lists

---

## 💎 Phase 7: Polish & Platform Integration (Month 9)

### 7.1 Media Handling
- [ ] Create feature-media module
- [ ] Build image viewer
  - [ ] Zoom and pan gestures
  - [ ] Swipe between images
  - [ ] Share action
  - [ ] Save to device
- [ ] Build video player
  - [ ] ExoPlayer integration
  - [ ] Playback controls
  - [ ] Picture-in-Picture mode
- [ ] Add GIF support (animated)

### 7.2 Android-Specific Features
- [ ] Implement Material You dynamic colors
- [ ] Create adaptive app icon
- [ ] Build home screen widgets
  - [ ] Timeline widget
  - [ ] Compose widget (quick post)
- [ ] Implement share sheet receiving
- [ ] Add app shortcuts (compose, notifications)
- [ ] Optimize for split-screen/multi-window
- [ ] Create tablet-optimized layouts
- [ ] Test on foldable devices

### 7.3 Settings
- [ ] Create settings screen
- [ ] Appearance settings
  - [ ] Theme selection (light/dark/auto)
  - [ ] Dynamic colors toggle
  - [ ] Font size
  - [ ] Display density
- [ ] Behavior settings
  - [ ] Swipe gestures configuration
  - [ ] Tab order customization
  - [ ] Auto-play videos toggle
  - [ ] Data saver mode
- [ ] Privacy settings
- [ ] Notification preferences
- [ ] Build about screen

### 7.4 Validation
- [ ] Images zoom smoothly
- [ ] Videos play without stuttering
- [ ] PiP mode works
- [ ] Widgets update correctly
- [ ] Share sheet integration works
- [ ] App looks good on tablets
- [ ] Material You colors apply
- [ ] All settings persist

---

## ⚡ Phase 8: Performance & Quality (Months 10-11)

### 8.1 Performance Optimization
- [ ] Profile app with Android Profiler
- [ ] Optimize image loading
  - [ ] Proper image sizing
  - [ ] Memory cache tuning
  - [ ] Disk cache configuration
- [ ] Optimize timeline scrolling
  - [ ] Compose recomposition analysis
  - [ ] Key stability checks
  - [ ] Remove unnecessary recompositions
- [ ] Reduce startup time
  - [ ] Lazy initialization
  - [ ] App startup tracing
  - [ ] Baseline profiles
- [ ] Optimize battery usage
  - [ ] WorkManager for background tasks
  - [ ] Doze mode compliance
  - [ ] Wake lock audit
- [ ] Reduce app size
  - [ ] Enable R8 shrinking
  - [ ] Remove unused resources
  - [ ] Optimize images

### 8.2 Testing
- [ ] Write unit tests for repositories (>70% coverage)
- [ ] Write unit tests for ViewModels
- [ ] Write unit tests for use cases
- [ ] Write integration tests (MockWebServer)
- [ ] Write database migration tests
- [ ] Write Compose UI tests
  - [ ] Login flow
  - [ ] Post composition
  - [ ] Timeline scrolling
  - [ ] Navigation
- [ ] Run screenshot tests
- [ ] Test accessibility with TalkBack
  - [ ] Content descriptions
  - [ ] Touch target sizes
  - [ ] Navigation order

### 8.3 Quality Assurance
- [ ] Set up crash reporting (Firebase Crashlytics or Sentry)
- [ ] Add analytics (Firebase or Posthog)
- [ ] Create beta testing group
- [ ] Run beta test for 1 month
- [ ] Collect and triage bug reports
- [ ] Fix critical bugs
- [ ] Fix high-priority bugs
- [ ] Performance profiling on low-end devices

### 8.4 Validation
- [ ] Startup time <2s on mid-range device
- [ ] Timeline scrolls at 60fps
- [ ] App size <50MB
- [ ] Crash-free rate >99.5%
- [ ] Test coverage >70%
- [ ] All accessibility checks pass
- [ ] Beta testers report positive experience

---

## 🚀 Phase 9: Launch Preparation (Month 12)

### 9.1 Store Presence
- [ ] Create Google Play Console account
- [ ] Set up app in console
- [ ] Create app listing
  - [ ] Write app description
  - [ ] Design feature graphic
  - [ ] Take phone screenshots (6+)
  - [ ] Take tablet screenshots (2+)
  - [ ] Create promo video (optional)
- [ ] Write privacy policy
- [ ] Complete content rating questionnaire
- [ ] Set up app signing
- [ ] Configure release track

### 9.2 Documentation
- [ ] Write user documentation
  - [ ] Getting started guide
  - [ ] Feature overview
  - [ ] FAQ
- [ ] Write developer documentation
  - [ ] Architecture overview
  - [ ] Build instructions
  - [ ] Contributing guidelines
  - [ ] Code style guide
- [ ] Generate API documentation (KDoc)
- [ ] Create changelog

### 9.3 Marketing
- [ ] Create landing page
- [ ] Write blog post announcement
- [ ] Prepare social media posts
- [ ] Create press kit
- [ ] Reach out to Android blogs/sites
- [ ] Post on Mastodon (of course!)

### 9.4 Compliance
- [ ] Review privacy policy
- [ ] Ensure GDPR compliance
- [ ] Add data deletion request flow
- [ ] Review terms of service
- [ ] Add in-app legal links

### 9.5 Launch
- [ ] Submit to Google Play (internal track)
- [ ] Test internal release
- [ ] Promote to closed beta
- [ ] Promote to open beta
- [ ] Monitor crash reports and reviews
- [ ] Fix any critical issues
- [ ] Promote to production
- [ ] Phased rollout (10% → 50% → 100%)
- [ ] Announce launch publicly

### 9.6 Validation
- [ ] App approved by Google Play
- [ ] Zero critical bugs reported
- [ ] Store listing looks professional
- [ ] Documentation complete
- [ ] App live on Play Store
- [ ] Launch announcement published

---

## Post-Launch Checklist (Ongoing)

### Week 1
- [ ] Monitor crash reports daily
- [ ] Respond to user reviews
- [ ] Fix critical bugs immediately
- [ ] Prepare hotfix release if needed

### Month 1
- [ ] Analyze user feedback
- [ ] Review analytics data
- [ ] Plan version 1.1 features
- [ ] Address common complaints

### Month 3
- [ ] Release version 1.1
- [ ] Celebrate 10K+ downloads 🎉
- [ ] Evaluate success metrics
- [ ] Plan major features for 2.0

---

## Success Criteria

Mark complete when ALL of these are true:

- [ ] App available on Google Play Store
- [ ] Feature parity with iOS version (core features)
- [ ] Crash-free rate >99.5%
- [ ] Average rating >4.0 stars
- [ ] 1,000+ downloads
- [ ] Performance targets met
- [ ] Test coverage >70%
- [ ] Documentation complete
- [ ] Zero known critical bugs

---

**Total Estimated Time:** 12 months (with team) or 18-24 months (solo)

**Current Phase:** Not started ⏸️

**Progress:** 0 / 9 phases complete

---

*This checklist corresponds to the detailed plan in ANDROID_PORT_PLAN.md*
