# Pickle Rick CTF

> Derrick Evans | June 22, 2022 | [Pickle Rick CTF](https://tryhackme.com/room/picklerick)

---

1. Checked page source and notice a comment with Rick's username `R1ckRul3s`

2.  Full nmap scan can be found [here](nmap/initial_scan.txt).
	- Open ports:
		- `22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.6 (Ubuntu Linux; protocol 2.0)`
		- `80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))`

3. Checked robots.txt to reveal `Wubbalubbadubdub`.

4. Full gobuster scan can be found [here](gobuster/dir_scan.txt).
	- First directory returned was `/assets` with status of `301`
		- Navigating there shows the index and we can view jpg/gif in that location.
			- Ran `file` and `strings` on the images. Everything seemed to be fine with the images.
	- Second directory returned was `/server-status` with status of `403`

5. Searched for `OpenSSH 7.2p2` on `www.exploit-db.com` and found a couple of username enumerations.

6. Was unable to run hyrda with the ssh protocol due to the follow: `[ERROR] target ssh://$IP:22/ does not support password authentication`

7. Another gobuster scan was performed with a different wordlist. `gobuster dir -u $IP -w ~/CTF/SecLists-master/Discovery/Web-Content/dirsearch.txt`. The full scan can be found [here](gobuster/gobuster_dir_search_scan.txt).
	- Notable finds from this scan:
		- /assets/
		- /icons/
		- /login.php
		- /robots.txt

8. Nikto scan `nikto -h http://$IP`

9. Accessing `/login.php` shows an admin login form. You can login with `R1ckRul3s` as the username and `Wubbalubbadubdub` as the password.

10. Successful login grants access to `/portal.php`. This page has an input field for commands and an execute button.

11. Entering and executing `whoami` shows execution by `www-data` user.

12. Entering and executing `ls` shows the directory (`pwd` `/var/www/html`) contents:
	- Sup3rS3cretPickl3Ingred.txt
	- assets
	- clue.txt
	- denied.php
	- index.html
	- login.php
	- portal.php
	- robots.txt

13. Entering and executing `cat Sup3rS3cretPickl3Ingred.txt` shows `Command disabled to make it hard for future PICKLEEEE RICCCKKKK.`

14. I was able to get the first flag by entering and executing `grep . Sup3rS3cretPickl3Ingred.txt`, but this only worked because I knew there was a `.` in the flag from the TryHackMe answer format.

15. How to read file without cat?

16. I noticed entering and executing `cd ../../../../../../../` and then entering and executing `pwd` returns `/var/www/html`. This means it returns to `/var/www/html` after the page reloads. However, we can enter and execute multiple commands at once. For example: `cd ../../../../../;pwd` returns `/` the root directory.

17. Exploring this way I found `-rwxrwxrwx 1 root root 13 Feb 10  2019 second ingredients` in `/home/rick`

18. We can read this flag by running `grep ' ' 'second ingredients'`

19. Does not look like the www-data user needs password to run sudo: `sudo -l` shows:
```
User www-data may run the following commands on ip-10-10-71-159.eu-west-1.compute.internal:
    (ALL) NOPASSWD: ALL

```

20. The final ingredient was found in `/root`:
```
3rd.txt
snap
```

21. Used the same technique (with `sudo`) as before to read the flag.

---

<details>
	<summary>Flag Spoilers</summary>
	
### What is the first ingredient Rick needs?

```
mr. meeseek hair
```

### Whats the second ingredient Rick needs?

```
a jerry tear
```

### Whats the final ingredient Rick needs?

```
fleeb juice
```
</details>