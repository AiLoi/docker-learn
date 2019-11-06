#字符串转base64
```
[Convert]::ToBase64String([System.Text.UnicodeEncoding]::Unicode.GetBytes('ailuoli')) 
```
#base64转字符串
```
[System.Text.UnicodeEncoding]::Unicode.GetString([Convert]::FromBase64String('VwBzADkANgAxADIAMgA2AA=='))  
```
#关闭hyperv
`
bcdedit /set hypervisorlaunchtype off
`