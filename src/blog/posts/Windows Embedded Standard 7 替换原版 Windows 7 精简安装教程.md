---
layout: layouts/post.njk
title: Windows Embedded Standard 7 替换原版 Windows 7 精简安装教程
description: Windows Embedded Standard 7 替换原版 Windows 7 精简安装教程
date: 2026-06-11
---


## 一、前言
目前超级本大多配备SSD固态硬盘，出厂预装 Windows 7 系统，使用过程中存在两大问题：
1. 原版 Win7 初始占用磁盘空间超 11GB，长期使用系统盘空间持续增长，最终导致系统运行缓慢；
2. 原版系统臃肿，即便全新安装，多开网页、办公软件、聊天工具也会出现明显卡顿。

本人设备搭载 24G SSD + Win7 64 位，尝试过多种方案，对比如下：
1. **网友自制精简版 Win7**：体积压缩有限，捆绑广告与插件，安全性差，不推荐；
2. **Windows Thin PC**：微软官方精简版，安装仅占 3GB，但缺失图片查看器、搜索、.NET 框架等功能，扩展组件极易启动失败，配置难度高；
3. **Windows Embedded Standard 7（WES7）**：微软组件化定制系统，基于 Win7 内核，**驱动、软件与原版 Win7 完全兼容**。按需勾选组件，保留常用功能后总占用不超过 3GB，性能、兼容性表现优秀，为最优方案。

## 二、前期准备
### 1. 系统镜像
微软官方 180 天评估版 WES7：
http://www.microsoft.com/en-us/download/details.aspx?id=11887

下载后解压得到约 2.9G ISO 文件。

### 2. 中文语言包（仅下载 SP1 简体中文包）
- 32位：http://www.microsoft.com/en-us/download/details.aspx?id=26215
- 64位：http://www.microsoft.com/en-us/download/details.aspx?id=26216

### 3. 序列号
- 官方试用密钥（180天）：`GJVTR-C4WQ6-BKRH3-DRFFH-J83DM`
- 临时安装密钥：`TJHCC-9DKQT-RB9PJ-YCQPV-3KDJG`

### 4. 安装介质
准备 **4G 及以上 U 盘**，使用 UltraISO 将 ISO 写入 U 盘制作启动盘；虚拟机安装可省略 U 盘。

> 注意：Win7 PE 下硬盘安装 WES7 无法自定义组件，不建议使用。

## 三、系统安装
整体流程：U盘引导安装 → 自定义组件 → 分区安装 → 初始化设置

### 1. 引导与基础设置
1. 从启动 U 盘开机，选择 **Build an Image**（本地安装系统）。
2. 同意许可协议，点击「下一步」。
3. 模板选择：
   - 自定义精简（推荐）：选择 **Do not use a template**（不使用模板）；
   - 懒人模式：选择 `Internet Explorer、Windows Media Player、Remote Desktop` 模板。
4. 语言选项：安装界面无中文，保持默认英文，后续统一汉化，点击「下一步」。
5. 驱动设置：选择 **Automatically detect devices**（自动检测硬件驱动）。
6. 列表中 `unmapped devices` 为未识别驱动设备，系统装好后手动补装即可，继续下一步。

### 2. 自定义功能组件（核心步骤）
建议**全选所有组件，再按需剔除无用项**，兼顾精简与兼容性。

