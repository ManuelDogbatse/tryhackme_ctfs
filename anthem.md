# Anthem

## Task 1

### 1.1

Regular nmap using '-sS -sV' doesn't work, so must use ICMP bypass scan '-Pn'.

```
sudo nmap -Pn -sV -p- 10.10.105.125 -v -oN nmap.out
```

Two ports open.

### 1.2

HTTP service on port 80

### 1.3

ms-wbt-server (RDP) service on port 3389

### 1.4

Web crawlers look at the 'robots.txt' file in websites. In the robots.txt file, this appears:

![alt text](images/anthem/image.png)

'UmbracoIsTheBest!' is a potential password

### 1.5

CMS means Content Management System, which is software that helps manage content on a website. Searching 'Umbraco' in the robots.txt file on Google comes up with the Umbraco CMS, meaning that the CMS is Umbraco

### 1.6

Will run gobuster to find the domain.

```
gobuster dir -u http://10.10.105.125:80/ -w ~/wordlists/dirbuster/directory-list-2.3-medium.txt -t 100 -o gb_root.out
```

The main page says 'anthem.com', which directs to the main page. Therefore 'anthem.com' is the domain.

### 1.7

Clicked on second article named 'A cheers to our IT department'. Author wrote a poem about the admin. The admin's name hasn't been mentioned, however will copy and paste poem into Google to see if there is a name attached to it.

Google search comes up with Solomon Grundy, which is the admin's name.

### 1.8

On the first article named 'We are hiring', the author, Jane Doe has written her email, which is 'JD@anthem.com'. Going by this naming convention of [Initials]@anthem.com, the admin's email would be 'SG@anthem.com'

## Task 2

### 2.1

Will inspect the pages that I have access to and the gobuster directories that have been found.

'/umbraco' leads to a login page. Will be useful for gaining access to the system.

Looking at the meta tag of 'We are hiring', a flag is found:

THM{L0L_WH0_US3S_M3T4}

### 2.2

The hint says that the flag needs to be searched for.

Looking at page source code, a flag can be found in the placeholder for the search bar:

THM{G!T_G00D}

### 2.3

One interesting directory is '/sitemap' which could lead to more directories.

It shows both '/archive and '/authors', which could show potential flags.

'/archive' redirects to '/blog', but '/authors' leads to a page with a flag:

THM{L0L_WH0_D15}

### 2.4

Can login to umbraco with username 'SG@anthem.com' and password 'UmbracoIsTheBest!'.

Looking at the 'A cheers to our IT department' blog in Umbraco, the meta tag shows a flag:

THM{AN0TH3R_M3TA}

## Task 3

### 3.1

Have logged into Umbraco with username 'SG@anthem.com' and password 'UmbracoIsTheBest!'. Will attempt to log into RDP with same credentials.

```
xfreerdp /dynamic-resolution /v:10.10.105.125 /u:sg /p:UmbracoIsTheBest!
```

Login success. Will look for login credentials.

### 3.2

When logged in, the desktop shows 'user.txt', which shows the flag:

THM{N00T_NO0T}

### 3.3

Inspecting the C drive and enabling hidden files shows a hidden folder called 'backup', which contains a 'restore.txt' file. The current user doesn't have permission to open the file.

I can change the permissions though, so after changing file permissions, the file contains the potential administrator password:

ChangeMeBaby1MoreTime

### 3.4

Now the password has been found, I can escalate my privileges by clicking the administrator folder and entering the password when the prompt shows up.

After escalating privileges, the Desktop folder inside the Administrator folder contains 'root.txt' which shows the root flag:

THM{Y0U_4R3_1337}

## Things learnt/to improve upon

- Be familiar with what files are usually in a website e.g. robots.txt

- View page source on websites

- Learn how to change file permissions in Windows