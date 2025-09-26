stage('Debug-Front-Structure') {
    steps {
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

stage('Build-Front') {
    steps {
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