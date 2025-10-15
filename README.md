# Jenkins CI/CD with Maven – Multi-stage Pipeline

This repo contains a simple Java Maven project and a Jenkins Pipeline (Jenkinsfile) that runs a multi-stage workflow using the Jenkins Maven Integration plugin:

- Compile
- Test
- Build/Package

It also publishes JUnit results and archives the JAR artifact.

## Project layout

```
Jenkinsfile
pom.xml
mvnw / mvnw.cmd
src/
  main/java/com/example/App.java
  test/java/com/example/AppTest.java
```

## What the pipeline does

1. Checkout – pulls code from SCM
2. Compile – `mvn clean compile` (tests skipped)
3. Test – `mvn test` + publish JUnit reports from `target/surefire-reports/*.xml`
4. Build – `mvn -DskipTests package` + archive `target/*.jar`

All Maven invocations run inside `withMaven(...)`, enabling the Jenkins Maven Integration plugin to capture build, tests, and artifacts metadata.

## Jenkins prerequisites

Install Jenkins 2.440+ (or latest LTS) and ensure the build agent has:

- Git
- JDK 17 (project targets Java 17)

### Required Jenkins plugins

- Pipeline
- Pipeline: Stage View
- Pipeline Maven Integration (aka `withMaven`)
- JUnit (usually bundled)
- Workspace Cleanup (for `cleanWs()` in post section) – optional

### Configure tools (Manage Jenkins > Tools)

- JDK installations
  - Name: `JDK17`
  - Set JAVA_HOME or custom path to a JDK 17 install
- Maven installations
  - Name: `Maven`
  - Enable automatic install (easiest) or point to an existing Maven installation

Names must match those referenced in `Jenkinsfile`.

## Create the job

1. New Item > Pipeline (or Multibranch Pipeline)
2. Enter a name, click OK
3. Pipeline definition: "Pipeline script from SCM"
4. SCM: Git
   - Repository URL: your repo (HTTPS/SSH)
   - Credentials: add/select if private
   - Branch: `main`
   - Script Path: `Jenkinsfile`
5. Save

## Run and verify

- Click "Build Now"
- Open the build > "Stage View" / "Pipeline Steps"
- Check "Test Result" for JUnit report (1 test, 0 failures)
- Check "Artifacts" for the packaged JAR, e.g. `target/my-java-app-1.0-SNAPSHOT.jar`

## Local quick test (Windows PowerShell)

```
./mvnw.cmd -B -ntp clean test package
```

On macOS/Linux:

```
./mvnw -B -ntp clean test package
```

## Troubleshooting

- "No such DSL method 'withMaven'": install Pipeline Maven Integration plugin
- "mvn not found": configure Maven tool or switch steps to use Maven Wrapper (`./mvnw`/`mvnw.cmd`)
- "Unsupported class file major version" or compiler errors: make sure your agent uses JDK 17
- No tests found: check `pom.xml` surefire config and that tests are under `src/test/java`

## Notes

- The pipeline is cross-platform: it uses `sh` on Unix agents and `bat` on Windows agents.
- If you prefer using Maven Wrapper in Jenkins, you can keep `withMaven { ... }` and replace commands with `./mvnw` / `mvnw.cmd`. The plugin will still collect metadata while using the wrapper.
