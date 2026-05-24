In Docker, whether we use **JDK** or **JRE** depends on the purpose of the image.

---

# 🔹 During Build → Use JDK

If you are:

* compiling Java code
* running Maven/Gradle build
* creating `.jar` files

then you need **JDK** because it contains:

* `javac` compiler
* build tools support

Example:

```dockerfile
FROM openjdk:17-jdk
WORKDIR /app

COPY . .

RUN mvn clean package
```

Here Maven compiles code → so JDK is required.

---

# 🔹 During Runtime → Use JRE

After the `.jar` is created, to only **run** the application:

```dockerfile
FROM openjdk:17-jre

COPY target/app.jar app.jar

CMD ["java", "-jar", "app.jar"]
```

Now compilation is not needed.
Only Java runtime is needed.

This makes image:

* smaller
* faster
* more secure

---

# 🔥 Best Practice → Multi-Stage Build

Experienced developers usually use:

* **JDK** in build stage
* **JRE** in runtime stage

Example:

```dockerfile
# Stage 1 - Build
FROM maven:3.9-eclipse-temurin-17 AS build

WORKDIR /app

COPY . .

RUN mvn clean package

# Stage 2 - Runtime
FROM eclipse-temurin:17-jre

WORKDIR /app

COPY --from=build /app/target/demo.jar app.jar

CMD ["java", "-jar", "app.jar"]
```

---

# 🔹 Why This Is Good

| Using JDK Only             | Using Multi-stage |
| -------------------------- | ----------------- |
| Bigger image               | Smaller image     |
| Contains unnecessary tools | Runtime only      |
| More security risk         | More secure       |
| Slower deployment          | Faster deployment |

---

# 🔹 How to Explain in Interview

> “While building Docker images for Spring Boot applications, we generally use JDK in the build stage because compilation is required. For production runtime containers, we prefer JRE or lightweight runtime images using multi-stage builds to reduce image size and improve security.”

---

## Flow

```text
Java Code
   ↓
JDK compiles code
   ↓
Creates JAR
   ↓
JRE runs JAR inside container
```
