# Git

## 快速开始

### Markdown

```bash
git init
# 添加所有文件
git add .
# -m后面是本次提交的说明
git commit -m "first commit"
git branch -M main
# 添加远程仓库
git remote add origin URL
# 新建远程仓库是空的，所以加上-u参数
# 不仅会推送，还会将本地与远程仓库分支关联，后续简化操作
git push -u origin main
```



### 变动追踪

只能追踪有编码的文本，而不能追踪二进制文件

> 例如：word、图片

### 软件更新

* 检查版本

  ```bash
  git --version
  ```

* 更新

  ```bash
  git update
  git update-git-for-windows# 2.17.1后
  ```

  
