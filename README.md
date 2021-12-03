# Hexo Blog
基于 `hexo` ( `fluid` 主题)搭建的个人博客

## clone项目
```
git clone git@github.com:Van-Yo/Van-Yo.github.io.git
```

## 运行项目
### 安装依赖包
```
// 建议不要直接使用 cnpm 安装依赖，会有各种诡异的 bug。可以通过如下操作解决 npm 下载速度慢的问题
npm install --registry=https://registry.npm.taobao.org

npm install
```

### 生成静态资源
```
hexo g
// hexo generate
```

### 手动获取静态资源（图片）
切换到 `master` 分支，将 `img` 文件夹全部拷贝出来，再切换到 `resources` 分支，将拷贝的 `img` 文件夹替换掉 `/public` 中的 `img` 文件夹

### 启动服务预览
```
hexo s
// hexo server
```
在 `http://localhost:4000` 预览

## 新建博客并部署
### 创建新博客
```
hexo n "博客名称"
// hexo new "博客名称"
```

### 生成静态资源
```
hexo g
// hexo generate
```

### 部署
会自动部署到 `github` 上( `master` 分支)，可在 `_config.yml` 修改部署地址
```
hexo d
// hexo deploy
```
需要过十几秒博客才能更新

## 清除缓存（出现bug使用，慎用）
```
hexo clean
```

注意：全程无需修改本地 `master` 分支中的文件，只需要拉远程 `origin/master` 分支内容即可