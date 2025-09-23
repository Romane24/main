pipeline {
    agent any
    environment {
        // 仓库地址
        FRONT_REPO = 'git@github.com:Romane24/block.git'
        BACK_REPO  = 'git@github.com:Romane24/blockEndfinally.git'
        TEST_REPO  = 'git@github.com:Romane24/----UI.git'

        // 部署目录（同一台机器演示）
        FRONT_DIR = '/www/test-front'
        BACK_DIR  = '/www/test-back'

        // Node 版本（Egg 需要 14+）
        NODE_VERSION = '16'
    }
    stages {
        stage('Checkout-All') {
            steps {
                dir('front') {
                    git branch: 'main', url: FRONT_REPO, credentialsId: 'github-ssh-key'
                }
                dir('back') {
                    git branch: 'main', url: BACK_REPO, credentialsId: 'github-ssh-key'
                }
                dir('test') {
                    git branch: 'main', url: TEST_REPO, credentialsId: 'github-ssh-key'
                }
            }
        }

        /* ===== 前端：React ===== */
        stage('Build-Front') {
            steps {
                dir('front') {
                    sh "npm ci --registry=https://registry.npmmirror.com"
                    sh "npm run build"
                }
            }
        }
        stage('Deploy-Front') {
            steps {
                sh "rm -rf ${FRONT_DIR}/*"
                sh "cp -r front/dist/* ${FRONT_DIR}/"
                echo 'React 部署完成'
            }
        }

        /* ===== 后端：Egg (Node.js) ===== */
        stage('Build-Back') {
            steps {
                dir('back') {
                    sh "npm ci --registry=https://registry.npmmirror.com"
                    sh "npm run build"   // egg-bin build 或 tsc
                }
            }
        }
        stage('Deploy-Back') {
            steps {
                sh 'pkill -f "egg-server" || true'                       // 停旧服务
                sh "rm -rf ${BACK_DIR}/*"
                sh "cp -r back/* ${BACK_DIR}/"                         // 整个项目拷过去
                sh "cd ${BACK_DIR} && npm ci --production"             // 装生产依赖
                sh "cd ${BACK_DIR} && nohup npm start > nohup.out 2>&1 &"  // 启动 Egg
                sleep 8                                                // 等 Egg 初始化
            }
        }

        /* ===== 测试：pytest + selenium ===== */
        stage('Test-E2E') {
            steps {
                dir('test') {
                    sh "python3 -m venv venv && . venv/bin/activate"
                    sh ". venv/bin/activate && pip install -r requirements.txt"
                    sh ". venv/bin/activate && pytest -v --html=report.html --self-contained-html"
                }
            }
        }
    }
    post {
        always {
            publishHTML([
                allowMissing: false,
                alwaysLinkToLastBuild: true,
                keepAll: true,
                reportDir: 'test',
                reportFiles: 'report.html',
                reportName: 'E2E Test Report'
            ])
        }
        success {
            echo '✅ 前端+后端+自动化测试全部通过，可合并！'
        }
        failure {
            echo '❌ 测试失败，请修代码后重新 push。'
        }
    }
}