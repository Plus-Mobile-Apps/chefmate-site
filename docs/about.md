# About

Chef Mate+ is an open source app that all source code is available on [Github](https://github.com/Plus-Mobile-Apps/chef-mate)

## Libraries Used

### Shared

* [Coroutines](https://github.com/Kotlin/kotlinx.coroutines) - asynchronous programming framework
* [kotlinx.serialization](https://github.com/Kotlin/kotlinx.serialization) - multiplatform serialization library
* [kotlinx-datetime](https://github.com/Kotlin/kotlinx-datetime) - multiplatform date time library
* [ktfmt](https://github.com/facebook/ktfmt) - kotlin code formatting and linter

### Client

* [Compose Multiplatform](https://github.com/JetBrains/compose-multiplatform/#compose-multiplatform) - shared UI for client
* [SqlDelight](https://sqldelight.github.io/sqldelight/2.1.0/) - sqlite database
* [Essenty](https://github.com/arkivanov/Essenty) - lifecycle, instance keeper, back handler, state keeper
* [Decompose](https://github.com/arkivanov/Decompose) - navigation
* [metro](https://zacsweers.github.io/metro/) - dependency injection
* [kermit](https://github.com/touchlab/Kermit) - logging
* [Bugsnag](https://github.com/bugsnag/bugsnag-kotlin-multiplatform) - error logging
* [multiplatform-settings](https://github.com/russhwolf/multiplatform-settings) - key value storage

### Server

* [Supabase](https://supabase.com/)

### Testing

* [Kotlin test](https://kotlinlang.org/api/core/kotlin-test/) - KMP testing framework
* [Kotest](https://kotest.io/docs/assertions/assertions.html) - assertions
* [Mokkery](https://mokkery.dev/) - mocking library
* [Turbine](https://github.com/cashapp/turbine) - coroutines testing library
* [Compose Preview Screenshot Testing](https://developer.android.com/studio/preview/compose-screenshot-testing) - snapshot tests for `@Preview` composables (see `client/ui/screenshot-test/` and `CLAUDE.md` for the workflow)