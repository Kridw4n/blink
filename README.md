# BLINK

BLINK is an Android social networking application for discovering nearby users, joining interest-based communities, publishing posts, and communicating through direct and group chats.

## Features

- Discover popular communities and nearby users
- Create, browse, and join communities
- Publish posts with optional images
- Like, dislike, and comment on community posts
- Send direct and group messages
- Receive in-app notifications
- Maintain a personal profile and location-based presence

## Screenshots

| Home discovery | Community browser | Community feed |
| --- | --- | --- |
| <img src="docs/screenshots/01-home-discovery.png" width="240" alt="BLINK home discovery screen"> | <img src="docs/screenshots/02-community-browser.png" width="240" alt="BLINK community browser"> | <img src="docs/screenshots/03-community-feed.png" width="240" alt="BLINK community feed"> |

| Post engagement | Direct messaging | Profile |
| --- | --- | --- |
| <img src="docs/screenshots/04-post-engagement.png" width="240" alt="BLINK post comments and reactions"> | <img src="docs/screenshots/05-direct-messaging.png" width="240" alt="BLINK direct messaging"> | <img src="docs/screenshots/08-profile-page.png" width="240" alt="BLINK profile page"> |

| Create a community | Create a post | Location-enabled profile |
| --- | --- | --- |
| <img src="docs/screenshots/06-create-community.png" width="240" alt="BLINK community creation form"> | <img src="docs/screenshots/07-create-post.png" width="240" alt="BLINK post creation form"> | <img src="docs/screenshots/09-location-enabled-profile.png" width="240" alt="BLINK location-enabled profile"> |

The location shown in the final screenshot is the Android emulator's default simulated location.

## Technology

- Java 11
- Android SDK 35 (minimum SDK 24)
- Firebase Authentication, Cloud Firestore, Storage, Messaging, and Analytics
- Material Design components
- Glide for image loading
- Google Play Services Location
- MVVM-oriented view models and repository-style managers

## Architecture and code organisation

BLINK uses an MVVM-oriented, feature-layered structure. It is not a strict implementation of MVVM throughout the entire application, but it separates user-interface code, application state, data operations, and domain models so that individual features are easier to understand and maintain.

- **Activities and fragments** manage screens, navigation, user input, and Android lifecycle events. Reusable base fragments centralise behaviour shared by the main navigation destinations.
- **ViewModels** use Android `LiveData` to retain and expose UI-facing state, such as post lists and chat availability, without tying that state directly to a view instance.
- **Managers** encapsulate feature operations for authentication, users, posts, comments, communities, chats, messages, and notifications. This keeps most Firebase interactions and business operations out of adapters and screen controllers.
- **Models** represent application data such as users, communities, posts, messages, notifications, and geographic points.
- **Adapters** translate model collections into RecyclerView and ViewPager interfaces for feeds, chats, communities, notifications, and nearby users.
- **Services** handle background or platform-facing responsibilities, including location updates and Firebase Cloud Messaging notifications.
- **Utilities and interfaces** centralise shared Firebase references, Android helpers, callbacks, and contracts between components.

The result is a modular structure in which a screen can delegate a data operation to a manager, observe state through a ViewModel where appropriate, and pass the resulting models to an adapter for presentation.

## Nearby-user discovery with a quadtree

BLINK implements a quadtree-based spatial index for the **Similar People Nearby** feature. The implementation uses four small model classes—`Quadrant`, `GeoRect`, `Point`, and `QuadTree`—and connects them to the home screen in `HomeFragment`.

1. A user's latitude and longitude are converted into a stable geographic tile identifier by `Quadrant`.
2. Users belonging to the current user's tile are represented as longitude/latitude `Point` objects and inserted into a `QuadTree` with a node capacity of four.
3. When a node reaches capacity, the tree recursively divides its geographic bounds into northeast, northwest, southeast, and southwest child nodes.
4. The app creates a small rectangular search area around the current location and queries the tree.
5. Branches whose bounds do not intersect the search area are skipped; matching user IDs are mapped back to `User` objects and displayed in the nearby-user carousel.

This demonstrates spatial partitioning and range-query pruning: after users are loaded and filtered to the relevant geographic tile, the app does not need to test every in-memory point during each nearby-area query. The current search is an approximate latitude/longitude rectangle rather than a road-distance or exact circular-distance calculation.

## Getting started

1. Clone the repository.
2. Open the project in Android Studio.
3. Configure an Android emulator or connect a physical Android device.
4. Ensure the required Firebase configuration is available at `app/google-services.json`.
5. Build and run the `app` configuration.

From the command line on Windows:

```powershell
.\gradlew.bat :app:assembleDebug
```

The debug APK is generated under `app/build/outputs/apk/debug/`.

## Project structure

```text
app/src/main/java/com/example/infosys/
|-- activities/   # Full-screen application flows
|-- fragments/    # Main navigation destinations
|-- adapters/     # RecyclerView and ViewPager adapters
|-- managers/     # Authentication, chat, and data operations
|-- model/        # Application data models
|-- services/     # Location and notification services
|-- utils/        # Shared Firebase and Android helpers
`-- viewmodels/   # UI-facing state management
```
