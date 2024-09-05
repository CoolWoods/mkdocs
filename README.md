## mkdocs使用方法
本项目使用mkdocs构建，mkdocs文档地址[mkdocs.org](https://www.mkdocs.org).
## 安装
```bash
pip install mkdocs
```
## 主题
1. 查看 [mkdocs主题](https://github.com/mkdocs/mkdocs/wiki/MkDocs-Themes)
2. 安装主题
```bash
pip install mkdocs-material
```
## 命令
* `mkdocs new [dir-name]` - 创建一个新的项目。
* `mkdocs serve` - 启动实时重新加载文档服务器。
* `mkdocs build` - 建立文档站点。
* `mkdocs -h` - 查看帮助文档信息。

## 配置
```yaml
docs_dir: mkdoc
site_dir: docs
site_name: Min doc
theme:
  name: material
  language: zh
nav:
  - 首页: index.md
  - Markdown语法: Markdown语法.md
```

## 目录及文件说明
1. docs mkdocs构建生成的目录，同时也是github pages的目录。 
2. mkdoc markdown文件所在目录。 
3. mkdocs.yml mkdocs的配置文件，mkdocs根据此配置文件将md文件构建成为站点。
- 注意：mkdocs build生成目录和markdown文档所在目录可通过mkdocs.yaml文件进行配置