# Docker

## Multistage dockerfile

```
# -------- Build Stage --------
FROM maven:3.9.6-eclipse-temurin-17 AS builder

WORKDIR /app
COPY pom.xml .
COPY src ./src

RUN mvn clean package -DskipTests

# -------- Run Stage --------
FROM eclipse-temurin:17-jdk-jammy

WORKDIR /app

COPY --from=builder /app/target/*.jar app.jar

CMD ["java", "-jar", "app.jar"]

```
# Project structure

```
java-multistage-app/
│
├── pom.xml
└── src/
    └── main/
        └── java/
            └── com/
                └── example/
                    └── App.java
```
# pom.xml

<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
         http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>java-multistage-app</artifactId>
    <version>1.0</version>

    <properties>
        <maven.compiler.source>17</maven.compiler.source>
        <maven.compiler.target>17</maven.compiler.target>
    </properties>

    <build>
        <plugins>
            <!-- Create executable JAR -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-jar-plugin</artifactId>
                <version>3.3.0</version>
                <configuration>
                    <archive>
                        <manifest>
                            <mainClass>com.example.App</mainClass>
                        </manifest>
                    </archive>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>

# App.java

```
package com.example;

public class App {
    public static void main(String[] args) {
        System.out.println("Hello from Maven Multi-Stage Docker Build!");
    }
}
```



## Build & Run

```
docker build -t java-maven-app .
docker run java-maven-app
```

## Output

```
Hello from Maven Multi-Stage Docker Build!
```
Important Understanding (Interview)

👉 What happens internally:

Stage 1 (Maven image)
Downloads dependencies
Runs mvn clean package
Creates .jar file inside /target
Stage 2 (Lightweight JDK image)
Copies only .jar
Runs the app

# Single-Stage Dockerfile (Java Maven)

```
FROM maven:3.9.6-eclipse-temurin-17

WORKDIR /app

# Copy project files
COPY pom.xml .
COPY src ./src

# Build the application
RUN mvn clean package -DskipTests

# Run the JAR
CMD ["java", "-jar", "target/java-multistage-app-1.0.jar"]

```

# Build & Run

```
docker build -t java-single-stage .
docker run java-single-stage
```
# project structure
```
java-multistage-app/
├── pom.xml
└── src/main/java/com/example/App.java
```


