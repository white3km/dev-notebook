# Maven CLI - Cheatsheet

**Last Updated: 2026-08

## Common Lifecycle Commands

### Clean

Remove previous build output:

```bash
mvn clean
```

Typically removes:

```text
target/
```

### Compile

Compile application source:

```bash
mvn compile
```

### Test

Run tests:

```bash
mvn test
```

### Package

Compile, test, and create the project's package:

```bash
mvn package
```

Depending on the project, this may produce:

```text
target/my-app.jar
```

or:

```text
target/my-app.war
```

### Verify

Run checks needed to verify the package:

```bash
mvn verify
```

### Install

Install the artifact into the local Maven repository:

```bash
mvn install
```

Default local repository:

```text
~/.m2/repository/
```

### Deploy

Deploy the artifact to a configured remote repository:

```bash
mvn deploy
```

---

## Common Build Workflows

### Clean Build

```bash
mvn clean package
```

### Clean Build Without Tests

```bash
mvn clean package -DskipTests
```

### Clean Build and Install Locally

```bash
mvn clean install
```

### Full Verification

```bash
mvn clean verify
```

---

## Skipping Tests

### Skip Running Tests

```bash
mvn package -DskipTests
```

Tests are compiled but not executed.

### Skip Test Compilation and Execution

```bash
mvn package -Dmaven.test.skip=true
```

This is different from `-DskipTests`.

| Option | Compile Tests | Run Tests |
|---|---:|---:|
| `-DskipTests` | Yes | No |
| `-Dmaven.test.skip=true` | No | No |

---

## Running Specific Tests

Run a specific test class:

```bash
mvn test -Dtest=MyTest
```

Run a specific test method:

```bash
mvn test -Dtest=MyTest#testSomething
```

Run multiple test classes:

```bash
mvn test -Dtest=MyTest,OtherTest
```

---

## Maven Version

```bash
mvn --version
```

or:

```bash
mvn -v
```

Shows information such as:

```text
Apache Maven version
Java version
Java home
OS information
```

---

## Maven Help

```bash
# General help
mvn help:help

# Help for a specific plugin
mvn help:describe -Dplugin=compiler

# Detailed plugin information
mvn help:describe \
  -Dplugin=compiler \
  -Ddetail

# Help for a specific goal
mvn help:describe \
  -Dplugin=compiler \
  -Dgoal=compile \
  -Ddetail
```
## Dependency Tree

Show the project's dependency tree:

```bash
mvn dependency:tree
```

Verbose output:

```bash
mvn dependency:tree -Dverbose
```

Filter by group or artifact:

```bash
mvn dependency:tree \
  -Dincludes=org.example:example-library
```

Exclude a dependency:

```bash
mvn dependency:tree \
  -Dexcludes=org.example:example-library
```

---
## Dependency Resolution

### Resolve Dependencies

```bash
mvn dependency:resolve
```

### Analyze Dependencies

```bash
mvn dependency:analyze
```

Useful for finding:

- Used but undeclared dependencies
- Declared but unused dependencies
- Potentially unnecessary dependencies

---

## Local Repository

Maven normally stores downloaded dependencies in:

```text
~/.m2/repository/
```

### Show Local Repository

```bash
mvn help:evaluate \
  -Dexpression=settings.localRepository \
  -q \
  -DforceStdout
```

### Force Dependency Updates

```bash
mvn clean install -U
```

`-U` forces Maven to check for updated snapshots and releases.

---

## Maven Settings

User settings:

```text
~/.m2/settings.xml
```

Global Maven settings:

```text
$MAVEN_HOME/conf/settings.xml
```

Use a specific settings file:

```bash
mvn -s /path/to/settings.xml clean install
```

---

## Profiles

Profiles allow different configurations for different environments or use cases.

Activate a profile:

```bash
mvn clean package -Pproduction
```

Multiple profiles:

```bash
mvn clean package -Pproduction,docker
```

List active profiles:

```bash
mvn help:active-profiles
```

Show effective POM:

