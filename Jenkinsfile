pipeline {
    agent any
    
    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }
        
        stage('Checkout-All') {
            steps {
                script {
                    // 前端仓库
                    dir('front') {
                        git url: 'git@github.com:Romane24/block.git', credentialsId: 'github-ssh-key'
                    }
                    // 后端仓库
                    dir('back') {
                        git url: 'git@github.com:Romane24/blockEndfinally.git', credentialsId: 'github-ssh-key'
                    }
                    // 测试仓库
                    dir('test') {
                        git url: 'git@github.com:Romane24/----UI.git', credentialsId: 'github-ssh-key'
                    }
                }
            }
        }
        
        stage('Debug-Front-Structure') {
            steps {
                script {
                    dir('/var/jenkins_home/workspace/UI测试CICD/front') {
                        sh '''
                            echo "=== 前端仓库根目录结构 ==="
                            pwd
                            ls -la
                            
                            echo "=== 查找 block 目录 ==="
                            find . -name "block" -type d
                            
                            echo "=== 递归查看完整结构 ==="
                            find . -type f -name "package.json" || echo "未找到 package.json"
                            find . -type f -name "vite.config.*" || echo "未找到 Vite 配置文件"
                            
                            echo "=== 检查目标路径是否存在 ==="
                            if [ -d "block/myBlock/react-vite-smyblock" ]; then
                                echo "✅ 目标路径存在"
                                ls -la block/myBlock/react-vite-smyblock/
                            else
                                echo "❌ 目标路径不存在"
                                echo "当前存在的目录:"
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
                    dir('/var/jenkins_home/workspace/UI测试CICD/front/block/myBlock/react-vite-smyblock') {
                        sh '''
                            echo "当前构建目录:"
                            pwd
                            echo "文件列表:"
                            ls -la
                            
                            echo "开始安装依赖..."
                            npm install --registry=https://registry.npmmirror.com
                            
                            echo "开始构建..."
                            npm run build
                        '''
                    }
                }
            }
        }
        
        stage('Deploy-Front') {
            steps {
                script {
                    dir('/var/jenkins_home/workspace/UI测试CICD/front/block/myBlock/react-vite-smyblock') {
                        sh '''
                            echo "部署前端静态资源"
                            ls -la dist/
                        '''
                    }
                }
            }
        }
        
        stage('Build-Back') {
            steps {
                script {
                    dir('/var/jenkins_home/workspace/UI测试CICD/back') {
                        sh '''
                            echo "构建后端项目"
                            # 这里添加后端构建命令
                        '''
                    }
                }
            }
        }
        
        stage('Deploy-Back') {
            steps {
                script {
                    dir('/var/jenkins_home/workspace/UI测试CICD/back') {
                        sh '''
                            echo "部署后端服务"
                            # 这里添加后端部署命令
                        '''
                    }
                }
            }
        }
        
        stage('Test-E2E') {
            steps {
                script {
                    dir('/var/jenkins_home/workspace/UI测试CICD/test') {
                        sh '''
                            echo "运行 E2E 测试"
                            # 这里添加测试命令
                        '''
                    }
                }
            }
        }
    }
    
    post {
        always {
            publishHTML([
                allowMissing: false,
                alwaysLinkToLastBuild: false,
                keepAll: true,
                reportDir: 'test',
                reportFiles: 'index.html',
                reportName: 'E2E Test Report',
                reportTitles: ''
            ])
            echo "流水线执行完成"
        }
        failure {
            echo "❌ 测试失败，请修代码后重新 push。"
        }
        success {
            echo "✅ 流水线执行成功！"
        }
    }
}