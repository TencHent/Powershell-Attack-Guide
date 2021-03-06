# powershell(11)-Powershell与事件日志
在渗透的过程中，我们难免遇到有删除日志的需求，比如我们做了某些操作是必须要进行日志的删除，同时作为系统管理员也是必须掌握日志的操作与备份等等才能在遇到事件后的第一时间定位攻击和修复方案的提出。我们下面来看看Powershell在Windows事件日志中的表现。

### CmdLet
**Powershell Version 2.0**

关于PowershellV2的关于日志的CmdLet有下面的命令，给大家准备了官方的文档，可以自行研究。

* [Clear-EventLog](https://docs.microsoft.com/zh-cn/powershell/module/Microsoft.PowerShell.Management/Clear-EventLog?view=powershell-3.0)
* [Get-EventLog](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.management/get-eventlog?view=powershell-5.1)
* [Get-WinEvent](https://docs.microsoft.com/zh-cn/powershell/module/Microsoft.PowerShell.Diagnostics/Get-WinEvent?view=powershell-3.0)
* [Limit-EventLog](https://docs.microsoft.com/zh-cn/powershell/module/Microsoft.PowerShell.Management/Limit-EventLog?view=powershell-3.0)
* [New-EventLog](https://docs.microsoft.com/zh-cn/powershell/module/Microsoft.PowerShell.Management/New-EventLog?view=powershell-3.0)
* [Remove-EventLog](https://docs.microsoft.com/zh-cn/powershell/module/Microsoft.PowerShell.Management/Remove-EventLog?view=powershell-3.0)
* [Show-EventLog](https://docs.microsoft.com/zh-cn/powershell/module/Microsoft.PowerShell.Management/Show-EventLog?view=powershell-3.0)
* [Write-EventLog](https://docs.microsoft.com/zh-cn/powershell/module/Microsoft.PowerShell.Management/Write-EventLog?view=powershell-3.0)

### 常见的日志操作
下面介绍一下Powershell中常见的事件日志操作
#### 列出事件日志列表
```powershell
Get-Eventlog -List
```

![Alt text](./img/log/1510895065732.png)

#### 查看security日志
```powershell
Get-Eventlog -LogName security
```
![Alt text](./img/log/1510895162316.png)

#### 列出最近日志
```powershell
 Get-EventLog -LogName security -Newest 5
```
![Alt text](./img/log/1510895671895.png)

#### 列出指定时间段内的日志
```powershell
 Get-EventLog -LogName security -After 2017-11-15 -Before 2017-11-17
```
![Alt text](./img/log/1510895914819.png)

#### 根据事件ID列出日志
```powershell
 Get-EventLog -LogName security -InstanceId 4624
```

![Alt text](./img/log/1510896070185.png)
#### 获取某一条事件日志
通过index获取：

```powershell
Get-EventLog -LogName system -Index 32324
```
![Alt text](./img/log/1510925814823.png)

那么当我们获取到一条日志之后我们就把他完全看作是一个对象了，我们直接对其操作即可，下面是查看日志的一些属性的方法。
##### 查看此条日志的一些属性

```powershell
$log = Get-EventLog -LogName system -Index 32324
```

* 类型

```powershell
$log.EntryType

# Warning
```

* 事件ID

```powershell
$log.InstanceId

# 1014
```

* 日志消息

```powershell
$log.Message

# 在没有配置的 DNS 服务器响应之后，名称 teredo.ipv6.microsoft.com 的名称解析超时。
```

* 事件源


```powershell
$log.Source
# Microsoft-Windows-DNS-Client
```

* 日志产生时间

```powershell
$log.TimeGenerated

# 2017年11月17日 21:33:17
```

* 产生日志的用户

```powershell
$log.UserName

# NT AUTHORITY\NETWORK SERVICE
```
#### 删除事件日志
##### Remove-Eventlog
这个cmdlet会注销掉事件源

```powershell
 Remove-EventLog -LogName security
```

仅注销事件源，不删除日志

注销事件源后 app将无法写入事件日志

```powershell
Remove-EventLog -Source app
```
##### Clear-Eventlog
这个cmdlet仅会清除日志

```powershell
Clear-Eventlog -LogName security

# 可以直接远程删除
Clear-Eventlog -LogName security -computername localhost, Server02
```