#### 可精简组件清单
| 分类 | 移除项目 | 说明 | 节省空间 |
| ---- | ---- | ---- | ---- |
| Language Packages | 仅保留 en_us | 删除多余语言包 | 800MB |
| application support | MSMQ | 依赖IIS，普通用户无需安装 | 5MB |
| Data Access and Storage | Windows Data Access components - SQL | 移除SQL客户端支持 | 2MB |
| Data integrity | 卷影复制、系统还原 | 不需要系统还原可删除 | 10MB |
| Devices and Printers | 仅保留基础驱动框架 | 精简多余外设组件 | 47MB |
| Diagnostics | 仅保留 Common Diagnostic Tools | 精简诊断工具 | 88MB |
| Embedded Enabling Features | 嵌入式专属功能 | 普通PC无需使用 | 5MB |
| International\IME | 日语、韩语、繁体中文输入法 | 可使用第三方输入法替代 | 105MB |
| Internet Information Services | IIS服务 | 个人用户无需网站服务 | 30MB |
| Management | 家长控制(Parental Control) | 无需求可删除 | 9MB |
| Media Center | 媒体中心 | 占用空间大，日常少用 | 134MB |
| Networking | 仅保留域服务、无线网络 | 精简冗余网络组件 | 1MB |
| Security | 安全中心、微软反恶意软件 | 搭配第三方安全软件即可 | 6MB |
| SKU compliance | 取消勾选 | **必删项**，否则图片查看器等功能失效 | - |
| User Interface\Help | 系统帮助文件 | 基本不会使用 | 16MB |
| User Interface | 语音API、辅助工具、DVD制作工具 | 移除无用附属工具 | 155MB |

#### 必选单项（Exactly One）
1. 启动界面：二选一即可
   - unbranded startup screens
   - windows embedded standard startup screens
2. 启动环境：**选择 windows boot Environment**
3. 系统外壳：**选择 windows explorer shell**（图形桌面，必选）

#### 完成组件配置
1. 点击左下角 **Resolve optional dependencies** 修复组件依赖；
2. 配置完成后系统预估占用约 **2.4GB**；
3. 后续步骤和原版 Win7 一致：选择系统分区、确认配置，等待安装（该步骤耗时较长）。

### 3. 系统初始化
1. 安装完成后新建账户、设置密码；
2. 填入临时密钥：`TJHCC-9DKQT-RB9PJ-YCQPV-3KDJG`；
3. 时区选择：`UTC+08:00 beijing,chongqing`。

## 四、系统中文汉化
所有操作需使用管理员账号执行。

### 1. 启用 Administrator 管理员账户
1. 右键桌面 **Computer** → **Manage**（计算机管理）；
2. 展开 `local users and groups` → `users`；
3. 右键 `Administrator` → **Properties**，取消勾选 `Account is Disabled`；
4. 注销当前账号，使用 Administrator 重新登录。

### 2. 显示桌面图标
桌面右键 → **personalize** → **change desktop icons**，勾选全部图标，确定。

### 3. 开启基础中文支持（文字/输入法）
打开 **Control Panel** → **Clock, Language and region** → **region and language**
1. **Formats**：选择 `Chinese (Simplified, PRC)`
2. **Location**：选择 `China`
3. **Keyboard and language** → **Change keyboards**：默认输入法设为简体中文，删除英文输入法；
4. **Administrative** → **Change system locale**：选择简体中文；
5. 重启电脑，此时系统可正常显示、输入中文（界面仍为英文）。

### 4. 安装语言包（完整界面汉化）
1. 控制面板 → **install or uninstall display languages** → **install display languages**；
2. 选择 `browse computer or network`，选中已下载的简体中文语言包并安装；
3. 将系统显示语言设为简体中文，勾选「欢迎界面使用中文」；
4. 重启系统，界面完全汉化。

> 提示：习惯英文界面可跳过此步。

## 五、系统激活
系统默认试用期 30 天，激活步骤：
1. 输入官方密钥：`GJVTR-C4WQ6-BKRH3-DRFFH-J83DM`；
2. 激活工具下载：http://pan.baidu.com/share/link?shareid=491128&uk=3355891918
3. 使用 **Chew_WGA v0.9** 完成激活，实测可正常激活 WES7。

## 六、总结
WES7 基于 Win7 内核，组件化定制后体积仅 2.4~3GB，完美适配小容量 SSD 设备。软件、驱动兼容性与原版 Win7 一致，运行更流畅，是低配、小固态笔记本替换原版 Win7 的理想方案。