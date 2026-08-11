# Maven Documentation

Apache Maven is a build automation and dependency management tool commonly used for Java projects.

---

## Basic Syntax

```bash
mvn [options] [phase(s)] [goal(s)]
```

Examples:

```bash
mvn clean
mvn compile
mvn test
mvn package
mvn install
```

Multiple phases can be run in one command:

```bash
mvn clean package
```

---

## Maven Project Structure

A typical Maven project:

```text
my-project/
├── pom.xml
├── src/
│   ├── main/
│   │   ├── java/
│   │   └── resources/
│   └── test/
│       ├── java/
│       └── resources/
└── target/
```

| Directory | Purpose |
|---|---|
| `src/main/java` | Application source code |
| `src/main/resources` | Application resources |
| `src/test/java` | Test source code |
| `src/test/resources` | Test resources |
| `target` | Build output |
| `pom.xml` | Maven project configuration |

---

## Maven Lifecycle

Maven has three primary built-in lifecycles:

| Lifecycle | Purpose |
|---|---|
| `clean` | Removes build output |
| `default` | Builds, tests, packages, and installs the project |
| `site` | Generates project documentation |

The most commonly used default lifecycle phases are:

```text
validate
   ↓
compile
   ↓
test
   ↓
package
   ↓
verify
   ↓
install
   ↓
deploy
```


---
## POM (`pom.xml`)

The `pom.xml` is Maven's primary project configuration file.

Basic example:

```xml
<project>
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>my-app</artifactId>
    <version>1.0.0</version>

    <properties>
        <maven.compiler.source>21</maven.compiler.source>
        <maven.compiler.target>21</maven.compiler.target>
    </properties>

    <dependencies>
        <!-- Dependencies go here -->
    </dependencies>
</project>
```

### Core Coordinates

| Element | Purpose |
|---|---|
| `groupId` | Organization/project namespace |
| `artifactId` | Project/artifact name |
| `version` | Project version |
| `packaging` | Output type, e.g. `jar`, `war` |
| `name` | Human-readable project name |

Example:

```xml
<groupId>com.example</groupId>
<artifactId>my-app</artifactId>
<version>1.0.0</version>
<packaging>jar</packaging>
```

---

## Dependencies

Declare dependencies in `pom.xml`:

```xml
<dependency>
    <groupId>org.example</groupId>
    <artifactId>example-library</artifactId>
    <version>1.2.3</version>
</dependency>
```

Then Maven resolves them automatically:

```bash
mvn compile
```

---

## Dependency Scopes

Common dependency scopes:

| Scope | Compile | Test | Runtime | Packaged |
|---|---:|---:|---:|---:|
| `compile` | Yes | Yes | Yes | Yes |
| `provided` | Yes | Yes | Yes | No |
| `runtime` | No | Yes | Yes | Yes |
| `test` | No | Yes | No | No |
| `system` | Yes | Yes | Yes | Usually no |

Example:

```xml
<dependency>
    <groupId>org.example</groupId>
    <artifactId>example</artifactId>
    <version>1.0.0</version>
    <scope>test</scope>
</dependency>
```

---
## Dependency Management

`dependencyManagement` controls dependency versions without necessarily adding the dependency to the project.

Example:

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.example</groupId>
            <artifactId>example-library</artifactId>
            <version>1.2.3</version>
        </dependency>
    </dependencies>
</dependencyManagement>
```

A dependency can then omit its version:

```xml
<dependency>
    <groupId>org.example</groupId>
    <artifactId>example-library</artifactId>
</dependency>
```

This is commonly used by parent POMs and BOMs.

---

## BOMs

A Bill of Materials (BOM) centralizes dependency versions.

Example:

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.example</groupId>
            <artifactId>example-bom</artifactId>
            <version>1.0.0</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

Then individual dependencies can omit versions managed by the BOM.

---
