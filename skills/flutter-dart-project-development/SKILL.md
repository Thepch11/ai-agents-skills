---
name: flutter-dart-project-development
description: Guides development of Flutter applications using Provider-based state management with best practices for architecture, testing, and modern Flutter patterns. Use this skill when working on Flutter/Dart projects that need state management guidance, code scaffolding, refactoring assistance, or architectural recommendations.
license: Complete terms in LICENSE.txt
---

# Flutter/Dart Project Development with Provider

## Overview

This skill provides comprehensive guidance for Flutter project development with a focus on Provider-based state management, architectural best practices, and modern Flutter patterns. It helps you implement clean, maintainable, and performant Flutter applications with consistent state management approaches.

**Keywords**: Flutter, Dart, Provider, ChangeNotifier, state management, widget testing, clean architecture, performance optimization, mobile development

## Core Principles

### Provider-First State Management

Always use Provider pattern for app-wide and feature-level state management:
- **Use ChangeNotifier with ChangeNotifierProvider** for state management
- Never mix state management styles - maintain consistency throughout the project
- Keep Providers focused on specific domains (User, Settings, Cart, etc.)

### State Management Guidelines

**When to Use Provider:**
- App-wide state (user authentication, theme, settings)
- Feature-level state shared across multiple widgets
- Data that needs to persist across navigation
- State that requires notification to multiple listeners

**When NOT to Use Provider:**
- Ephemeral state like `TextEditingController`
- Simple animations and transitions
- Local widget state (form inputs, switches, etc.)
- **For ephemeral state, use StatefulWidget instead**

### Context Usage Patterns

Follow these architectural guardrails for accessing Providers:

**For Actions (Callbacks):**
```dart
context.read<T>()
```
Use `read` when you need to trigger actions without causing rebuilds (e.g., in onPressed callbacks).

**For UI Updates:**
```dart
context.watch<T>()  // Simple watching
Consumer<T>         // When you need child optimization
Selector<T, R>      // For selective rebuilds based on specific properties
```

## High-Velocity Scaffolding

Use these targeted prompts with GitHub Copilot Chat (@workspace) to generate boilerplate instantly:

### Creating Provider Models

**Basic Provider:**
```
Create a UserProvider class extending ChangeNotifier with a private _user object, 
a getter, and a updateProfile method that calls notifyListeners()
```

**Provider with Multiple Properties:**
```
Create a SettingsProvider with theme mode, language preference, and notification 
settings. Include methods to update each property and persist to SharedPreferences
```

### Multi-Provider Setup

**Application Bootstrap:**
```
Generate a MultiProvider wrapper for main.dart including UserProvider, 
SettingsProvider, and CartProvider
```

Example structure:
```dart
MultiProvider(
  providers: [
    ChangeNotifierProvider(create: (_) => UserProvider()),
    ChangeNotifierProvider(create: (_) => SettingsProvider()),
    ChangeNotifierProvider(create: (_) => CartProvider()),
  ],
  child: MyApp(),
)
```

### Selective Rebuilds

**Performance Optimization:**
```
Convert this widget to use Selector instead of Consumer to only rebuild when 
the isLoggedIn property of AuthProvider changes
```

Example pattern:
```dart
Selector<AuthProvider, bool>(
  selector: (context, auth) => auth.isLoggedIn,
  builder: (context, isLoggedIn, child) {
    // Widget only rebuilds when isLoggedIn changes
  },
)
```

## Agentic Refactoring with Provider

### Multi-File State Migrations

Use GitHub Copilot Agent for complex refactoring tasks:

**Refactoring to Provider:**
```
Refactor the current ProfileScreen and its children to pull data from a new 
ProfileProvider instead of passing parameters through constructors
```

**Performance Analysis:**
```
Analyze my Build methods and suggest where to use context.select to improve 
performance by reducing unnecessary rebuilds
```

### Common Refactoring Patterns

1. **Constructor Parameters → Provider:**
   - Identify widgets receiving data through constructors
   - Create appropriate Provider class
   - Update widgets to use `context.watch` or `Consumer`
   - Remove constructor parameters

2. **Callback Props → Provider Actions:**
   - Move callbacks from props to Provider methods
   - Use `context.read` for triggering actions
   - Simplify widget trees by removing callback chains

3. **setState → ChangeNotifier:**
   - Convert StatefulWidget to StatelessWidget where appropriate
   - Move state to ChangeNotifier Provider
   - Replace setState calls with notifyListeners

## Code Explanation Guidelines

When explaining Flutter/Dart code, follow these principles to provide clear, educational insights:

