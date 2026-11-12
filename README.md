[View Claude Chat](https://claude.ai/share/3967468b-b6b2-4197-806e-ffcc02cee72f)


# SB8050 DevOps Practical: Exam Guide

This guide covers Exercises 1 to 12 from the lab manual `DevOps.docx`.
All values are changed to **my own setup**, not the sample values in the manual.

## Table of Contents

- [Your setup and warnings](#your-setup-and-warnings)
- [Exercise 1: Testing Lab Set Up](#exercise-1-testing-lab-set-up)
- [Exercise 2: Git Operations using Eclipse EGit](#exercise-2-git-operations-using-eclipse-egit)
- [Exercise 3: Creating a Project in SonarQube](#exercise-3-creating-a-project-in-sonarqube)
- [Exercise 4: Using SonarQube with Sonar-Runner](#exercise-4-using-sonarqube-with-sonar-runner)
- [Exercise 5: Creating a Local Repository in Artifactory](#exercise-5-creating-a-local-repository-in-artifactory)
- [Exercise 6: Build Automation using Maven](#exercise-6-build-automation-using-maven)
- [Exercise 7: Jenkins Installation and System Configuration](#exercise-7-jenkins-installation-and-system-configuration)
- [Exercise 8: Download the Plugins in Jenkins](#exercise-8-download-the-plugins-in-jenkins)
- [Exercise 9: Creating the Central CI Pipeline](#exercise-9-creating-the-central-ci-pipeline)
- [Exercise 10: Copying and Moving Jobs](#exercise-10-copying-and-moving-jobs)
- [Exercise 11: Creating Pipeline View in Jenkins](#exercise-11-creating-pipeline-view-in-jenkins)
- [Exercise 12: Configuring Gating Conditions](#exercise-12-configuring-gating-conditions)
- [Additional Exercises](#additional-exercises)
- [Quick fixes](#quick-fixes)

---

## Your setup and warnings

| Item | My value | Manual sample value |
|---|---|---|
| GitHub repo | https://github.com/udhay-lang/devopsExam (branch `master`) | Parkbong7/calculator |
| Package and Group ID | `com.calculator` | calculator |
| Artifact ID | `Calculator` | calculator |
| JDK | 21 | 25 |
| Maven | 3.10.0 | 3.9.16 |
| IDE | Eclipse | Eclipse |
| Jenkins | http://localhost:8080 | http://localhost:8081 |
| SonarQube | http://localhost:9000 | http://localhost:9000 |
| Artifactory | http://localhost:8082 | http://localhost:8082 |

**Read these warnings first.**

1. **Ports.** Jenkins uses `8080`, SonarQube uses `9000` and Artifactory uses `8082`. They are all different, so there is no clash. Port `8080` is also the default port of other programs (for example Tomcat). If Jenkins does not start, check that nothing else uses `8080`. I am not sure which other programs run on your computer.
2. **Maven 3.10.0.** I am not sure that this version exists. Run `mvn -version` and use the version it really shows. Use that same version number in the Jenkins tool name and path.
3. **JDK 21 path.** I am not sure about your exact folder. The usual path is `C:\Program Files\Java\jdk-21`. Check yours with `where java`.
4. **Plugin versions** (JUnit 5.11.4, Surefire 3.5.2, JaCoCo 0.8.14, Sonar plugin 5.0.0.4389) are copied from the manual. I did not test them with JDK 21.
5. **Folder paths** like `C:\sonarqube...` and `C:\JenkinsLab\home` are from the manual. Your real folders may be different. Check them.
6. The manual has **no Additional Exercises**. See [Additional Exercises](#additional-exercises).
7. **Tokens.** `YOUR_TOKEN` means a token you make yourself. Never save a real token in GitHub.

**Rules for commands:** Run every command in Windows CMD. Each command is on one line. Run Maven commands in the folder that contains `pom.xml`.

```bat
cd /d C:\YOUR_WORKSPACE\calculator
dir
```

You must see `pom.xml` and `src` in the list.

**Repo name and project name are different.** The GitHub repository is `devopsExam`. The Eclipse project in this guide is `calculator`. This is fine. But `pom.xml` and the `src` folder must be in the **root** of the GitHub repository. Jenkins reads `pom.xml` from the root. If your files are inside a sub-folder on GitHub, Jenkins will say `no POM in this directory`. I am not sure how your repository is laid out. Open the repository on GitHub and check.

---

## Exercise 1: Testing Lab Set Up

**Aim:** Install and check all the tools needed for all the exercises.

### Commands

```bat
java -version
```
```bat
javac -version
```
```bat
git --version
```
```bat
mvn -version
```
```bat
where mvn
```
```bat
where java
```
```bat
echo %JAVA_HOME%
```

Set `JAVA_HOME` if it is empty (change the path to your real JDK folder):

```bat
setx JAVA_HOME "C:\Program Files\Java\jdk-21"
```

Close CMD and open it again after `setx`.

Start SonarQube (change the path to where you extracted it):

```bat
cd /d C:\sonarqube\sonarqube-26.9.0.129388\bin\windows-x86-64
```
```bat
StartSonar.bat
```

Start Jenkins with the batch file below (see the code section):

```bat
C:\Jenkins\start-jenkins.bat
```

### Code, configuration and steps

**Steps:**

1. Check Java, Git and Maven with the commands above.
2. Open Eclipse. Make a workspace, for example `C:\Eclipse\calculator`. Eclipse already has EGit.
3. Open GitHub. Create a new repository named `devopsExam`.
4. In Eclipse: **File, New, Java Project**. Name it `calculator`.
5. Right-click `src`, then **New, Class**. Package: `com.calculator`. Name: `Calculator` (capital C).
6. Paste the code below and run it.
7. Right-click the project, then **Configure, Convert to Maven Project**. Group Id: `com.calculator`. Artifact Id: `Calculator`. Click **Finish**.
8. Open `pom.xml` and replace everything with the pom below.
9. Start SonarQube. Open http://localhost:9000.
10. Start Jenkins. Open http://localhost:8080.

**File:** `src\com\calculator\Calculator.java`

```java
package com.calculator;

public class Calculator {

    public static void main(String[] args) {
        int a = 10;
        int b = 5;
        System.out.println("Addition: " + (a + b));
        System.out.println("Subtraction: " + (a - b));
        System.out.println("Multiplication: " + (a * b));
        System.out.println("Division: " + (a / b));
    }
}
```

**File:** `pom.xml` (basic version, used until Exercise 8)

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.calculator</groupId>
  <artifactId>Calculator</artifactId>
  <version>0.0.1-SNAPSHOT</version>
  <properties>
    <maven.compiler.release>21</maven.compiler.release>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  </properties>
  <build>
    <sourceDirectory>src</sourceDirectory>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.13.0</version>
      </plugin>
    </plugins>
  </build>
</project>
```

**File:** `C:\Jenkins\start-jenkins.bat` (save as type "All Files", not `.txt`)

```bat
@echo off
set JENKINS_HOME=C:\JenkinsLab\home
java -jar "C:\Jenkins\jenkins.war" --httpPort=8080
pause
```

If Jenkins runs as a Windows service (you have `jenkins.exe` and `jenkins.xml`), do not use the batch file. Open `jenkins.xml`, find `--httpPort=` and change it to `--httpPort=8080`. Then restart the service in `services.msc`.

**File:** `.gitignore` in the project folder

```
target/
bin/
```

### Expected output

- `java -version` shows version 21. `javac -version` shows `javac 21...`.
- `git --version` shows `git version 2.x`.
- `mvn -version` shows the Maven version and Java 21.
- Eclipse Console shows:

```
Addition: 15
Subtraction: 5
Multiplication: 50
Division: 2
```

- SonarQube page opens at `http://localhost:9000`.
- Jenkins page opens at `http://localhost:8080`.

### Screenshots

1. `java -version` and `javac -version`
2. `git --version`
3. `mvn -version`
4. Calculator output in Eclipse
5. SonarQube open at `http://localhost:9000` (address bar visible)
6. Jenkins open at `http://localhost:8080` (address bar visible)

### Common errors

| Error | Fix |
|---|---|
| `'java' is not recognized` | Add the JDK `bin` folder to `Path`. Open a new CMD. |
| `java -version` shows an old version | Another Java is first in `Path`. Run `where java` and fix the order. |
| `'mvn' is not recognized` | Add the Maven `bin` folder to `Path`. Open a new CMD. |
| `Unresolved compilation problem` or `wrong name: calculator/Calculator` | File name and class name must match exactly (capital C). The first line must be `package com.calculator;`. Delete `module-info.java`. Run **Project, Clean**. |
| Red mark on `<project` in `pom.xml` (`cvc-elt.1.a`, `DownloadResourceDisabled`) | This is only an Eclipse warning. Maven still works. Ignore it. |
| SonarQube page does not open | Wait 1 to 3 minutes. Do not close the black window. Wait for `SonarQube is operational`. |
| Jenkins page does not open | First start takes 2 to 5 minutes. Wait for `Jenkins is fully up and running`. |
| `Address already in use` | Another program uses the port. Close the old black window. Jenkins uses port 8080. Find the program that uses this port and close it. |

---

## Exercise 2: Git Operations using Eclipse EGit

**Aim:** Do basic Git operations (share, commit, push, clone) using Eclipse EGit.

### Commands

The manual uses the Eclipse menus. These are the same steps in CMD. Use them only if EGit gives trouble.

```bat
git config --global user.name "YOUR_NAME"
```
```bat
git config --global user.email "YOUR_EMAIL"
```
```bat
cd /d C:\YOUR_WORKSPACE\calculator
```
```bat
git init
```
```bat
git add .
```
```bat
git commit -m "first commit"
```
```bat
git branch -M master
```
```bat
git remote add origin https://github.com/udhay-lang/devopsExam.git
```
```bat
git push -u origin master
```

Clone into another folder:

```bat
cd /d C:\Eclipse
```
```bat
git clone https://github.com/udhay-lang/devopsExam.git calculator2
```

### Code, configuration and steps

**Steps in Eclipse (EGit):**

1. Make the GitHub repository (done in Exercise 1).
2. In Eclipse, make sure the `calculator` project runs.
3. Right-click the project, then **Team, Share Project**. Choose **Git**. Click **Create**, then **Finish**.
4. Right-click the project, then **Team, Commit**. Click the green **++** icon to stage all files. Do not stage `target`. Type the message `first commit`. Click **Commit**.
5. Right-click the project, then **Team, Push Branch 'master'**.
6. Remote URL: `https://github.com/udhay-lang/devopsExam.git`
7. User: `udhay-lang`. Password: your GitHub **Personal Access Token** (`YOUR_TOKEN`). A GitHub password does not work. Tick **Store in Secure Store**.
8. Click **Preview**, then **Push**.
9. Open the repo in the browser. Check that your files are there.
10. To clone: **File, Switch Workspace, Other**. Use a new folder such as `C:\Eclipse\calculator2`.
11. **File, Import, Git, Projects from Git, Clone URI**. Paste the repo URL. Enter user and token. Choose `master`. Import the project.
12. Switch back to the old workspace when you finish.

**How to make a token:** GitHub, profile photo, **Settings, Developer settings, Personal access tokens, Tokens (classic), Generate new token (classic)**. Tick `repo`. Click **Generate token**. Copy it at once. It is shown only one time.

### Expected output

- Push window shows: `Pushed to https://github.com/udhay-lang/devopsExam` and `master -> master [new branch]`.
- The GitHub repo shows `src`, `pom.xml`, `.project`, `.classpath`.
- The cloned project appears in the new workspace.

### Screenshots

1. Calculator program output
2. Eclipse Git commit (Git Staging or History view)
3. GitHub repository with the files
4. Cloned project in Eclipse

### Common errors

| Error | Fix |
|---|---|
| `Authentication failed` | Use the token as the password. Check that the token has the `repo` tick. |
| `Repository not found` | Check the spelling: `udhay-lang/devopsExam`. |
| `rejected` or `non-fast-forward` | GitHub has files that you do not have. Run `git pull origin master --rebase`, then push again. |
| `Unborn branch` message | Normal for the first commit. Just commit. |
| Push button is grey | Click **Preview** first. |
| Import says project already exists | You are still in the old workspace. Switch to the new workspace first. |

---

## Exercise 3: Creating a Project in SonarQube

**Aim:** Create a project in SonarQube and analyse it.

### Commands

Start SonarQube (change the path to your folder):

```bat
cd /d C:\sonarqube\sonarqube-26.9.0.129388\bin\windows-x86-64
```
```bat
StartSonar.bat
```

Run the analysis in the project folder, in a second CMD window:

```bat
cd /d C:\YOUR_WORKSPACE\calculator
```
```bat
mvn clean verify org.sonarsource.scanner.maven:sonar-maven-plugin:sonar -Dsonar.projectKey=calculator -Dsonar.projectName=calculator -Dsonar.host.url=http://localhost:9000 -Dsonar.token=YOUR_TOKEN
```

### Code, configuration and steps

1. Start SonarQube. Wait for `SonarQube is operational`. Do not close the black window.
2. Open http://localhost:9000. Log in. First time: user `admin`, password `admin`. It asks for a new password. Write it down.
3. Click **Create a local project**.
4. Project display name: `calculator`. Project key: `calculator`. Main branch: `master`. Click **Next**.
5. Choose **Use the global setting**. Click **Create project**.
6. Choose **Locally**.
7. Make a token: name `calculator-token`, expires in 30 days. Click **Generate**. Copy the token. Keep it in Notepad. It is shown only one time.
8. Choose **Maven**.
9. Run the Maven command above. Put your own token in place of `YOUR_TOKEN`.
10. Refresh the SonarQube page. Open the project `calculator`.

### Expected output

- CMD ends with `BUILD SUCCESS`.
- The SonarQube dashboard shows Security, Reliability, Maintainability, Issues, Coverage and Duplications.
- Coverage shows `0.0%` or "not computed". This is normal. There are no tests yet.

### Screenshots

1. SonarQube dashboard with Security, Reliability, Maintainability, Issues, Coverage and Duplications

### Common errors

| Error | Fix |
|---|---|
| `No goals have been specified` or `no POM in this directory` | You are in the wrong folder. Use `cd /d` to the folder that has `pom.xml`. |
| `Not authorized` | The token is wrong or old. Make a new token. |
| `Connection refused` | SonarQube is not running yet. Start it and wait. |
| `The system cannot find the path specified` when starting Sonar | The path has `.zip` in it. Extract the ZIP file first. |
| SonarQube stops at start (paging file error) | See [Quick fixes](#quick-fixes). |

---

## Exercise 4: Using SonarQube with Sonar-Runner

**Aim:** Do static code analysis, find technical issues, and fix them.

### Commands

Run these in the project folder. Run the same command two times: before and after the fix.

```bat
mvn clean verify org.sonarsource.scanner.maven:sonar-maven-plugin:sonar -Dsonar.projectKey=calculator -Dsonar.projectName=calculator -Dsonar.host.url=http://localhost:9000 -Dsonar.token=YOUR_TOKEN
```

Push the fix to GitHub:

```bat
git add .
```
```bat
git commit -m "fixed sonar issue"
```
```bat
git push origin master
```

### Code, configuration and steps

1. Start SonarQube. Open http://localhost:9000.
2. Run the Maven command above.
3. Open the project. Click **Issues**. Read each issue. It shows the line and the reason.
4. Fix the issue. The usual issue is **"Replace this use of System.out by a logger"**. I am not sure which issues your analysis will show. Fix what SonarQube shows you. If it shows this one, use the code below.
5. Run the Maven command again.
6. Refresh the dashboard. The issue count should be lower.

**File:** `src\com\calculator\Calculator.java` (fixed version)

```java
package com.calculator;

import java.util.logging.Level;
import java.util.logging.Logger;

public class Calculator {

    private static final Logger LOGGER = Logger.getLogger(Calculator.class.getName());

    public static void main(String[] args) {
        int a = 10;
        int b = 5;
        LOGGER.log(Level.INFO, "Addition: {0}", a + b);
        LOGGER.log(Level.INFO, "Subtraction: {0}", a - b);
        LOGGER.log(Level.INFO, "Multiplication: {0}", a * b);
        LOGGER.log(Level.INFO, "Division: {0}", a / b);
    }
}
```

### Expected output

- Maven ends with `BUILD SUCCESS`.
- Eclipse Console shows lines like `INFO: Addition: 15` (the output looks different now, this is normal).
- After the fix, the Issues page has fewer issues.

### Screenshots

1. Maven analysis showing `BUILD SUCCESS`
2. SonarQube project dashboard
3. Issues page
4. Updated analysis after the fix

### Common errors

| Error | Fix |
|---|---|
| Dashboard still shows the old issue | Run the analysis again. Press Ctrl + F5 in the browser. |
| Issue is different from the one in this guide | Click the issue. Read "Why is this an issue?". Fix that one. |
| Same errors as Exercise 3 | See the Exercise 3 table. |

---
## Exercise 5: Creating a Local Repository in Artifactory

**Aim:** Create a local repository in Artifactory. It will store the build files (`.jar` or `.war`) made by Maven and Jenkins.

### Commands

The manual gives no command for this exercise. It is done in the browser.

I am **not sure** how to start Artifactory on your computer. It depends on how it was installed. Try these in order:

1. Open `services.msc`. Look for a service named Artifactory. Right-click it and choose **Start**.
2. If there is no service, look in the Artifactory folder, then `app\bin`. Look for `artifactory.bat`. Run it in CMD.

Jenkins uses port 8080 and Artifactory uses port 8082. You can run both at the same time.

### Code, configuration and steps

1. Start Artifactory. Open http://localhost:8082.
2. Log in as `admin`. The manual says the default password is `password`. The first login asks you to set a new password. Write it down.
3. Go to **Administration, Repositories, Repositories**.
4. Click **Add Repositories**. Choose **Local Repository**.
5. Package type: **Maven**.
6. Repository Key: `calculator-local`.
7. Keep the other settings as default.
8. Click **Create Local Repository** (also called Save and Finish).
9. Go to **Application, Artifactory, Artifacts**. Check that `calculator-local` is in the tree.

### Expected output

- The repository `calculator-local` is in the repository list.
- The repository is in the Artifacts tree.

### Screenshots

1. Artifactory login or home page
2. Add Repositories screen with Local Repository and Maven chosen
3. Repository Key `calculator-local` entered
4. New repository in the Repositories list or the Artifacts tree

### Common errors

| Error | Fix |
|---|---|
| Page does not open at 8082 | Wait 2 to 3 minutes after start. Check that Artifactory is really running. Check that no other program uses port 8082. |
| Page loads slowly | Artifactory is heavy. Wait 2 to 3 minutes after start. |
| Login fails with `password` | The password was already changed. Use the one you set. I do not know your reset method. |
| Add Repositories button is missing | Open **Administration** from the top menu. Look in **Repositories** again. |

---

## Exercise 6: Build Automation using Maven

**Aim:** Understand build automation using Maven.

### Commands

Run these four in the project folder, in this order. `pom.xml` must be in that folder.

```bat
cd /d C:\YOUR_WORKSPACE\calculator
```
```bat
mvn clean
```
```bat
mvn compile
```
```bat
mvn test
```
```bat
mvn package
```

You can also run it in Eclipse: right-click the project, **Run As, Maven build...**, and type only the goal (`clean`, `compile`, `test` or `package`) in the **Goals** box.

### Code, configuration and steps

`pom.xml` holds the project settings, dependencies and build information. Use the basic `pom.xml` from Exercise 1.

The Maven lifecycle used here:

| Command | What it does |
|---|---|
| `mvn clean` | Deletes the old `target` folder. |
| `mvn compile` | Compiles the Java code. |
| `mvn test` | Runs the tests. |
| `mvn package` | Makes the `.jar` file. |

### Expected output

Each command ends with `BUILD SUCCESS`.

- `mvn test` now says `No tests to run.` and still gives `BUILD SUCCESS`. There are no tests yet. They are added in Exercise 9.
- `mvn package` makes `target\Calculator-0.0.1-SNAPSHOT.jar`.

After Exercise 9 the packaging is `war`. Then the file is `target\Calculator-0.0.1-SNAPSHOT.war`.

### Screenshots

1. `mvn clean`: BUILD SUCCESS
2. `mvn compile`: BUILD SUCCESS
3. `mvn test`: BUILD SUCCESS
4. `mvn package`: BUILD SUCCESS and the jar file in `target`

### Common errors

| Error | Fix |
|---|---|
| `no POM in this directory` | Wrong folder. Use `cd /d` to the folder with `pom.xml`. |
| `'mvn' is not recognized` | Add the Maven `bin` folder to `Path`. Open a new CMD. |
| `release version 21 not supported` | Maven is using an old Java. Run `mvn -version`. Fix `JAVA_HOME`. |
| `Could not transfer artifact` | No internet or a proxy problem. Check the internet and try again. |
| Jar is not in `target` | Press F5 on the project in Eclipse. Or look in the folder in File Explorer. |

---

## Exercise 7: Jenkins Installation and System Configuration

**Aim:** Set up Jenkins for Continuous Integration. Jenkins runs at http://localhost:8080.

### Commands

Start Jenkins (if you are not using the Windows service):

```bat
C:\Jenkins\start-jenkins.bat
```

Read the first-time admin password:

```bat
type C:\JenkinsLab\home\secrets\initialAdminPassword
```

Push the project to GitHub before you build (Jenkins reads the code from GitHub):

```bat
git add .
```
```bat
git commit -m "ready for jenkins"
```
```bat
git push origin master
```

### Code, configuration and steps

**First time only:** Open http://localhost:8080. Paste the admin password. Click **Install suggested plugins**. Create an admin user. Write the username and password down.

**Part A: Configure JDK**

1. **Manage Jenkins, Tools, JDK installations, Add JDK**.
2. Name: `JDK-21`
3. JAVA_HOME: `C:\Program Files\Java\jdk-21` (check your real path)
4. Untick **Install automatically**.

**Part B: Configure Maven**

1. In **Maven installations**, click **Add Maven**.
2. Name: `Maven-3.10.0`
3. MAVEN_HOME: `C:\apache-maven-3.10.0` (check with `where mvn`)
4. Untick **Install automatically**.
5. Click **Save**.

**Part C: Global environment variables**

1. **Manage Jenkins, System, Global properties**.
2. Tick **Environment variables**.
3. Add `JAVA_HOME` = `C:\Program Files\Java\jdk-21`
4. Add `M2_HOME` = `C:\apache-maven-3.10.0`
5. Click **Save**.

**Part D: Create a CI job**

1. **Dashboard, New Item**. Name: `calculator-CI`. Choose **Freestyle project**. Click **OK**.
2. **Source Code Management**: choose **Git**.
3. Repository URL: `https://github.com/udhay-lang/devopsExam.git`
4. Branch: `*/master`
5. **Build Steps, Add build step, Invoke top-level Maven targets**.
6. Maven Version: `Maven-3.10.0`. Goals: `clean package`
7. Click **Save**, then **Build Now**.
8. Open the build number. Click **Console Output**.

If the repository is private, add credentials in Jenkins: username `udhay-lang`, password `YOUR_TOKEN`. A public repository needs no credentials.

### Expected output

Console Output ends with:

```
BUILD SUCCESS
Finished: SUCCESS
```

Build #1 shows a blue or green mark.

### Screenshots

1. JDK and Maven configuration (**Manage Jenkins, Tools**)
2. Global environment variables
3. Job configuration (Git and Maven build step)
4. Build #1 with a green tick
5. Console Output ending in `BUILD SUCCESS`

### Common errors

| Error | Fix |
|---|---|
| `Failed to connect to repository` or `git not found` | Install Git. In **Manage Jenkins, Tools, Git installations**, set the path to `git.exe`. |
| `Could not find the Maven installation` | The Maven name in the job must be the same as in **Tools**. |
| `release version 21 not supported` | Jenkins is using a different Java. Check the `JAVA_HOME` in Global properties. |
| `Authentication failed` for GitHub | Make the repo public or add credentials with `YOUR_TOKEN`. |
| `No POM in this directory` in Jenkins | `pom.xml` is not in the repo root. Check on GitHub. |
| Build is stuck, `Waiting for next available executor` | The node is offline. See [Quick fixes](#quick-fixes). |
| Forgot the Jenkins password | See [Quick fixes](#quick-fixes). |

---

## Exercise 8: Download the Plugins in Jenkins

**Aim:** Install the plugins needed for the CI pipeline.

### Commands

The manual gives no command. It is done in the Jenkins browser page.

### Code, configuration and steps

1. Open http://localhost:8080.
2. **Manage Jenkins, Plugins**.
3. Open the **Available plugins** tab.
4. Search for each plugin and tick its box:
   - Copy Artifact
   - JaCoCo
   - Artifactory
   - Build Pipeline
   - Deploy to container
5. Click **Install** (in some versions the button is called **Install without restart**).
6. Wait until every plugin shows **Success**.
7. Open **Installed plugins**. Check all five are listed.
8. If Jenkins asks to restart, tick **Restart Jenkins when installation is complete**. I am not sure all five need a restart. A restart is safe.

### Expected output

The install page shows `Success` for all five plugins. **Installed plugins** lists them.

### Screenshots

1. Available plugins with the five plugins ticked
2. Installation progress page showing Success
3. Installed plugins list

### Common errors

| Error | Fix |
|---|---|
| Available plugins list is empty | No internet, or the update list is old. Click **Check now**. |
| A plugin shows `Failure` | Click **Retry**. Check the internet. |
| Plugin is not found in search | Search with part of the name. Names can be a little different. |
| Jenkins page does not return after restart | Wait 2 to 5 minutes. Refresh. |

---

## Exercise 9: Creating the Central CI Pipeline

**Aim:** Make a chain of linked Jenkins jobs (Checkout, Compile, UnitTest, Coverage, StaticAnalysis). Each job starts the next one.

### Commands

Run in the project folder. Check that it builds before you use Jenkins:

```bat
mvn clean test
```

Push to GitHub:

```bat
git add .
```
```bat
git commit -m "added tests and jacoco"
```
```bat
git push origin master
```

### Code, configuration and steps

**Part 1: Prepare the project**

1. Replace `Calculator.java` with the code below.
2. Make the folder `test\com\calculator` in the project. Make `CalculatorTest.java` in it.
3. Replace `pom.xml` with the pom below.
4. In Eclipse: right-click the project, **Maven, Update Project** (Alt + F5).
5. Push to GitHub. Run `calculator-CI` once to check that it still builds.

**File:** `src\com\calculator\Calculator.java`

```java
package com.calculator;

import java.util.logging.Level;
import java.util.logging.Logger;

public class Calculator {

    private static final Logger LOGGER = Logger.getLogger(Calculator.class.getName());

    public int add(int a, int b) {
        return a + b;
    }

    public int subtract(int a, int b) {
        return a - b;
    }

    public int multiply(int a, int b) {
        return a * b;
    }

    public int divide(int a, int b) {
        return a / b;
    }

    public static void main(String[] args) {
        Calculator calculator = new Calculator();
        int a = 10;
        int b = 5;
        LOGGER.log(Level.INFO, "Addition: {0}", calculator.add(a, b));
        LOGGER.log(Level.INFO, "Subtraction: {0}", calculator.subtract(a, b));
        LOGGER.log(Level.INFO, "Multiplication: {0}", calculator.multiply(a, b));
        LOGGER.log(Level.INFO, "Division: {0}", calculator.divide(a, b));
    }
}
```

Dividing an `int` by zero throws `ArithmeticException` by itself. So no extra `if` is needed.

**File:** `test\com\calculator\CalculatorTest.java`

```java
package com.calculator;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertThrows;

import org.junit.jupiter.api.Test;

class CalculatorTest {

    private final Calculator calculator = new Calculator();

    @Test
    void testAdd() {
        assertEquals(15, calculator.add(10, 5));
    }

    @Test
    void testSubtract() {
        assertEquals(5, calculator.subtract(10, 5));
    }

    @Test
    void testMultiply() {
        assertEquals(50, calculator.multiply(10, 5));
    }

    @Test
    void testDivide() {
        assertEquals(2, calculator.divide(10, 5));
    }

    @Test
    void testDivideByZero() {
        assertThrows(ArithmeticException.class, () -> calculator.divide(10, 0));
    }
}
```

**File:** `pom.xml` (full version for Exercise 9 to 12)

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.calculator</groupId>
  <artifactId>Calculator</artifactId>
  <version>0.0.1-SNAPSHOT</version>
  <packaging>war</packaging>

  <properties>
    <maven.compiler.release>21</maven.compiler.release>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <failOnMissingWebXml>false</failOnMissingWebXml>
  </properties>

  <dependencies>
    <dependency>
      <groupId>org.junit.jupiter</groupId>
      <artifactId>junit-jupiter</artifactId>
      <version>5.11.4</version>
      <scope>test</scope>
    </dependency>
  </dependencies>

  <build>
    <sourceDirectory>src</sourceDirectory>
    <testSourceDirectory>test</testSourceDirectory>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.13.0</version>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-surefire-plugin</artifactId>
        <version>3.5.2</version>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-war-plugin</artifactId>
        <version>3.4.0</version>
      </plugin>
      <plugin>
        <groupId>org.jacoco</groupId>
        <artifactId>jacoco-maven-plugin</artifactId>
        <version>0.8.14</version>
        <executions>
          <execution>
            <id>prepare-agent</id>
            <goals>
              <goal>prepare-agent</goal>
            </goals>
          </execution>
          <execution>
            <id>report</id>
            <phase>test</phase>
            <goals>
              <goal>report</goal>
            </goals>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
</project>
```

Note: the manual says only "goals prepare-agent and report". It does not say when `report` runs. I added `<phase>test</phase>` so that `target\site\jacoco\jacoco.xml` is made during `mvn test`. SonarQube needs that file. The `maven-war-plugin` version 3.4.0 is my choice, not from the manual.

**Part 2: Make the five jobs**

Tip (my own tip, not in the manual): make the jobs in **reverse order**. Make StaticAnalysis first and Checkout last. Then the name in "Build other projects" already exists.

For every job: **New Item**, name, **Freestyle project**, **OK**. In every job set:

- Source Code Management: **Git**
- Repository URL: `https://github.com/udhay-lang/devopsExam.git`
- Branch: `*/master`
- Maven Version: `Maven-3.10.0`

| Job | Maven goals | Post-build actions | Build other projects |
|---|---|---|---|
| StaticAnalysis | See the goals line below | none | none |
| Coverage | `clean test` | **Record JaCoCo coverage report**: exec files `**/target/jacoco.exec`, class directories `**/target/classes`, source directories `**/src` | `StaticAnalysis` |
| UnitTest | `test` | **Publish JUnit test result report**: `**/target/surefire-reports/*.xml` | `Coverage` |
| Compile | `clean compile` | none | `UnitTest` |
| Checkout | none (no build step) | none | `Compile` |

For every "Build other projects" choose **Trigger only if build is stable**.

**Goals for StaticAnalysis (one line):**

```
clean test org.sonarsource.scanner.maven:sonar-maven-plugin:5.0.0.4389:sonar -Dsonar.projectKey=calculator -Dsonar.projectName=calculator -Dsonar.host.url=http://localhost:9000 -Dsonar.token=YOUR_TOKEN -Dsonar.coverage.jacoco.xmlReportPaths=target/site/jacoco/jacoco.xml
```

**Part 3: Run**

1. Start SonarQube.
2. Open the `Checkout` job. Click **Build Now**.
3. Watch the dashboard. Each job should start the next one.

### Expected output

- `mvn clean test` shows `Tests run: 5, Failures: 0, Errors: 0` and `BUILD SUCCESS`.
- All five jobs are green on the dashboard.
- UnitTest has a **Test Result** page with 5 tests passed.
- Coverage has a **Coverage Report** page.
- SonarQube shows coverage and analysis after StaticAnalysis finishes.

### Screenshots

1. Configuration page of each job (Git, Maven goals, post-build action)
2. Dashboard with the jobs green
3. Test Result page (5 tests passed) and the JaCoCo Coverage Report
4. SonarQube project page after the StaticAnalysis build

### Common errors

| Error | Fix |
|---|---|
| `Tests run: 0` | The `test` folder is not read. Check `<testSourceDirectory>test</testSourceDirectory>` in `pom.xml`. Check the test is in `test\com\calculator`. |
| Eclipse says `junit cannot be resolved` | Right-click the project, **Maven, Update Project**. |
| `No tests were executed` or the JUnit report is empty | Check the path `**/target/surefire-reports/*.xml`. |
| Next job does not start | Check **Build other projects** has the exact job name. Check **Trigger only if build is stable** was chosen. |
| `target/site/jacoco/jacoco.xml` not found | The `report` goal did not run in `test`. Check the `<phase>test</phase>` line in `pom.xml`. |
| `Not authorized` in StaticAnalysis | The Sonar token is wrong. Make a new token. |
| `Failed to load class` or no coverage in the JaCoCo report | Check JaCoCo exec path `**/target/jacoco.exec` and class path `**/target/classes`. |
| JaCoCo threshold box error | See [Quick fixes](#quick-fixes). |

---
## Exercise 10: Copying and Moving Jobs

**Aim:** Copy a job and move a job in Jenkins.

### Commands

The manual gives no command. It is done in the Jenkins browser page.

### Code, configuration and steps

**Part I: Copy a job**

1. Click **New Item** in the left panel.
2. Item name: `calculator-CI-copy`.
3. Scroll down. In **Copy from** (also called **Duplicate an existing item**), type `calculator-CI`.
4. Click **OK**. The copy has the same settings as the original.
5. Click **Save**.

**Part II: Move a job**

1. **New Item**. Name: `MyFolder`. Choose **Folder**. Click **OK**, then **Save**.
2. Open the job `calculator-CI-copy`.
3. Click **Move** in the left menu.
4. Choose `MyFolder` as the destination. Click **Move**.
5. Open `MyFolder`. The job is inside.

### Expected output

- The dashboard shows `calculator-CI` and `calculator-CI-copy`.
- After the move, the job URL is:

```
http://localhost:8080/job/MyFolder/job/calculator-CI-copy/
```

### Screenshots

1. New Item page with **Duplicate an existing item** filled in
2. Dashboard showing the original job and the copy
3. Move page with the destination chosen
4. `MyFolder` showing the moved job

### Common errors

| Error | Fix |
|---|---|
| No **Folder** type in New Item | The Folders plugin is missing. Install **Folders** (CloudBees Folders) in **Manage Jenkins, Plugins**. I am not sure of the exact plugin name in your version. |
| No **Move** link on the job | The folder does not exist yet. Make `MyFolder` first. |
| Copy name is not accepted | The name is already used. Choose another name. |
| `Copy from` box is empty or missing | Type the exact job name `calculator-CI`. |

---

## Exercise 11: Creating Pipeline View in Jenkins

**Aim:** Understand how to make a pipeline view in Jenkins.

### Commands

The manual gives no command. It is done in the Jenkins browser page.

### Code, configuration and steps

1. On the dashboard, click the **+** symbol next to the **All** tab.
2. View name: `Calculator-Pipeline`.
3. Choose **Build Pipeline View**. Click **Create**.
4. Under **Upstream / downstream config**, set **Select Initial Job** to `Checkout`.
5. Click **OK**.
6. Click **Run** in the pipeline view.
7. Watch the flow.

### Expected output

The pipeline view shows boxes joined by arrows. The boxes turn green one by one.

The manual lists four boxes: Checkout, Compile, UnitTest, Coverage. But in Exercise 9 Coverage starts StaticAnalysis. So Jenkins should show five boxes. I am not sure which one your lab teacher expects. Five boxes is what Jenkins really shows.

### Screenshots

1. Create view screen with **Build Pipeline View** chosen
2. Configuration with the initial job `Checkout`
3. Pipeline view with all boxes green

### Common errors

| Error | Fix |
|---|---|
| No **Build Pipeline View** option | The Build Pipeline plugin is not installed. Do Exercise 8. Restart Jenkins. |
| The view shows only one box | The jobs are not linked. Check **Build other projects** in each job (Exercise 9). |
| **Run** does nothing | Check the initial job is `Checkout`. Check Jenkins has a free executor. |
| A box stays red | Open that job, then **Console Output**. Read the error. |

---

## Exercise 12: Configuring Gating Conditions

**Aim:** Set gating conditions. A job passes control to the next job only when a quality rule is met.

### Commands

Used in Gate A to break the test and then fix it:

```bat
git add .
```
```bat
git commit -m "break test on purpose"
```
```bat
git push origin master
```

After you restore the test:

```bat
git add .
```
```bat
git commit -m "restore test"
```
```bat
git push origin master
```

### Code, configuration and steps

The "gate" is already set. In each upstream job, **Build other projects** uses **Trigger only if build is stable**. If a job fails or becomes unstable, the next job does not start.

**Gate A: Unit testing job**

1. Open `UnitTest`, then **Configure**.
2. In **Publish JUnit test result report**, click **Advanced**. Set **Health report amplification factor** to `1.0`. Click **Save**.
3. Run the pipeline. All boxes are green.
4. In `CalculatorTest.java`, change one test so it fails. For example:

```java
assertEquals(16, calculator.add(10, 5));
```

5. Commit and push with the commands above.
6. Run the pipeline. UnitTest fails. Coverage does not start. Open the **Test Result** page.
7. Change the test back to `assertEquals(15, calculator.add(10, 5));`. Push again.
8. Run the pipeline. All boxes are green again.

**Gate B: Code coverage job**

1. Open `Coverage`, then **Configure**.
2. In **Record JaCoCo coverage report**, tick **Change build status according to the defined thresholds**.
3. **Smooth build:** Leave the storm cloud (lower) row at `0`. The build stays green.
4. **Broken build:** In the storm cloud row, set **Instruction** and **Line** to `80`. Coverage is below 80, so the build becomes **unstable**. The report shows the failing values in red. (The manual shows coverage of 34% and 38%. Your numbers will be different. They depend on your tests.)
5. Reset the thresholds to `0` and run the pipeline again.

**Gate C: Static analysis job (SonarQube quality gate)**

1. Start SonarQube. Log in at http://localhost:9000.
2. Go to **Quality Gates**. Open `CALQG` (a copy of **Sonar way**). If it does not exist, copy **Sonar way** and name the copy `CALQG`.
3. Click **Add Condition**. Add conditions such as:
   - Issues is greater than `0`
   - Coverage is less than `80`
   - Duplicated Lines (%) is greater than `3`
   - Security Hotspots Reviewed is less than `100%`
   - Maintainability Rating or Reliability Rating is worse than `A`
4. Open the project `calculator`, then **Project Settings, Quality Gate**. Choose `CALQG`. (The manual says: under **Projects**, move `calculator` to the **With** list.)
5. **Build breaker:** In the StaticAnalysis job, add `-Dsonar.qualitygate.wait=true` at the end of the Goals. The full line is:

```
clean test org.sonarsource.scanner.maven:sonar-maven-plugin:5.0.0.4389:sonar -Dsonar.projectKey=calculator -Dsonar.projectName=calculator -Dsonar.host.url=http://localhost:9000 -Dsonar.token=YOUR_TOKEN -Dsonar.coverage.jacoco.xmlReportPaths=target/site/jacoco/jacoco.xml -Dsonar.qualitygate.wait=true
```

6. **Broken build:** With strict conditions, run StaticAnalysis. The gate fails. The Jenkins build fails.
7. **Smooth build:** Relax the conditions. For example: Issues greater than `10`, Coverage less than `10`. Run again. The gate passes. The build succeeds.
8. Keep **Trigger only if build is stable** on the link to the next job.

### Expected output

| Gate | Broken case | Smooth case |
|---|---|---|
| A: UnitTest | UnitTest fails. Coverage does not start. | All five jobs green. |
| B: Coverage | Build is **unstable** (yellow). Failing values are red. | Build is green. |
| C: SonarQube | SonarQube shows **Failed**. Jenkins build fails. | SonarQube shows **Passed**. Jenkins build succeeds. |

### Screenshots

1. JUnit health report amplification factor
2. Pipeline with a failed UnitTest and Coverage not run, then the green pipeline
3. JaCoCo thresholds, then the report with failing values and the unstable build
4. `CALQG` conditions and the `calculator` project linked to it
5. SonarQube Overview showing **Failed** and the failed Jenkins build. Then **Passed** and the successful build.

### Common errors

| Error | Fix |
|---|---|
| The pipeline still continues after a failed test | Check **Trigger only if build is stable** (not "even if failed"). |
| The build does not turn unstable | Set the **storm cloud** row, not only the sunny row. Tick **Change build status according to the defined thresholds**. |
| JaCoCo threshold box error | See [Quick fixes](#quick-fixes). |
| Gate fails but Jenkins build is still green | Add `-Dsonar.qualitygate.wait=true` to the StaticAnalysis goals. |
| Gate always passes | The project is not linked to `CALQG`. Check **Project Settings, Quality Gate**. |
| `QUALITY GATE STATUS: FAILED` in the log | This is the expected result for the broken case. |
| Remember to undo | After the demo, restore the test and relax the thresholds. |

---

## Additional Exercises

The manual `DevOps.docx` has **only Exercises 1 to 12**. I checked the whole file and found no Additional Exercises. I did not invent any.

To add them: paste the text of the extra exercises into a new request. Then add them here in the same 7-part format (title, aim, commands, code and configuration, expected output, screenshots, common errors).

```
## Additional Exercise N: TITLE
**Aim:**
### Commands
### Code, configuration and steps
### Expected output
### Screenshots
### Common errors
```

---

## Quick fixes

| Problem | What you see | Fix |
|---|---|---|
| **SonarQube paging file error** | SonarQube stops at start. The log says `The paging file is too small for this operation to complete` or `Could not reserve enough space`. | **Fix 1 (more virtual memory):** Press Win + R, type `sysdm.cpl`, Enter. **Advanced, Performance Settings, Advanced, Virtual memory, Change**. Untick **Automatically manage**. Choose **Custom size**. Initial `4096` MB, Maximum `8192` MB. Click **Set, OK**. Restart the computer. **Fix 2 (less memory for SonarQube):** Open `conf\sonar.properties` in the SonarQube folder. Remove the `#` and lower the memory, for example `sonar.web.javaOpts=-Xmx512m`, `sonar.ce.javaOpts=-Xmx512m` and `sonar.search.javaOpts=-Xmx512m -Xms512m`. I am not sure about the exact default lines in your version. Look for the lines with `javaOpts`. Close other big programs. Then run `StartSonar.bat` again. |
| **Jenkins node offline (low disk space)** | The node shows offline. The message says disk space is too low. Builds wait for an executor. | **Step 1:** Free space on the `C:` drive. Empty the Recycle Bin. Run Disk Cleanup. Delete old files in `C:\Windows\Temp`. Delete old workspaces and builds in the Jenkins home folder. **Step 2:** Open **Manage Jenkins, Nodes**. Click **Built-In Node**. Click **Bring this node back online** (if the button is there). **Step 3:** To lower the limit, open **Manage Jenkins, Nodes, Configure Monitors**. Lower **Free Disk Space Threshold** and **Free Temp Space Threshold**. The menu names may be a little different in your version. Jenkins default limit is about 1 GB. |
| **JaCoCo empty threshold error** | Saving the Coverage job gives an error on the threshold boxes. | Type `0` in every empty threshold box (Instruction, Branch, Complexity, Line, Method, Class). Both rows (sunny and storm cloud) need a number. I am not sure of the exact error text in your plugin version. The usual cause is an empty box. Then click **Save**. |
| **Forgot Jenkins password** | You cannot log in. | **Step 1:** Stop Jenkins (stop the service in `services.msc`, or press Ctrl + C in the black window). **Step 2:** Open the Jenkins home folder (the manual uses `C:\JenkinsLab\home`; check `JENKINS_HOME` in your `jenkins.xml` or batch file). **Step 3:** Open `config.xml` in Notepad. Change `<useSecurity>true</useSecurity>` to `<useSecurity>false</useSecurity>`. Save it. **Step 4:** Start Jenkins. Open http://localhost:8080. You can enter without a login. **Step 5:** **Manage Jenkins, Security**. Choose **Jenkins' own user database**. Tick **Allow users to sign up**. Choose **Logged-in users can do anything**. Click **Save**. Click **Sign up** and make a new user (or open **Users**, choose your user, **Security**, and set a new password). **Step 6:** Untick **Allow users to sign up**. Save. Write down the new password. While security is off, anyone can use Jenkins. Turn it on again soon. |
| `mvn` or `java` not recognized | CMD cannot find the command. | Add the `bin` folder to `Path`. Open a new CMD window. |
| `no POM in this directory` | Maven cannot find `pom.xml`. | Use `cd /d` to go to the folder that has `pom.xml`. Check with `dir`. |
| Push rejected or authentication failed | Git push fails. | Use `YOUR_TOKEN` (a GitHub Personal Access Token) as the password. |
| Port already in use | `Address already in use`. | Close the old black window. Jenkins uses 8080, SonarQube 9000 and Artifactory 8082. Find the program that already uses that port and close it. |

---

*Never save a real token in this file or in GitHub. Always use `YOUR_TOKEN`.*





















Ex 1: Lab setup

Enna: Ella tools um install aagirukka nu verify panradhu.

CMD la run pannunga: java -version, javac -version, git --version, mvn -version, where mvn
Eclipse open panni workspace create pannunga (C:\Eclipse\calculator).
GitHub la calculator nu repo create pannunga.
Eclipse: File → New → Java Project → calculator. Calculator.java create panni doc la irukkura code ah run pannunga (Addition, Subtraction, Multiplication, Division output varum).
Project right-click → Configure → Convert to Maven Project (pom.xml varum).
SonarQube start pannunga (StartSonar.bat) → localhost:9000 open pannunga.
Jenkins bat file run pannunga → localhost:8081 open pannunga.

Screenshots: java/git/mvn versions, Eclipse output, Sonar page, Jenkins page.

Ex 2: Git using EGit

Enna: Eclipse la irundhu code ah GitHub ku push panni, clone panradhu.

Project right-click → Team → Share Project → Git → local repo create pannunga.
Team → Commit → message kudunga → Commit.
Team → Push Branch → GitHub URL kudunga → push pannunga.
Vera workspace la: File → Import → Git → Projects from Git → Clone URI → URL paste panni import pannunga.

GitHub password ketta, Personal Access Token use pannunga.

Screenshots: program output, commit, GitHub repo, cloned project.

Ex 3: SonarQube project create
localhost:9000 la login pannunga.
New project: name calculator, key calculator.
Locally select panni token generate pannunga. Idhu oru dhadava thaan kaattum, so copy pannidunga.
Project folder la idha run pannunga:
mvn clean verify org.sonarsource.scanner.maven:sonar-maven-plugin:sonar -Dsonar.projectKey=calculator -Dsonar.projectName=calculator -Dsonar.host.url=http://localhost:9000 -Dsonar.token=YOUR_TOKEN

Screenshot: Sonar dashboard (Security, Reliability, Maintainability, Issues, Coverage, Duplications).

Ex 4: Sonar static analysis
Ex 3 command ah thirumba run pannunga (BUILD SUCCESS varanum).
Sonar dashboard → Issues page la issues paarunga.
Oru issue ah code la fix pannunga.
Analysis ah thirumba run pannunga, result maarudhu nu paarunga.

Screenshots: BUILD SUCCESS, dashboard, issues, updated analysis.

Ex 5: Artifactory local repo
localhost:8082 → login admin / password (first time new password set pannanum).
Administration → Repositories → Add Repositories → Local Repository.
Package type Maven, key calculator-local → Create.
Application → Artifactory → Artifacts la repo irukka nu check pannunga.
Ex 6: Maven build

Project folder la ithai order ah run pannunga:

mvn clean (pazhaya build files delete aagum)
mvn compile (code compile aagum)
mvn test (tests run aagum)
mvn package (target\calculator-0.0.1-SNAPSHOT.jar create aagum)

Ovvondrukkum BUILD SUCCESS screenshot edunga.

Ex 7: Jenkins configuration

Manage Jenkins → Tools:

JDK: name JDK-25, path C:\Program Files\Java\jdk-25, Install automatically untick.
Maven: name Maven-3.9.16, path C:\apache-maven-3.9.16, untick auto install.

Manage Jenkins → System → Global Properties: Environment variables tick panni JAVA_HOME and M2_HOME add pannunga.

CI job:

New Item → calculator-CI → Freestyle project.
SCM: Git, repo URL, branch */master.
Build step: Invoke top-level Maven targets, version Maven-3.9.16, goals clean package.
Save → Build Now → Console Output la BUILD SUCCESS / Finished: SUCCESS paarunga.
Ex 8: Jenkins plugins

Manage Jenkins → Plugins → Available plugins. Idha search panni tick pannunga: Copy Artifact, JaCoCo, Artifactory, Build Pipeline, Deploy to container. Install pannunga, ellam Success aana apram Installed plugins la check pannunga.

Ex 9: Central CI pipeline (idhu thaan periya exercise)

Preparation:

Calculator.java la add, subtract, multiply, divide methods ah separate ah pirinchu main la call pannunga.
test\calculator\CalculatorTest.java la 5 JUnit 5 tests ezhuthunga (add, subtract, multiply, divide, divide by zero).
pom.xml update: packaging war, failOnMissingWebXml=false, junit-jupiter 5.11.4, surefire 3.5.2, jacoco 0.8.14.
Commit and push panni calculator-CI run panni check pannunga.

5 jobs create pannunga. Ellathukum same Git URL, branch, Maven 3.9.16:

Job	Goals	Post-build
Checkout	(build step illa)	Build other projects → Compile
Compile	clean compile	→ UnitTest
UnitTest	test	JUnit report **/target/surefire-reports/*.xml → Coverage
Coverage	clean test	JaCoCo report record → StaticAnalysis
StaticAnalysis	Sonar command (doc la irukku, unga token podunga)	—

Ovvondrulayum Trigger only if build is stable select pannunga. Mudinja apram Checkout ah Build Now pannunga, adhu thaana ellathayum chain ah run pannum.

Ex 10: Copy and move jobs
Copy: New Item → name calculator-CI-copy → Duplicate an existing item → calculator-CI → OK → Save.
Move: New Item → MyFolder → Folder → OK. Apram calculator-CI-copy open panni left menu la Move → MyFolder select pannunga.
Ex 11: Pipeline view
Dashboard la + click pannunga.
Name Calculator-Pipeline, Build Pipeline View select pannunga.
Initial Job: Checkout → OK.
Run click pannunga. Ella boxes um green aaganum.
Ex 12: Gating conditions

Idhoda main idea: ethavadhu fail aanaa pipeline nikkanum.

Gate A (UnitTest):

Test oru assertion ah thappa maathunga (e.g. add(10,5) ku 16 expect pannunga) → commit and push.
Pipeline run pannunga. UnitTest fail aagum, Coverage start aagaadhu.
Assertion ah sari pannitu push pannunga, apram green aagum.

Gate B (Coverage):

JaCoCo la Change build status according to thresholds tick pannunga.
Storm cloud row la Instruction and Line ah 80 nu set pannunga. Coverage 80 ku keezha irukradhunaala build unstable aagum.
Apram threshold reset pannunga.

Gate C (Sonar quality gate):

Sonar → Quality Gates → CALQG (Sonar way copy) → conditions add pannunga (Issues > 0, Coverage < 80, Duplicated Lines > 3 …).
Projects la calculator ah With list la serkunga.
StaticAnalysis goals la last la -Dsonar.qualitygate.wait=true add pannunga.
Strict conditions la run pannina fail aagum. Relax pannina (Issues > 10, Coverage < 10) pass aagum.