```bash
mvn help:effective-pom
```

---

## Properties

Set a Maven property from the command line:

```bash
mvn package -Dproperty=value
```

Example:

```bash
mvn package -DskipTests
```

Java version example:

```bash
mvn package -Dmaven.compiler.release=21
```

Display a property:

```bash
mvn help:evaluate \
  -Dexpression=project.version \
  -q \
  -DforceStdout
```

---

## Build Information

### Effective POM

Show the POM after inheritance, profiles, and defaults are applied:

```bash
mvn help:effective-pom
```

Save to a file:

```bash
mvn help:effective-pom > effective-pom.xml
```

### Effective Settings

```bash
mvn help:effective-settings
```

---
## Plugins

Maven functionality is provided by plugins.

Common plugins include:

| Plugin | Purpose |
|---|---|
| `maven-compiler-plugin` | Compile Java |
| `maven-surefire-plugin` | Unit tests |
| `maven-failsafe-plugin` | Integration tests |
| `maven-jar-plugin` | Build JARs |
| `maven-war-plugin` | Build WARs |
| `maven-clean-plugin` | Clean build output |
| `maven-resources-plugin` | Process resources |
| `maven-install-plugin` | Install artifacts |
| `maven-deploy-plugin` | Deploy artifacts |
| `maven-dependency-plugin` | Dependency operations |
| `maven-source-plugin` | Generate source JARs |
| `maven-javadoc-plugin` | Generate Javadocs |

Run a plugin goal:

```bash
mvn <plugin>:<goal>
```

Example:

```bash
mvn dependency:tree
```

---

## Compiler Plugin

Example configuration:

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.14.1</version>
    <configuration>
        <release>21</release>
    </configuration>
</plugin>
```

Compile:

```bash
mvn compile
```

---

## Surefire / Unit Tests

Run unit tests:

```bash
mvn test
```

Common test output:

```text
target/surefire-reports/
```

Skip tests:

```bash
mvn package -DskipTests
```

---

## Failsafe / Integration Tests

A common lifecycle for integration tests:

```text
integration-test
    ↓
verify
```

Run:

```bash
mvn verify
```

Integration test reports are commonly found under:

```text
target/failsafe-reports/
```

---

## JAR Packaging

Build a JAR:

```bash
mvn package
```

Output:

```text
target/<artifactId>-<version>.jar
```

---

## WAR Packaging

Set packaging:

```xml
<packaging>war</packaging>
```

Build:

```bash
mvn package
```

Output:

```text
target/<artifactId>-<version>.war
```

---

## Run a Java Application

Maven itself does not provide a universal Java application runner, but plugins can.

For example, using the Exec Maven Plugin:

```bash
mvn exec:java \
  -Dexec.mainClass=com.example.Main
```

With arguments:

```bash
mvn exec:java \
  -Dexec.mainClass=com.example.Main \
  -Dexec.args="arg1 arg2"
```

---

## Spring Boot Applications

For Spring Boot projects using the Spring Boot Maven Plugin:

```bash
mvn spring-boot:run
```

Build:

```bash
mvn clean package
```

Run the resulting JAR:

```bash
java -jar target/my-app.jar
```

Skip tests:

```bash
mvn spring-boot:run -DskipTests
```

---

## Multi-Module Projects

Typical structure:

```text
parent/
├── pom.xml
├── module-a/
│   └── pom.xml
├── module-b/
│   └── pom.xml
└── module-c/
    └── pom.xml