### Explain Provider Patterns First

**State Flow Analysis:**
```
When explaining code using Providers, always:
1. Identify which Provider(s) are being accessed
2. Explain the state flow: Provider → Consumer/Selector → UI
3. Clarify whether context.read, context.watch, or Consumer is used and why
4. Highlight what triggers rebuilds vs. what just reads state
```

**Example Explanation Format:**
- "This widget uses `context.watch<CartProvider>()` which means it will rebuild whenever any property in CartProvider changes"
- "The `context.read<AuthProvider>()` in the onPressed callback won't trigger rebuilds - it just calls the login method"
- "This Selector watches only the `itemCount` property, so the widget rebuilds only when that specific value changes, not on every cart update"

### Widget Lifecycle Context

**Explain Stateful vs. Stateless:**
```
When explaining widgets:
- For StatelessWidget: Emphasize that it's immutable and rebuilds are controlled by parent or Provider
- For StatefulWidget: Explain initState, dispose, and setState usage
- Always clarify why the widget type was chosen (ephemeral state vs. shared state)
```

**Build Method Analysis:**
- Point out expensive operations in build() that might cause performance issues
- Highlight const constructors and their performance benefits
- Explain widget tree composition and how children are passed

### Async Operations in Flutter

**Future and Stream Patterns:**
```
When explaining asynchronous code:
1. Identify FutureBuilder or StreamBuilder usage
2. Explain the three states: waiting, data, error
3. Show how Provider methods handle async (returning Future/Stream)
4. Highlight proper error handling patterns
```

**Example:**
```dart
// Explain this pattern clearly
FutureBuilder<User>(
  future: context.read<UserProvider>().fetchUser(),
  builder: (context, snapshot) {
    if (snapshot.connectionState == ConnectionState.waiting) {
      return CircularProgressIndicator();
    }
    if (snapshot.hasError) {
      return ErrorWidget(snapshot.error);
    }
    return UserProfile(user: snapshot.data);
  },
)
```

**Explanation Points:**
- "FutureBuilder handles three states: loading (waiting), success (hasData), and error (hasError)"
- "Using context.read here is correct because we're triggering an action, not watching for changes"
- "The builder is called multiple times as the Future progresses through its states"

### Key Flutter/Dart Concepts

**1. Immutability and final:**
```
Always explain:
- Why fields are marked final in widgets
- The difference between final and const
- How immutability relates to Flutter's rebuild mechanism
```

**2. Named Parameters and Required:**
```
Highlight:
- When parameters use 'required' keyword and why
- Optional named parameters with defaults
- The super.key pattern in constructors
```

**3. Extension Methods:**
```dart
// Explain these common patterns
context.watch<T>()  // Extension on BuildContext
MediaQuery.of(context).size  // Static method pattern
Theme.of(context)  // InheritedWidget access pattern
```

### Performance Implications

**Always Mention:**
- Widget rebuild frequency and optimization opportunities
- Const constructors for static widgets
- When to extract widgets into separate classes
- Memory implications of Provider disposal

**Example Explanation:**
"This Consumer rebuilds only the Text widget when the counter changes, while the expensive Image widget is passed as `child` and doesn't rebuild. This is a performance optimization."

### Testing Context

**When explaining test code:**
```
1. Show how Providers are mocked or provided in tests
2. Explain pumpWidget vs. pump vs. pumpAndSettle
3. Clarify finder patterns (find.text, find.byType, find.byKey)
4. Highlight async expectations with expect + matcher
```

### Code Smell Recognition

**Point out anti-patterns:**
- "⚠️ This uses context.watch inside a callback, which will cause unnecessary rebuilds. Should use context.read instead."
- "⚠️ This Provider is managing ephemeral state like TextEditingController - consider moving to StatefulWidget."
- "⚠️ This widget has business logic in the build method - should be extracted to the Provider."
- "⚠️ Missing dispose() for this Provider's resources (streams, controllers, listeners)."

### Incremental Explanation Strategy

**For complex code:**
1. **High-level overview** - "This screen manages user profile updates using ProfileProvider"
2. **Widget tree structure** - "The main Scaffold contains a Form with TextFields and a submit button"
3. **State management flow** - "When Submit is pressed, context.read<ProfileProvider>().updateProfile() is called"
4. **Data flow** - "The Provider validates input, calls the API, updates state, and notifies listeners"
5. **UI response** - "Consumer<ProfileProvider> rebuilds to show success message or errors"

### Domain-Specific Vocabulary

