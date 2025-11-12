pipeline {
    agent any
    
    tools {
        nodejs 'NodeJS-20' // Cần cấu hình NodeJS tool trong Jenkins với tên này
    }
    
    environment {
        NODE_VERSION = '20'
        FRONTEND_DIR = 'frontend'
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Validate Build Artifacts') {
            steps {
                script {
                    dir(env.FRONTEND_DIR) {
                        sh '''
                            for path in node_modules .next; do
                                if git ls-files --error-unmatch "$path" >/dev/null 2>&1; then
                                    echo "ERROR: $path should not be committed. Please add it to .gitignore and remove it from the repository."
                                    exit 1
                                fi
                            done
                        '''
                    }
                }
            }
        }
        
        stage('Install Dependencies') {
            steps {
                dir(env.FRONTEND_DIR) {
                    sh 'npm ci'
                }
            }
        }
        
        stage('Lint') {
            steps {
                dir(env.FRONTEND_DIR) {
                    sh 'npm run lint -- --max-warnings=0'
                }
            }
        }
        
        stage('Build') {
            steps {
                dir(env.FRONTEND_DIR) {
                    sh 'npm run build'
                }
            }
        }
    }
    
    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
        always {
            // Clean up workspace (optional - uncomment if needed)
            // cleanWs()
        }
    }
}

checksInfo {
    name("Build")
    detailsURL("${env.BUILD_URL}console")
    summary("Build completed")
}