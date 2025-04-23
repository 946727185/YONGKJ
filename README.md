# YONGKJ
BPB面板混淆
Github部署
1. 新建github仓库:把BPB panel项目代码同步到仓库。

2. 配置github Actions: 在仓库目录下创建.github/workflows文件夹，并创建Obfuscate.yml文件。2025.4.18更新可使用！

name: Build Obfuscate BPB Panel

on:
  push:
    branches:
      - main
  schedule:
    - cron: "0 1 * * *"

permissions:
  contents: write

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Check out the code
        uses: actions/checkout@v4

      - name: Set up Node.js
        uses: actions/setup-node@v4
        with:
          node-version: "latest"

      - name: Install dependencies
        run: |
          npm install -g javascript-obfuscator
          sudo apt-get install -y unzip

      - name: Download and extract latest BPB worker
        run: |
          wget https://github.com/bia-pain-bache/BPB-Worker-Panel/releases/latest/download/unobfuscated-worker.zip
          unzip -o unobfuscated-worker.zip
          mv _worker.js origin.js

      - name: Obfuscate BPB worker js
        run: |
          javascript-obfuscator origin.js --output _worker.js \
          --compact true \
          --identifier-names-generator hexadecimal \
          --rename-globals true \
          --string-array true \
          --string-array-encoding 'base64' \
          --string-array-threshold 0.75 \
          --transform-object-keys true \
          --self-defending false \
          --simplify true

      - name: 提交更改
        uses: stefanzweifel/git-auto-commit-action@v5
        with:
          branch: main
          commit_message: ':arrow_up: update latest bpb panel'
          commit_author: 'github-actions[bot] <github-actions[bot]@users.noreply.github.com>'
          push_options: '--set-upstream'
Copy
Github仓库通过Obfuscate.yml自动下载BPB源代码，并执行混淆。

Cloudflare 部署
•创建pages：点击workers和pages，选择pages部署。连接github仓库，选择新建的项目仓库，然后点击部署。
•绑定自定义域名：以防止page分配的域名被屏蔽。
•设置变量：UUID，PROXYIP, TR_PASS
•绑定KV命名空间：名称随便但不能含有bpb等敏感词
•重试部署pages
BPB面板设置
•部署成功后，打开浏览器输入:https://[自定义域名]或者你的项目地址,后面加上/panel检查是否能正常访问BPB面板.
•修改BPB面板密码
•配置BPB面板参数
常用IP获取方式
cleanIP/优选IP：地址1  地址2  地址3  地址4
PROXYIP：点击进入1  点击进入2
