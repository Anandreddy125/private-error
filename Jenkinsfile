pipeline {
    agent any

    environment {
        IMAGE_NAME = "prophazedevops/dev-tech"
        VERSION_FILE = "version.txt"
        HISTORY_FILE = "version_history.txt"
        DOCKER_CREDENTIALS_ID = "docker"
        GIT_CREDENTIALS_ID = "github-terra"
        KUBERNETES_CREDENTIALS_ID = "k3s-secrets"
    }

    stages {
        stage('Initialize Version') {
            steps {
                script {
                    // Ensure the latest commit is fetched
                    sh 'git fetch --all'
                    if (fileExists(VERSION_FILE)) {
                        STABLE_VERSION = sh(script: "cat ${VERSION_FILE}", returnStdout: true).trim()
                    } else {
                        STABLE_VERSION = "1.0.0"
                    }
                    def parts = STABLE_VERSION.tokenize('.')
                    def major = parts[0].toInteger()
                    def minor = parts[1].toInteger()
                    def patchParts = parts[2].tokenize('-')
                    def patch = patchParts[0].toInteger()
                    // Increment patch, reset to 0 if it reaches 10, and increment minor
                    patch++
                    if (patch > 9) {
                        patch = 0
                        minor++
                    }
                    // If minor exceeds 9, reset and increment major
                    if (minor > 9) {
                        minor = 0
                        major++
                    }
                    // Construct new version without commit ID initially
                    BUILD_VERSION = "${major}.${minor}.${patch}"
                    echo "Next build version: ${BUILD_VERSION}"
                }
            }
        }
        stage('Clean Workspace') {
            steps {
                cleanWs()  // This clears the workspace before each build
            }
        }

        stage('Checkout Code') {
            steps {
                git branch: 'main', 
                    credentialsId: "${GIT_CREDENTIALS_ID}", 
                    url: 'https://github.com/Anandreddy125/private-error.git'
            }
        }
            stage('Fetch Commit ID') {
            steps {
                script {
                    // Fetch the latest commit ID after checkout
                    COMMIT_ID = sh(script: "git rev-parse --short HEAD", returnStdout: true).trim()
                    // Append commit ID to the build version
                    BUILD_VERSION = "${BUILD_VERSION}-commit${COMMIT_ID}"
                    echo "Final build version with commit ID: ${BUILD_VERSION}"
                }
            }
        }
    }
}
