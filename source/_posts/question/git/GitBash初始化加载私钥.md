---
title: GitBash初始化加载私钥
date: 2019-01-25 10:37:01
---
#### 
    在用户目录C:\Users\user 创建.bashrc 文件，添加所有秘钥
    eval "$(ssh-agent -s)"
    ssh-add ~/.ssh/id_rsa1
    ssh-add ~/.ssh/id_rsa2
    ssh-add ~/.ssh/id_rsa3