# Music Assistant Mobile App

**Repo:** `music-assistant/mobile-app` | **Base branch:** `main` | **Kotlin Multiplatform + Compose**

---

## Research Hints

- Read `.claude/project.md` (imported via `@import`) — it has extensive sub-docs for architecture, dependencies, guidelines, SendSpin protocol, settings screen, CarPlay, WebRTC, volume control, and iOS audio pipeline.
- Check if the change is in `commonMain` (shared) or platform-specific (`androidMain`/`iosMain`).
- Explain the KMP shared/platform split and where the change fits.

---

## Coding Standards

**Framework & tooling:**
- Kotlin Multiplatform (KMP) + Compose Multiplatform
- Platforms: Android and iOS from a shared codebase
- Build: Gradle with Kotlin DSL, version catalog (`libs.plugins.*`)
- Architecture: MVVM + Unidirectional Data Flow
- Has detailed agent docs at `.claude/project.md` — **always read this first**

**Project structure:**
- `composeApp/` — shared KMP module (`commonMain`, `androidMain`, `iosMain`)
- `androidApp/` — Android-specific app module
- `iosApp/` — iOS-specific Xcode project

**Kotlin coding standards:**
- Use Kotlin idioms: safe calls (`?.`), elvis (`?:`), `let`/`also`/`apply` — NEVER Java-style null checks
- **NEVER use `!!`** (non-null assertion) in live code
- Prefer `when` over `if-else` chains
- Material3 components only
- `StateFlow` over `LiveData` — ViewModels expose `StateFlow`, UI collects with `collectAsStateWithLifecycle()`
- Sealed interfaces for navigation and polymorphism
- Minimize `expect/actual` — keep code in `commonMain` whenever possible
- One primary composable per file, named after the main composable
- No previews unless explicitly requested
- Logging with `Logger.withTag("Component")`

**Key dependencies:**
- Ktor (HTTP + WebSocket networking)
- Koin (dependency injection)
- Navigation3 (type-safe nav + Material3 adaptive)
- Coil 3 (image loading)
- ExoPlayer/Media3 (Android audio)
- kotlinx.serialization (JSON)
- WebRTC KMP (remote access)
- SendSpin protocol (multi-room audio)

**Note:** The app is transitioning from old `MainScreen`/`MainViewModel` to new `HomeScreen`/`HomeScreenViewModel` architecture.

---

## Lint & Test

```bash
./gradlew :composeApp:testAndroidHostTest   # Shared KMP tests (run on JVM)
./gradlew :androidApp:testDebug              # Android-specific tests
./gradlew :androidApp:assembleDebug          # Debug APK
./gradlew :androidApp:assembleSelfSignedRelease  # Performance testing APK
# iOS: open iosApp/iosApp.xcodeproj in Xcode
```

---

## PR Requirements

Target: `main`

- Description and rationale
- Platforms affected (Android, iOS, or both via commonMain)
- Link to related issue
- Screenshots/recordings for UI changes
