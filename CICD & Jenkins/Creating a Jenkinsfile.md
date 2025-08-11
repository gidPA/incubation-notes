A `Jenkinsfile` is a text file that tells Jenkins **how to build, test, and deploy** our code. It’s written in Groovy syntax and stored in our source code repository.

This allows you to treat our build pipeline as **code** (Pipeline-as-Code), making it **version-controlled**, **auditable**, and **repeatable**.

### Typical Jenkinsfile Structure

```groovy
pipeline {
    agent {
        ...
    }

    options {
        ...
    }

    environment {
        ...
    }

    stages {
        stage("Stage Name") {
            steps {
                ...
            }
        }
    }

    post {
        ...
    }
}
```

---
#### `agent`

Defines **where** the pipeline should run.

Examples:

```groovy
agent any          // Run on any available agent
agent none         // Don't run anything unless specified in stages
agent { label 'linux' }  // Run on agents with the 'linux' label
```

Use this to assign which Jenkins node (machine) does the work.

#### `options`

Provides **additional settings** to control the behavior of the pipeline.

Examples:

```groovy
options {
    timeout(time: 10, unit: 'MINUTES')  // Set timeout for the whole pipeline
    buildDiscarder(logRotator(numToKeepStr: '5'))  // Keep only last 5 builds
}
```

These settings are optional but helpful for cleaning up or handling edge cases.

---
#### `environment`

Used to define **environment variables** for the pipeline.

Examples:

```groovy
environment {
    NODE_ENV = 'production'
    DEPLOY_KEY = credentials('my-deploy-key')  // Securely use Jenkins credentials
}
```

Variables defined here can be used in the `steps`.

---

#### `stages`

The **main body** of the pipeline. Each `stage` represents a logical step like Build, Test, or Deploy.

Example:

```groovy
stages {
    stage("Build") {
        steps {
            echo "Building the project..."
            sh "npm install"
        }
    }
    stage("Test") {
        steps {
            echo "Running tests..."
            sh "npm test"
        }
    }
}
```

Each `stage` must contain `steps`, which are the actual commands Jenkins will run.

---

####  `post`

Defines actions that run **after the pipeline (or each stage)** completes — regardless of success or failure.

Example:

```groovy
post {
    success {
        echo 'Build succeeded!'
    }
    failure {
        echo 'Build failed!'
    }
    always {
        echo 'This will always run.'
    }
}
```

This is useful for cleanup, notifications, or sending reports.

### Jenkinsfile Example

An example can be found [[Jenkins Pipeline Example - NET 9 Project Build & Packaging|here]]