```

Build all modules:

```bash
mvn clean install
```

Build a specific module:

```bash
mvn clean install -pl module-a
```

Build a module and its dependencies:

```bash
mvn clean install -pl module-a -am
```

`-pl` means **projects list**.

`-am` means **also make required projects**.

Build dependents too:

```bash
mvn clean install -pl module-a -amd
```

`-amd` means **also make dependents**.

---

## Parallel Builds

Maven can build independent modules in parallel.

```bash
mvn clean install -T 4
```

Use one thread per CPU core:

```bash
mvn clean install -T 1C
```

---

## Offline Mode

Use only locally cached dependencies:

```bash
mvn -o clean package
```

Equivalent:

```bash
mvn --offline clean package
```

Useful when working without network access.

---

## Batch Mode

Useful for CI/CD:

```bash
mvn -B clean verify
```

Batch mode reduces interactive output.

---

## Quiet / Debug Output

### Quiet

```bash
mvn -q clean package
```

### Debug

```bash
mvn -X clean package
```

### Error Details

```bash
mvn -e clean package
```

| Option | Purpose |
|---|---|
| `-q` | Quiet output |
| `-B` | Batch mode |
| `-X` | Debug output |
| `-e` | Show execution error details |
| `-U` | Force dependency update checks |
| `-o` | Offline mode |

---

## Maven Wrapper

The Maven Wrapper allows a project to use a specified Maven version without requiring Maven to be globally installed.

Common wrapper files:

```text
mvnw
mvnw.cmd
.mvn/
```

Linux/macOS:

```bash
./mvnw clean package
```

Windows:

```powershell
.\mvnw.cmd clean package
```

The wrapper is generally preferred in CI/CD and team projects because it provides a consistent Maven version.

---

## Maven Wrapper Commands

```bash
# Build
./mvnw clean package

# Test
./mvnw test

# Install
./mvnw clean install

# Show Maven version
./mvnw --version
```

---

## CI/CD Builds

A common CI build:

```bash
mvn -B clean verify
```

Skip tests when appropriate:

```bash
mvn -B clean package -DskipTests
```

Build with a specific profile:

```bash
mvn -B clean verify -Pproduction
```

Force dependency updates:

```bash
mvn -B clean verify -U
```

---

## Deploying Artifacts

Install locally:

```bash
mvn install
```

Deploy to a configured remote repository:

```bash
mvn deploy
```

Deployment repositories are typically configured through:

```text
pom.xml
settings.xml
```

Example POM configuration:

```xml
<distributionManagement>
    <repository>
        <id>releases</id>
        <url>https://repo.example.com/releases</url>
    </repository>

    <snapshotRepository>
        <id>snapshots</id>
        <url>https://repo.example.com/snapshots</url>
    </snapshotRepository>
</distributionManagement>
```

---

## Versions

Display the current project version:

```bash
mvn help:evaluate \
  -Dexpression=project.version \
  -q \
  -DforceStdout
```

Display the project artifact ID:

```bash
mvn help:evaluate \
  -Dexpression=project.artifactId \
  -q \
  -DforceStdout
```

Display the group ID:

```bash
mvn help:evaluate \
  -Dexpression=project.groupId \
  -q \
  -DforceStdout
```

---

## Common Maven Commands

| Command | Description |
|---|---|
| `mvn clean` | Remove `target/` |
| `mvn validate` | Validate project |
| `mvn compile` | Compile source |
| `mvn test` | Run unit tests |
| `mvn package` | Create JAR/WAR |
| `mvn verify` | Run verification checks |
| `mvn install` | Install artifact locally |
| `mvn deploy` | Deploy artifact remotely |
| `mvn clean package` | Clean and package |
| `mvn clean install` | Clean and install |
| `mvn clean verify` | Clean and verify |
| `mvn dependency:tree` | Show dependency tree |
| `mvn dependency:analyze` | Analyze dependencies |
| `mvn help:effective-pom` | Show effective POM |
| `mvn help:active-profiles` | Show active profiles |
| `mvn -U clean install` | Force dependency updates |
| `mvn -o clean package` | Offline build |
| `mvn -B clean verify` | Batch-mode build |
| `mvn -X clean verify` | Debug build |

---

## Common Options

| Option | Description |
|---|---|
| `-Dname=value` | Set a system/Maven property |
| `-DskipTests` | Skip test execution |
| `-Dmaven.test.skip=true` | Skip test compilation and execution |
| `-P<profile>` | Activate profile |
| `-f <file>` | Use a specific POM |
| `-s <file>` | Use a specific settings file |
| `-pl <module>` | Build selected module(s) |
| `-am` | Also build required modules |
| `-amd` | Also build dependent modules |
| `-T <threads>` | Parallel build |
| `-U` | Update snapshots/releases |
| `-o` | Offline mode |
| `-B` | Batch mode |
| `-q` | Quiet mode |
| `-X` | Debug output |
| `-e` | Show error details |

---

## Useful `-D` Properties

```bash
# Skip tests
mvn package -DskipTests

