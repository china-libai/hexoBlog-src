---
title: 弱口令检测工具fenghuangscanner的安装与使用
date: 2017-05-16 21:38:53
tags:
---
### 下载地址 : [https://github.com/wilson9x1/fenghuangscanner](https://github.com/wilson9x1/fenghuangscanner)

```bash
cd fenghuangscanner
pip install -r requirements.txt         # 安装依赖包
python fenghuangscanner.py -h           # 运行

# 在安装依赖包的过程中可能会报错。  主要是pymssql这个包
brew install freetds
pip install cython

# 如果仍然不行，使用下面命令安装最新的pymssql
pip install git+https://github.com/pymssql/pymssql.git

# Because 2.2.0 still hasn't made it to PyPI as of this comment, the following command from @bkanuka still works and installed smoothly without a single error:
# pip install git+https://github.com/pymssql/pymssql.git
```

问题参考   [https://github.com/Homebrew/homebrew-python/issues/338](https://github.com/Homebrew/homebrew-python/issues/338)

### 使用
```bash
python fenghuangscan.py --ip 192.168.199.0/24
```
主机存活扫描 --> 端口扫描 --> 弱口令爆破