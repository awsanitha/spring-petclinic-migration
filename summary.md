# Java 17 Migration Summary

## Status: BUILD SUCCESS ✅

The Spring PetClinic application has been successfully built after the OpenRewrite Java 17 upgrade.

## Build Results

- **Spring Boot version:** 3.5.16
- **Java version:** 17 (javac release 17)
- **Tests run:** 42, Failures: 0, Errors: 0, Skipped: 1

## What OpenRewrite Did

The OpenRewrite `UpgradeSpringBoot_3_5` recipe performed the following transformations:
- Upgraded Spring Boot from 2.1.18.RELEASE → 3.5.16
- Migrated `javax.*` namespaces to `jakarta.*` (JEE 10)
- Migrated JUnit 4 → JUnit 5 across all test files
- Removed unnecessary `@RunWith` / `@ExtendWith` Spring Boot test annotations
- Updated test annotations (`@Before` → `@BeforeEach`, `@Test` etc.)
- Migrated Mockito to JUnit 5 extension style

## Fixes Applied in This Cycle

### 1. Replaced deprecated `git-commit-id-plugin`

**Problem:** `pl.project13.maven:git-commit-id-plugin` is no longer managed by Spring Boot 3.x's BOM, producing a build warning about a missing version.

**Fix:** Replaced with `io.github.git-commit-id:git-commit-id-maven-plugin` which is the maintained successor and is version-managed by Spring Boot 3.x.

```xml
<!-- Before -->
<groupId>pl.project13.maven</groupId>
<artifactId>git-commit-id-plugin</artifactId>

<!-- After -->
<groupId>io.github.git-commit-id</groupId>
<artifactId>git-commit-id-maven-plugin</artifactId>
```

## Observations / Non-Blocking Notes

### Thymeleaf deprecated fragment syntax
The templates in `src/main/resources/templates/fragments/layout.html` use deprecated unwrapped fragment expression syntax (e.g., `::menuItem (...)` instead of `~{::menuItem (...)}`). These generate warnings during test execution but do not break functionality. Should be updated in a future cycle.

### 1 skipped test
`CrashControllerTests` has 1 test marked `@Disabled` (migrated from `@Ignore` by OpenRewrite). This is expected behaviour from the migration.

### javax.cache dependency
The `pom.xml` still has `javax.cache:cache-api` as a dependency alongside Spring Boot 3.5 / Jakarta. The `javax.cache` API is not part of Jakarta EE (it remains under `javax.cache` namespace), so this is correct and intentional — JCache (JSR-107) kept its `javax.cache` package.

## Next Steps

- Fix deprecated Thymeleaf fragment expressions in `src/main/resources/templates/fragments/layout.html`
- Review the `@Disabled` test in `CrashControllerTests` and re-enable if appropriate
- Consider adding `spring.jpa.open-in-view=false` to `application.properties` to suppress the open-in-view warning