# Skip test compilation and execution
mvn package -Dmaven.test.skip=true

# Set a profile-specific property
mvn package -Denv=production

# Set Java release
mvn package -Dmaven.compiler.release=21

# Run specific test
mvn test -Dtest=MyTest

# Run specific test method
mvn test -Dtest=MyTest#testSomething
```

---

## Troubleshooting Builds

### Dependency Resolution Problems

```bash
mvn dependency:tree
mvn clean install -U
```

If necessary, inspect:

```text
~/.m2/repository/
```

### See Full Build Details

```bash
mvn clean package -X
```

### Check Effective Configuration

```bash
mvn help:effective-pom
mvn help:effective-settings
mvn help:active-profiles
```

### Check Java and Maven Versions

```bash
java -version
mvn -version
```

### Force a Clean Build

```bash
mvn clean install
```

---

## Common Build Failures

| Error / Symptom | Things to Check |
|---|---|
| Dependency not found | Repository configuration, coordinates, network |
| Compilation failure | Java version, compiler configuration, source code |
| Tests failing | Test output under `target/surefire-reports` |
| Integration tests failing | `target/failsafe-reports`, environment configuration |
| Plugin resolution failure | Plugin repository/network configuration |
| Could not transfer artifact | Repository credentials/network |
| Unsupported class version | Java runtime/compiler versions |
| Dependency conflict | `mvn dependency:tree` |
| Build works locally but not CI | Maven/Java versions, settings, profiles, environment |

---

## Quick Troubleshooting Workflow

```bash
# 1. Check Java
java -version

# 2. Check Maven
mvn -version

# 3. Clean previous output
mvn clean

# 4. Inspect dependencies
mvn dependency:tree

# 5. Build with detailed errors
mvn clean verify -e

# 6. If necessary, enable full debug output
mvn clean verify -X
```

---

## Command Cheat Sheet

| Task | Command |
|---|---|
| Maven version | `mvn -v` |
| Clean build output | `mvn clean` |
| Compile | `mvn compile` |
| Run tests | `mvn test` |
| Package JAR/WAR | `mvn package` |
| Verify project | `mvn verify` |
| Install locally | `mvn install` |
| Deploy remotely | `mvn deploy` |
| Clean + package | `mvn clean package` |
| Clean + install | `mvn clean install` |
| Skip tests | `mvn package -DskipTests` |
| Run one test | `mvn test -Dtest=MyTest` |
| Dependency tree | `mvn dependency:tree` |
| Analyze dependencies | `mvn dependency:analyze` |
| Force updates | `mvn clean install -U` |
| Offline build | `mvn -o clean package` |
| Debug build | `mvn -X clean package` |
| Batch build | `mvn -B clean verify` |
| Activate profile | `mvn clean package -Pprofile` |
| Effective POM | `mvn help:effective-pom` |
| Active profiles | `mvn help:active-profiles` |
| Create JAR | `mvn package` |
| Spring Boot run | `mvn spring-boot:run` |
| Create chart | N/A |
| Multi-module build | `mvn clean install -pl module -am` |
| Parallel build | `mvn clean install -T 1C` |

---

## Typical Developer Workflow

```text
Edit Code
   ↓
mvn test
   ↓
mvn package
   ↓
mvn verify
   ↓
mvn install
   ↓
Run Application
```

For a clean build:

```bash
mvn clean verify
```

For a local install:

```bash
mvn clean install
```

For CI/CD:

```bash
mvn -B clean verify
```

For a production release:

```bash
mvn -B clean deploy
```