**Use correct Flutter terminology:**
- "Hot reload" vs. "hot restart"
- "Widget tree" vs. "element tree" vs. "render tree"
- "BuildContext" is the widget's location in the tree
- "Scaffold" provides Material Design visual layout structure
- "Navigator" manages route stack for navigation
- "MediaQuery" provides device/display information
- "Theme" provides app-wide styling configuration

### Interactive Explanation Prompts

**Encourage deeper understanding:**
- "Would you like me to explain why Provider was chosen over setState here?"
- "I can show you how to optimize this widget's rebuilds using Selector - interested?"
- "This pattern might be unfamiliar - should I break down how ChangeNotifier works?"

## Integration with Modern Flutter Patterns

### Clean Architecture

Organize your Flutter project with clear separation of concerns:

**Directory Structure:**
```
lib/
├── domain/              # Business logic (pure Dart)
│   ├── models/
│   ├── repositories/
│   └── use_cases/
├── presentation/        # UI layer
│   ├── providers/       # State management
│   ├── screens/
│   └── widgets/
└── data/               # Data sources
    ├── api/
    ├── local/
    └── repositories/
```

**Key Principles:**
- Keep Providers in `presentation/providers/` directory
- Separate pure business logic in `domain/` layer
- Providers should orchestrate use cases, not contain business logic
- Use dependency injection for repositories in Providers

### Widget Testing with Mocked Providers

**Test Setup Pattern:**
```
Generate a widget test for LoginScreen that mocks AuthProvider using the 
mocktail package
```

Example test structure:
```dart
testWidgets('LoginScreen shows error on failed login', (tester) async {
  final mockAuthProvider = MockAuthProvider();
  
  when(() => mockAuthProvider.login(any(), any()))
      .thenAnswer((_) async => false);
  
  await tester.pumpWidget(
    ChangeNotifierProvider<AuthProvider>.value(
      value: mockAuthProvider,
      child: MaterialApp(home: LoginScreen()),
    ),
  );
  
  // Test assertions
});
```

### Performance Optimization Strategies

**1. Use Selector for Precise Rebuilds:**
```dart
Selector<CartProvider, int>(
  selector: (context, cart) => cart.itemCount,
  builder: (context, count, child) => Text('$count items'),
)
```

**2. Use context.select for Single Values:**
```dart
final username = context.select<UserProvider, String>(
  (user) => user.name,
);
```

**3. Provide child to Consumer:**
```dart
Consumer<ThemeProvider>(
  child: ExpensiveWidget(), // Won't rebuild
  builder: (context, theme, child) {
    return ThemedContainer(
      theme: theme,
      child: child,
    );
  },
)
```

## Configuration Files

### Copilot Instructions (.github/copilot-instructions.md)

Create this file to enforce Provider-first mindset:

```markdown
# Flutter Project - State Management Guidelines

## State Management Rules

1. **Always use ChangeNotifier with ChangeNotifierProvider** for state management
2. **Use context.read<T>()** for actions (callbacks)
3. **Use context.watch<T>() or Consumer<T>** for UI updates
4. **Do not use Providers for ephemeral state** like TextEditingController or simple animations
5. **Use StatefulWidget for ephemeral state** management

## Architecture Guidelines

- Place Providers in `presentation/providers/` directory
- Keep business logic in `domain/` layer, separate from Providers
- Use dependency injection for repositories
- Follow clean architecture principles

## Testing Standards

- Mock Providers using mocktail package
- Test Provider logic separately from UI
- Use widget tests with mocked Providers for screens
```

## Best Practices Summary

1. **Consistency:** Use Provider pattern throughout the project
2. **Separation:** Keep Provider state management separate from ephemeral state
3. **Performance:** Use Selector and context.select for optimized rebuilds
4. **Architecture:** Follow clean architecture with clear layer separation
5. **Testing:** Mock Providers for reliable widget and integration tests
6. **Documentation:** Maintain clear Provider responsibilities and contracts

## Common Pitfalls to Avoid

- ❌ Mixing Provider with other state management solutions
- ❌ Using Provider for ephemeral/local state
- ❌ Using context.watch in callbacks (causes unnecessary rebuilds)
- ❌ Creating overly broad Providers (prefer focused, single-responsibility Providers)
- ❌ Putting business logic directly in Providers (use use cases/repositories)
- ❌ Forgetting to dispose resources in Provider's dispose method
- ❌ Not using Selector/context.select for performance-critical widgets

## Additional Resources

- Provider package: https://pub.dev/packages/provider
- Flutter architecture samples: https://github.com/brianegan/flutter_architecture_samples
- Clean architecture guide: Focus on separation between domain, data, and presentation layers
