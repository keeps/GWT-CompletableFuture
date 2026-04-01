# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

GWT 2.8.0-compatible implementation of Java 8's `java.util.concurrent.CompletableFuture` and related concurrency classes. Enables `CompletableFuture` and Java 8 lambda support in GWT applications by providing super-source emulation. Based on Google's GWT commit 8b17515.

Published to GitHub Packages as `com.ekotrope:gwt-completablefuture`.

## Build Commands

```bash
mvn clean install    # Build and install locally
mvn package          # Package JAR only
mvn deploy           # Deploy to GitHub Packages (requires auth)
```

There are no tests in this repository. Testing is done by consuming GWT projects that inherit this module.

## Architecture

This is a GWT super-source library — the source code is shipped inside the JAR and compiled by GWT's compiler into JavaScript, not by `javac`.

**Key configuration:**
- `src/main/super` — GWT super-source root; overrides `java.util.concurrent` classes in the JRE emulation
- `src/main/java/com/ekotrope/gwt/emul/` — The actual emulation sources placed under a path structure that mirrors `java.util.concurrent`
- `CompletableFuture.gwt.xml` — GWT module descriptor; declares the super-source path and module inheritance

**Layer structure:**

| Layer | Package | Purpose |
|-------|---------|---------|
| Public API | `java.util.concurrent` | Standard Java concurrent interfaces and classes (`CompletableFuture`, `CompletionStage`, `Future`, `Executor`, `Executors`, etc.) |
| Internal impl | `java.util.concurrent.impl` | Promise-based async internals (`Promise`, `PromiseImpl`, `NativePromiseImpl`, `DeferredExecutor`) |
| JS bridge | `java.util.concurrent.impl` | `JsPromise` bridges to browser-native JavaScript Promises via `NativePromiseImpl` |

**Design:** GWT is single-threaded, so `CompletableFuture` is implemented on top of browser Promises rather than Java threads. `DeferredExecutor` schedules work using deferred execution. `NativePromiseImpl` wraps native browser Promise objects for optimal performance.

**GWT module inheritance** (in consuming projects):
```xml
<inherits name='com.ekotrope.gwt.CompletableFuture'/>
```

**Maven dependency:**
```xml
<dependency>
  <groupId>com.ekotrope</groupId>
  <artifactId>gwt-completablefuture</artifactId>
  <version>1.0.1</version>
</dependency>
```
