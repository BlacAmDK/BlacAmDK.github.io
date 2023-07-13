# SSL加密算法配置

## 相关资料

RFC文档中有推荐的和不应使用的加密算法(第4节)
[RFC 9142 Key Exchange (KEX) Method Updates and Recommendations for Secure Shell (SSH)](https://www.rfc-editor.org/rfc/rfc9142.html)

SSL配置文件生成器, 可以根据安全级别和应用软件版本生成对应的配置文件
[SSL Configuration Generator SSL Config Generator](https://ssl-config.mozilla.org/)

## 外部扫描

可以使用nmap script

    nmap -p22 <ip> -sC # Send default nmap scripts for SSH
    nmap -p22 <ip> -sV # Retrieve version
    nmap -p22 <ip> --script ssh2-enum-algos # Retrieve supported algorythms 
    nmap -p22 <ip> --script ssh-hostkey --script-args ssh_hostkey=full # Retrieve weak keys
    nmap -p22 <ip> --script ssh-auth-methods --script-args="ssh.user=root" # Check authentication methods

或者metasploit

    msf> use scanner/ssh/ssh_enumusers


tags: #SSL
