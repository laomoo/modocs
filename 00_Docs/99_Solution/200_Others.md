# 其他问题

## 北京旅游攻略

	添加时间：2018年12月04日

	


## Win10应用无法使用

	添加时间：2018年12月02日

- 打开windows防火墙
- 桌面右键 -> 个性化 -> 任务栏 -> 当我右键单击开始按钮那一行勾选
- 右键单击开始 选Windws Powershell (以管理员方式打开)
- 复制代码：
```bash
Get-AppxPackage -AllUsers| Foreach {Add-AppxPackage -DisableDevelopmentMode -Register "$($_.InstallLocation)\AppXManifest.xml"}
```
- 回车，等待执行完毕即可

