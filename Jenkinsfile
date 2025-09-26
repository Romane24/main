pipeline {
    agent any
    
    stages {
        stage('Checkout Main Repo') {
            steps {
                checkout scm
            }
        }
        
        stage('Checkout-All') {
            steps {
                script {
                    // 前端仓库
                    dir('front') {
                        git url: 'git@github.com:Romane24/block.git', 
                            credentialsId: 'github-ssh-key', 
                            branch: 'main'
                    }
                    // 后端仓库
                    dir('back') {
                        git url: 'git@github.com:Romane24/blockEndfinally.git', 
                            credentialsId: 'github-ssh-key',
                            branch: 'main'
                    }
                    // 测试仓库
                    dir('test') {
                        git url: 'git@github.com:Romane24/----UI.git', 
                            credentialsId: 'github-ssh-key',
                            branch: 'main'
                    }
                }
            }
        }
        
        stage('Debug-Front-Structure') {
            steps {
                script {
                    dir('front') {
                        sh '''
                            echo "=== 前端仓库结构 ==="
                            pwd
                            ls -la
                            
                            echo "=== 查找项目目录 ==="
                            find . -name "package.json" -type f
                            
                            if [ -d "block/myBlock/react-vite-smyblock" ]; then
                                echo "✅ 找到项目目录"
                                cd block/myBlock/react-vite-smyblock
                                pwd
                                ls -la
                            else
                                echo "❌ 项目目录不存在，当前结构："
                                find . -type d | head -20
                            fi
                        '''
                    }
                }
            }
        }
        
        stage('Build-Front') {
            steps {
                script {
                    dir('front/block/myBlock/react-vite-smyblock') {
                        sh '''
                            echo "=== 开始构建前端 ==="
                            echo "当前目录:"
                            pwd
                            echo "文件列表:"
                            ls -la
                            
                            echo "安装依赖..."
                            npm install --registry=https://registry.npmmirror.com
                            
                            echo "执行构建..."
                            npm run build
                            
                            echo "✅ 前端构建完成"
                            ls -la dist/
                        '''
                    }
                }
            }
        }
        
        stage('Deploy-Front') {
            steps {
                script {
                    dir('front/block/myBlock/react-vite-smyblock') {
                        sh '''
                            echo "=== 部署前端 ==="
                            echo "部署静态资源到服务器..."
                            # 这里添加您的部署命令，例如：
                            # rsync -av dist/ user@server:/path/to/www/
                            # 或使用 scp, s3 等
                            echo "✅ 前端部署完成"
                        '''
                    }
                }
            }
        }
        
        stage('Build-Back') {
            steps {
                script {
                    dir('back') {
                        sh '''
                            echo "=== 构建后端 ==="
                            pwd
                            ls -la
                            
                            # 根据您的后端技术栈添加构建命令
                            # 例如 Maven: mvn clean package
                            # 例如 Gradle: ./gradlew build
                            # 例如 Node.js: npm install && npm run build
                            echo "后端构建步骤待配置"
                        '''
                    }
                }
            }
        }
        
        stage('Deploy-Back') {
            steps {
                script {
                    dir('back') {
                        sh '''
                            echo "=== 部署后端 ==="
                            # 这里添加后端部署命令
                            # 例如：docker build, kubectl apply, 服务器部署等
                            echo "后端部署步骤待配置"
                        '''
                    }
                }
            }
        }
        
        stage('Test-E2E') {
            steps {
                script {
                    dir('test') {
                        sh '''
                            echo "=== 执行 E2E 测试 ==="
                            pwd
                            ls -la
                            
                            # 这里添加测试命令
                            # 例如：npm test, ./run_tests.sh 等
                            echo "E2E 测试步骤待配置"
                        '''
                    }
                }
            }
            post {
                always {
                    publishHTML([
                        allowMissing: true,
                        alwaysLinkToLastBuild: true,
                        keepAll: true,
                        reportDir: 'test/reports',
                        reportFiles: 'index.html',
                        reportName: 'E2E Test Report',
                        reportTitles: 'E2E 测试报告'
                    ])
                }
            }
        }
    }
    
    post {
        always {
            echo "🚀 流水线执行完成 - 状态: ${currentBuild.result ?: 'SUCCESS'}"
        }
        success {
            echo "✅ 流水线执行成功！"
            // 可以添加成功通知，如邮件、钉钉等
        }
        failure {
            echo "❌ 测试失败，请修复代码后重新 push。"
            // 可以添加失败通知
        }
        unstable {
            echo "⚠️ 流水线执行不稳定"
        }
    }
}