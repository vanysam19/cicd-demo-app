pipeline {
    agent any
    parameters {
        string(name: 'REPO_URL', defaultValue: 'https://github.com/vanysam19/cicd-demo-app')
        string(name: 'GIT_BRANCH', defaultValue: 'master')
        booleanParam(name: 'RUN_TESTS', defaultValue: true)
    }
    stages {
        stage('Checkout') {
            steps {
                git url: params.REPO_URL, branch: params.GIT_BRANCH
                script {
                    env.COMMIT_SHA = sh(script: 'git rev-parse HEAD', returnStdout: true).trim()
                }
                echo "Ветка: ${params.GIT_BRANCH}"
                echo "SHA коммита: ${env.COMMIT_SHA}"
            }
        }
        stage('Validate') {
            steps {
                sh '''
                    for f in README.md requirements.txt; do test -f "$f" || { echo "Нет файла $f"; exit 1; }; done
                    for d in src tests; do test -d "$d" || { echo "Нет каталога $d"; exit 1; }; done
                '''
            }
        }
        stage('Test') {
            when { expression { params.RUN_TESTS } }
            steps {
                sh 'python3 -m unittest discover -s tests -v'
            }
        }
        stage('Build') {
            steps {
                sh '''
                    echo "Build number: ${BUILD_NUMBER}" > build-info.txt
                    echo "Commit SHA: ${COMMIT_SHA}" >> build-info.txt
                    tar --exclude=.git --exclude=app.tar.gz -czf app.tar.gz .
                '''
            }
        }
        stage('Archive') {
            steps {
                archiveArtifacts artifacts: 'app.tar.gz'
            }
        }
    }
    post {
        success { echo 'Сборка завершена успешно' }
        failure { echo 'Сборка завершена неуспешно' }
    }
}
