```groovy
pipeline {
    agent {
        docker {
            image 'mcr.microsoft.com/dotnet/sdk:9.0'
        }
    }

    options {
        timestamps()
    }

    environment {
        DOTNET_CLI_HOME = "${env.WORKSPACE}/.dotnet"
        DOTNET_CLI_TELEMETRY_OPTOUT = '1'
        DOTNET_SKIP_FIRST_TIME_EXPERIENCE = "1"
    }

    stages {
        stage('Environment Check: Validate .NET SDK') {
            steps {
                sh '''
                    echo "Checking if 'dotnet' is available..."
                    if ! command -v dotnet > /dev/null; then
                        echo "ERROR: dotnet is not installed or not in PATH"
                        exit 1
                    fi

                    echo "dotnet SDK info:"
                    dotnet --info
                '''
            }
        }

        stage('Dependency Management: Restore Dependencies') {
            steps {
                sh '''
                    echo "Restoring project dependencies..."
                    dotnet restore RepositoryManager/RepositoryManager.csproj
                '''
            }
        }

        stage('Building: Build the Library') {
            steps {
                sh '''
                    echo "Running build and packaging script..."
                    dotnet build RepositoryManager/RepositoryManager.csproj  --configuration Release
                '''
            }
        }

        stage('Testing: Running Unit Test'){
            steps {
                sh '''
                    echo "Running unit test..."
                    dotnet test RepositoryManagerTest/RepositoryManagerTest.csproj
                '''
            }
        }

        stage('Packing: Create Nuget Package'){
            steps {
                sh '''
                echo "Creating Nuget Package..."
                dotnet pack RepositoryManager/RepositoryManager.csproj --configuration Release
                '''
            }
        }

        stage('Archive Artifacts: Store Nuget packages') {
            steps {
                archiveArtifacts artifacts: 'RepositoryManager/bin/Release/*.nupkg', onlyIfSuccessful: true
            }
        }
    }

    post {
        failure {
            echo '❌ Build failed. Check the log above for details.'
        }
        success {
            echo '✅ Build succeeded. Artifacts archived.'
        }
    }
}
```

This Jenkinsfile defines a **declarative pipeline** to build a .NET 9 project inside a Docker container. It’s designed for projects that require restoring dependencies, building the code, running unit tests, and packaging it into a NuGet package.

---

### 🔧 `agent` block (Docker)

```groovy
agent {
    docker {
        image 'mcr.microsoft.com/dotnet/sdk:9.0'
    }
}
```

- Runs the pipeline inside the official **.NET 9 SDK Docker image**.
- Ensures a clean, isolated build environment every time.
- Eliminates dependency on the Jenkins agent's system-installed .NET version.

---

### ⚙️ `options` block

```groovy
options {
    timestamps()
}
```

- Adds timestamps to console log output.
- Helps track step durations and improves readability for debugging.

---

### 🌍 `environment` block

```groovy
environment {
    DOTNET_CLI_HOME = "${env.WORKSPACE}/.dotnet"
    DOTNET_CLI_TELEMETRY_OPTOUT = '1'
    DOTNET_SKIP_FIRST_TIME_EXPERIENCE = "1"
}
```

Sets environment variables to:
- Avoid polluting global user folders by setting a local `.dotnet` path.
- Disable telemetry and first-time setup prompts for clean CI builds.

---

### 📦 `stages` block

Each stage represents a step in the build lifecycle:

#### ✅ Stage: Validate .NET SDK

```groovy
stage('Environment Check: Validate .NET SDK') { ... }
```

- Verifies that `dotnet` is installed and available.
- Prints .NET SDK info for traceability.

#### 🔄 Stage: Restore Dependencies

```groovy
stage('Dependency Management: Restore Dependencies') { ... }
```

- Runs `dotnet restore` for your main project.
- Ensures all required NuGet packages are downloaded.

#### 🛠️ Stage: Build the Library

```groovy
stage('Building: Build the Library') { ... }
```

- Compiles the project in `Release` mode.
- Uses `dotnet build` for fast incremental builds.

#### 🧪 Stage: Run Unit Tests

```groovy
stage('Testing: Running Unit Test') { ... }
```

- Executes the test suite using `dotnet test`.
- Useful for catching regressions before packaging.

#### 📦 Stage: Create NuGet Package

```groovy
stage('Packing: Create Nuget Package') { ... }
```

- Packages the library into a `.nupkg` using `dotnet pack`.
- This package can later be published to a NuGet server.

#### 🗃️ Stage: Archive NuGet Package

```groovy
stage('Archive Artifacts: Store Nuget packages') { ... }
```

- Archives all `.nupkg` files created during the build.
- Makes them available as downloadable build artifacts in Jenkins.

---

### 📬 `post` block

```groovy
post {
    failure {
        echo '❌ Build failed. Check the log above for details.'
    }
    success {
        echo '✅ Build succeeded. Artifacts archived.'
    }
}
```

- Adds final logging depending on whether the pipeline **succeeded** or **failed**.
- Helpful for team notifications or debugging.