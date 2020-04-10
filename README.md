# 使用 Vue + LeanCloud 的周报系统

> **<span style="color:#da3218">deprecated</span>**:  
> 这是一个小白入门半年时候写的代码，已不建议直接使用。  
> 不过可以当做一个参考，基于 LeanCloud 一个不会一点后端的前端也可以做出一个完整产品来。

[![GitHub issues](https://img.shields.io/github/issues/cdswyda/weekly-report.svg)](https://github.com/cdswyda/weekly-report/issues)
[![GitHub pull requests](https://img.shields.io/github/issues-pr/cdswyda/weekly-report.svg)](https://github.com/cdswyda/weekly-report/pulls)
[![GitHub forks](https://img.shields.io/github/forks/cdswyda/weekly-report.svg)](https://github.com/cdswyda/weekly-report/network)
[![GitHub stars](https://img.shields.io/github/stars/cdswyda/weekly-report.svg)](https://github.com/cdswyda/weekly-report/stargazers)
[![GitHub license](https://img.shields.io/github/license/cdswyda/weekly-report.svg)](https://github.com/cdswyda/weekly-report/blob/master/LICENSE)
[![Twitter](https://img.shields.io/twitter/url/https/github.com/cdswyda/weekly-report.svg?style=social)](https://twitter.com/intent/tweet?text=Wow:&url=https%3A%2F%2Fgithub.com%2Fcdswyda%2Fweekly-report)

使用 Vue + LeanCloud 开发的一个周报系统，纯前端实现。

并利用 LeanCloud 提供的云引擎服务实现在周五给全员发送邮件提醒填写周报，周六周日分别再次对未填人员发送邮件进行填写提醒。

## Changelog

- **2019-07-16**
  - 更新一些依赖库版本
  - 暂时移除 [pushbear](http://pushbear.ftqq.com/)
  - 更新后问题修复 [7237](https://github.com/vuejs/vue/issues/7237)
- **2019-02-21**
  新增 [pushbear](http://pushbear.ftqq.com/) 微信推送填写提醒。（广播通知，通知全部订阅者） 云函数仓库 [https://github.com/cdswyda/weekly-report-pushbear](https://github.com/cdswyda/weekly-report-pushbear)

  请修改 [pushbear.config-example.js](./src/config/pushbear.config-example.js) 文件，并重命名为 pushbear.config.js 即可
- **2018-09-03**
  - 新增用户删除功能
  - 邮件发送迁移到云函数，详情参看云函数仓库
- **2018-08-15** 重置密码页 [#3]
- **2018-08-13**
  - 完成[防止其他人误注册，注册后加入管理员校验，通过后才能访问系统 #12](https://github.com/cdswyda/weekly-report/issues/12)，**需要同步更新**一些内容，详见issues。
  - 开启打包的gzip支持。
- **2018-08-12**
  - 允许管理员直接给为用户发送密码重置邮件
  - 新增错误提醒
  - 注册后的用户无法直接登录，需要团队管理员验证
- **2018-04-24** 修复本周汇总页面下可能的报错现象，且人员选择打开无效 [#10](https://github.com/cdswyda/weekly-report/issues/10)
- **2018-04-21** 初始化小组配置优化 [#8](https://github.com/cdswyda/weekly-report/issues/8)
- **2018-04-21** 修复登录页面切换小组时，若小组内暂时没有成员，报错的问题。 [#9](https://github.com/cdswyda/weekly-report/issues/9)

## Todo

- ~~防止其他人误注册，注册后加入管理员校验，通过后才能访问系统 [#12](https://github.com/cdswyda/weekly-report/issues/12)~~
- ~~重置密码页 [#3](https://github.com/cdswyda/weekly-report/issues/3)~~
- 小组管理 [#4](https://github.com/cdswyda/weekly-report/issues/4)
- ~~成员删除 [#6](https://github.com/cdswyda/weekly-report/issues/6)~~
- ~~错误信息提示 [#5](https://github.com/cdswyda/weekly-report/issues/5)~~
- ~~初始化小组配置优化 [#8](https://github.com/cdswyda/weekly-report/issues/8)~~

## 基本配置

**相关仓库**

云函数仓库：[https://github.com/cdswyda/weekly-report-avcloud](https://github.com/cdswyda/weekly-report-avcloud)

**LeanCloud 应用配置**

前往 [LeanCloud](https://leancloud.cn/) 或 [LeanCloud 国际版](https://leancloud.app) 新增应用。 并导入 `/appSchema/` 下的 schema

修改 `src/config/av.config-example.js` 文件，填入 LeanCloud 应用的 **App ID**、**App key**、**服务器地址**。

此 `id`、`key`、`url` 可以从 [LeanCloud](https://leancloud.cn/)  `要关联的应用 => 设置 => 应用 Key` 中获取。

```js
// 填写配置后重命名此文件为av.config.js
export default {
  id: '填写LeanCloud应用的ID',
  key: '填写LeanCloud应用的Key',
  url: '填写 leancloud 的 REST API 服务器地址'
}
```

LeanCloud 国际版应用不强制要求绑定自有域名，如果使用 LeanCloud 国际版应用，`url` 可以留空（`url: ''`）。

**周报配置**

可以从 `src/config/input.config.js` 和  `src/config/group.config.js` 中配置周报填写的类型、说明以及小组配置，格式相应参见文件即可。

- `input.config.js` 中的配置信息，用于配置输入页面中存在的不同类型和相对应的提示，以及每周的基础工时、计算为任务饱和度的关联任务等。

- `group.config.js` 中配置的小组信息，将在首个成员注册时自动写入到 LeanClound 应用中。

> 规划时，计算任务饱和度是单独配置的，但实际开发中，这块耦合住了，在考虑优化掉，做成一个通用的产品，如果你有任何想法，可以联系我，谢谢。

**发送邮件配置**

修改 `mail/mailer-example.php` 文件，配置完成后重命名为 `mailer.php` 即可

```php
public static $HOST = 'smtp.163.com'; // 邮箱的服务器地址
public static $PORT = 465; // smtp 服务器的远程服务器端口号
public static $SMTP = 'ssl'; // 使用 ssl 加密方式登录
public static $CHARSET = 'UTF-8'; // 设置发送的邮件的编码

/**
 * 配置此处信息后将此文件重命名为mailer.php即可
 */
private static $USERNAME = '配置用户'; // 授权登录的账号
private static $PASSWORD = '配置授权密码'; // 授权登录的密码
private static $NICKNAME = '新点前端周报'; // 发件人的昵称
```

无需邮件服务器，直接使用各个邮箱的 SMTP 服务即可完成。

这里发送邮件的实现是使用了 [PHPMailer](https://github.com/PHPMailer/PHPMailer) 简单包装来实现的。

注：

- 不要使用 QQ 邮箱的 SMTP ，我在使用中，团队30+人， 单独发送到10+后，之后的都失败了，提醒发送邮件过快。使用163邮箱的暂无问题。
- why php?
  - 因为目前没在服务器上装有nodejs。
  - 虽然 LeanCloud 提供的免费云引擎，本身就支持nodejs服务，但是免费版是做测试用的，会自动休眠，不够稳定，经常挂掉。
  - 若使用nodejs，可使用 [nodemailer](https://github.com/nodemailer/nodemailer) 来发送邮件。

以上展示了配置发送邮件的功能，还需要定时查找用户或未提交的用户来发送邮件。

此处使用了 LeanCloud 提供的云引擎中的定时任务来实现。

1. 定义云函数，以便发送邮件。实现可参考 [weekly-report-avcloud](https://github.com/cdswyda/weekly-report-avcloud)
2. 在 LeanCloud 的应用中 点击 `云引擎 => 定时任务` 来创建定时任务，定时执行发送邮件。

![http://qiniu.cdswyda.com/images/201802/timing-task.png](http://qiniu.cdswyda.com/images/201802/timing-task.png)

相关文档可参考 [LeanCloud 开发指南](https://leancloud.cn/docs/leanengine_cloudfunction_guide-node.html)

## 构建使用步骤

此项目直接使用 **Vue-cli** 工具初始化，配置进行了略微修改，相关命令如下：

```bash
# install dependencies
npm ci

# serve with hot reload at localhost:8086
npm run dev

# build for production with minification
npm run build

# build for production and view the bundle analyzer report
npm run build --report
```

关于打包后的部署使用，请根据要放的目录，自行调整 `/config/index.js` 中的
`assetsPublicPath` 路径，并将打包生成的文件（默认在 `/dist/` 下）全部拷贝到你指定目录下即可。

```js
// 例如：这里最后期望通过访问 域名/weeklyreport/ 访问此周报系统，则配置为/weeklyreport/即可
assetsPublicPath: '/weeklyreport/',
```

## 效果展示

填写

![填写 https://qiniu.cdswyda.com/images/201802/input-show.gif](https://qiniu.cdswyda.com/images/201802/input-show.gif)

汇总展示

![汇总展示 https://qiniu.cdswyda.com/images/201802/summary.png](https://qiniu.cdswyda.com/images/201802/summary.png)

只想看你关心的？这里有！

![只想看你关心的？这里有！](https://qiniu.cdswyda.com/images/201802/person-select.png)

汇总图表

![汇总图表 https://qiniu.cdswyda.com/images/201802/summary-echarts.png](https://qiniu.cdswyda.com/images/201802/summary-echarts.png)

还支持任意时段的历史查看，下方表格和图标的展示同周汇总。

![任意时间段汇总 https://qiniu.cdswyda.com/images/201802/summary-all.png](https://qiniu.cdswyda.com/images/201802/summary-all.png)

个人信息维护

![个人信息维护 https://qiniu.cdswyda.com/images/201802/person-info.png](https://qiniu.cdswyda.com/images/201802/person-info.png)

管理员对成员查看和管理

![管理员对成员查看和管理 https://qiniu.cdswyda.com/images/201802/person-manage.png](https://qiniu.cdswyda.com/images/201802/person-manage.png)

对了，还可以导出表格为csv



```bash
# 忽略本地修改过的配置文件
git update-index --assume-unchanged src\config\av.config.js
git update-index --assume-unchanged src\config\pushbear.config.js
```
