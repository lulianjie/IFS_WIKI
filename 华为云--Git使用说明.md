
# 1. 华为云相关说明
### 1.1 华为云访问地址
https://dl.devcloud.huaweicloud.com/home ，输入公司账号密码登录
![](assets/image/huawei-01.png)
### 1.2 找到要查看的代码仓
![](assets/image/huawei-02.png)
### 1.3 设置登录账号的SSH密匙
一个账号可以允许多个SSH访问
![](assets/image/huawei-03.png)
![](assets/image/huawei-04.png)
### 1.4 选择具体的代码仓库
选择SSH访问方式，将对应的Git地址复制出来，用于后续步骤克隆（2.3）
本例中地址为：git@codehub-cn-northeast-1.devcloud.huaweicloud.com:NISSAN-TH00001/workspace.git
![](assets/image/huawei-05.png)

# 2.Git客户端软件说明，本例以Sourcetree软件为例介绍
### 2.1 打开软件，启动SSH助手
![](assets/image/huawei-06.png)
### 2.2 任务栏右键PuTTY添加SSH密匙
![](assets/image/huawei-07.png)
***
![](assets/image/huawei-08.png)
***
![](assets/image/huawei-09.png)
***
![](assets/image/huawei-10.png)
### 2.3 克隆Git仓库中代码到本地环境
![](assets/image/huawei-11.png)
***
![](assets/image/huawei-12.png)
***
![](assets/image/huawei-13.png)
### 2.4 修改默认作者，用于区分代码归属
![](assets/image/huawei-14.png)
### 2.5 更多Sourcetree操作请参照其他文章介绍
https://www.cnblogs.com/tian-xie/p/6264104.html
# 3.增量提取变更代码
git diff 61d2112 f3c0f99 --name-only | xargs zip update.zip