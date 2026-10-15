# 致谢与维护说明

本仓库 fork 自 [233boy/sing-box](https://github.com/233boy/sing-box)，谨向原作者及所有贡献者致以诚挚的敬意与感谢。为便于独立维护，我们对脚本安装源、更新源及问题反馈链接进行了必要替换，使其指向本仓库；原作者署名、名称、配置目录及相关帮助文档链接等均保持不变。

本仓库最初的维护目标，是排查并改善极低配置主机在创建配置时出现的资源紧张、系统响应迟缓，甚至 SSH 连接中断等问题，让有限资源环境下的配置管理更加轻量、稳定。相关优化基于代码排查与测试逐步推进，不代表所有断连问题都由同一原因引起，也不保证脚本可在任意资源限制下稳定运行。

# 介绍

最好用的 sing-box 一键安装脚本 & 管理脚本

# 特点

- 快速安装
- 无敌好用
- 零学习成本
- 自动化 TLS
- 简化所有流程
- 兼容 sing-box 命令
- 强大的快捷参数
- 支持所有常用协议
- 一键添加 VLESS-REALITY (默认)
- 一键添加 TUIC
- 一键添加 Trojan
- 一键添加 Hysteria2
- 一键添加 AnyTLS
- 一键添加 Shadowsocks 2022
- 一键添加 VMess-(TCP/HTTP/QUIC)
- 一键添加 VMess-(WS/H2/HTTPUpgrade)-TLS
- 一键添加 VLESS-(WS/H2/HTTPUpgrade)-TLS
- 一键添加 Trojan-(WS/H2/HTTPUpgrade)-TLS
- 一键启用 BBR
- 一键更改伪装网站
- 一键更改 (端口/UUID/密码/域名/路径/加密方式/SNI/等...)
- 还有更多...

# 设计理念

设计理念为：**高效率，超快速，极易用**

脚本基于作者的自身使用需求，以 **多配置同时运行** 为核心设计

并且专门优化了，添加、更改、查看、删除、这四项常用功能

你只需要一条命令即可完成 添加、更改、查看、删除、等操作

例如，添加一个配置仅需不到 1 秒！瞬间完成添加！其他操作亦是如此！

脚本的参数非常高效率并且超级易用，请掌握参数的使用

# 文档

本仓库由 xy4xf 独立维护，基于 233boy 的原始脚本，维护分支为 `personal/main`。

在支持的 Linux 服务器上以 root 用户运行：

```bash
curl -fL https://raw.githubusercontent.com/xy4xf/sing-box/personal/main/install.sh -o install.sh
bash install.sh
```

安装及脚本更新均从 [本仓库的 Releases](https://github.com/xy4xf/sing-box/releases) 下载 `code.tar.gz`，不会下载原作者仓库的脚本。使用前需确认本仓库已成功发布包含该文件的 Release；现有 GitHub Actions 工作流会在推送后按 `is_sh_ver` 发布。后续发布新版本时需递增 `sing-box.sh` 中的 `is_sh_ver`，以便已安装的脚本检测到更新。

sing-box 内核、Caddy 和 jq 仍从各自的官方仓库下载。原作者署名、配置目录及外部帮助链接保留不变。

原作者使用文档（仅供参考，请勿使用其中指向原仓库的安装命令）：https://233boy.com/sing-box/sing-box-script/

# 低内存主机排查

本维护版消除了无 `shuf` 环境下的死循环，减少创建配置时的并行重启，并取消启动失败后的自动重试及额外前台内核进程。`sing-box test` 现在仅显示服务状态和最近日志，不再尝试启动服务。512 MB Ubuntu 主机的 SSH 断连仍需结合现场日志判断，不能仅凭内存容量认定为 OOM。

出现问题后，可通过云厂商控制台或恢复后的 SSH 会话执行：

```bash
free -h
swapon --show
sudo journalctl -k -b --no-pager | grep -Ei 'out of memory|oom|killed process'
sudo journalctl -u systemd-oomd -b -n 80 --no-pager
sudo journalctl -u sing-box -b -n 80 --no-pager
sudo systemctl show sing-box -p Result -p NRestarts -p MemoryCurrent -p MemoryPeak
```

如果已经重启主机，可尝试将 `-b` 改为 `-b -1` 查看上一轮启动日志（需要系统保留历史日志）。脚本不会自动调整 swap、关闭 OOM 防护或修改 SSH 服务。

已安装原版脚本的服务器，其更新命令仍指向原仓库。确认本仓库的 `v1.19` 或更新 Release 发布成功后，可用以下命令仅替换管理脚本，保留现有配置与内核；以 root 用户执行，建议先备份 `/etc/sing-box/sh`：

```bash
archive=$(mktemp)
curl -fL https://github.com/xy4xf/sing-box/releases/latest/download/code.tar.gz -o "$archive" &&
    tar -xzf "$archive" -C /etc/sing-box/sh
rm -f "$archive"
sing-box version
```

# 帮助

使用：`sing-box help`

```
sing-box script v1.0 by 233boy
Usage: sing-box [options]... [args]...

基本:
   v, version                                      显示当前版本
   ip                                              返回当前主机的 IP
   pbk                                             同等于 sing-box generate reality-keypair
   get-port                                        返回一个可用的端口
   ss2022                                          返回一个可用于 Shadowsocks 2022 的密码

一般:
   a, add [protocol] [args... | auto]              添加配置
   c, change [name] [option] [args... | auto]      更改配置
   d, del [name]                                   删除配置**
   i, info [name]                                  查看配置
   qr [name]                                       二维码信息
   url [name]                                      URL 信息
   log                                             查看日志
更改:
   full [name] [...]                               更改多个参数
   id [name] [uuid | auto]                         更改 UUID
   host [name] [domain]                            更改域名
   port [name] [port | auto]                       更改端口
   path [name] [path | auto]                       更改路径
   passwd [name] [password | auto]                 更改密码
   key [name] [Private key | auto] [Public key]    更改密钥
   method [name] [method | auto]                   更改加密方式
   sni [name] [ ip | domain]                       更改 serverName
   new [name] [...]                                更改协议
   web [name] [domain]                             更改伪装网站

进阶:
   dns [...]                                       设置 DNS
   dd, ddel [name...]                              删除多个配置**
   fix [name]                                      修复一个配置
   fix-all                                         修复全部配置
   fix-caddyfile                                   修复 Caddyfile
   fix-config.json                                 修复 config.json
   import                                          导入 sing-box/v2ray 脚本配置

管理:
   un, uninstall                                   卸载
   u, update [core | sh | caddy] [ver]             更新
   U, update.sh                                    更新脚本
   s, status                                       运行状态
   start, stop, restart [caddy]                    启动, 停止, 重启
   t, test                                         查看服务状态和日志（不启动进程）
   reinstall                                       重装脚本

测试:
   debug [name]                                    显示一些 debug 信息, 仅供参考
   gen [...]                                       同等于 add, 但只显示 JSON 内容, 不创建文件, 测试使用
   no-auto-tls [...]                               同等于 add, 但禁止自动配置 TLS, 可用于 *TLS 相关协议
其他:
   bbr                                             启用 BBR, 如果支持
   bin [...]                                       运行 sing-box 命令, 例如: sing-box bin help
   [...] [...]                                     兼容绝大多数的 sing-box 命令, 例如: sing-box generate uuid
   h, help                                         显示此帮助界面

谨慎使用 del, ddel, 此选项会直接删除配置; 无需确认
反馈问题) https://github.com/xy4xf/sing-box/issues
文档(doc) https://233boy.com/sing-box/sing-box-script/
```
