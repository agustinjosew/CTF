# Mr Robot CTF
Based on the show, Mr. Robot. This VM has three *keys* hidden in different locations. Your goal is to find all *three*. Each key is progressively difficult to find. The VM isn’t too difficult. There isn’t any advanced exploitation or reverse engineering. The level is considered beginner-intermediate.
 
<p align="center"><img src="https://i.imgur.com/Ybn9MlI.gif" align="center"></p>


|Name of the CTF |Date of release | Type | Size|
| :---: | :---: | :---: | :---: |
|[MR-ROBOT: 1](https://download.vulnhub.com/mrrobot/mrRobot.ova) | 2016 | Virtual Machine (Virtualbox - OVA)|704MB|

## CTF Walk-Through
### Things we have to do to achieve attack vector recognition
- [x] Determine the IP address of the server/machine
- [x] List of open or working ports and services
- [x] Try to determine what type of content is that we will be able to access according to the information obtained in the audit
- [x] Observe the type of information or documentation that arises from the audit
- [x] Carry out an effective organization of the data we discover through the use of tools or lateral/vertical thinking, we must always remember that the information we find is always powerful with the correct logic.

## First stage : recognition and information gathering

I prefer to use the * to scan the segment, with the use of `ifconfig` I can see which IP is mine but I need to know that other IPs are currently active, so we use  `nmap 192.168.234.*`
<p align="center"><img src="https://i.imgur.com/aEXHI0l.png" align="center"></p>
  
nmap tells me that `192.168.234.4` has ports and everything seems to indicate that it is a server, in the browser I enter that address and I see that it is a web page with commands but that they are not very useful, nor do I see that the url this one with `SSL` and that clears the picture a little more for me...
<p align="center"><img src="https://i.imgur.com/ifFovtX.png" align="center"></p>

I start to see the source code of the web, nothing out of the ordinary but something catches my attention, an IP address `208.185.115.6` that is invoked when loading the page through a script, I do a `whois` but nothing that It catches my attention, surely later I will see the script call but now I follow my usual checklist for this type of thing.

The healthy habit of entering web pages and looking for curious things makes us always try to find what content the file **robots.txt** has.
The content tells me that there is a `dictionary` called fsocity.dic By the extension I infer that this is what it is about, I take note to take it into account when having to scan with the dictionaries that I have, and finally it tells me that there is a text file called `key-1-of- 3.txt` also to verify what it is about.
<p align="center"><img src="https://i.imgur.com/VgL8w8G.png" align="center"></p>

In the command line at once I am going to create the `dictionaries` folder then move to the created folder and download the` fsocity.dic` file there for later analysis (then i do *cat fsocity.dic*...), I prefer to use wget... was designed for more than just downloading single URLs, but for spidering/mirroring entire sites, If you do use wget, **be aware that you may accidentally copy much, much more than you intended**.
<p align="center"><img src="https://i.imgur.com/yDtwFtV.png" align="center"></p>

Then I do all the above in a similar way to `download the file with a txt` extension that I found referenced in robots.txt, I do a `cat` and wala! we found the first key : **073403c8a58a1f80d943455fb30724b9**
<p align="center"><img src="https://i.imgur.com/4Lv3LZ4.png" align="center"></p>

After seeing the first key, two remain to be obtained. I am going to clone a repository with a tool called `dirsearch`, check the python requirements but also if I want I can run it with ~ bash, then change the default configuration a bit to be able to view the 200, 300, 301 responses with that in mind to better organize the recognition task, I hope to get directory names or file that corresponds to the basic structure of any *content management system* or *technology*.
<p align="center"><img src="https://i.imgur.com/PrdLsuo.png" align="center"></p>
<p align="center"><img src="https://i.imgur.com/SyBQiK2.png" align="center"></p>

After a few minutes I see the answers obtained on the terminal. Before executing with `python3 dirsearch.py -u HOST` I modify` default.conf` and put a filter for the response codes that I want to see only through the terminal, it is easier and we focus on the things that `we think are useful at this point of the investigation`; the reason why I wanted to see only the **200**, **300** and **301** is the following:
|Responde Code | Notes | Reference|
| :---:        | :---: | :---:    | 
|200           |The most common form of cache entry is a successful result of a retrieval request: i.e., a 200 (OK) response to a GET request, which contains a representation of the resource identified by the request target | [Successful 2xx](https://tools.ietf.org/html/rfc7231#section-6.3.1)|
|300           |The 300 (Multiple Choices) status code indicates that the target resource has more than one representation, each with its own more specific identifier, and information about the alternatives is being provided so that the user (or user agent) can select a preferred representation by redirecting its request to one or more of those identifiers.  In other words, the server desires that the user agent engage in reactive negotiation to select the most appropriate representation(s) for its needs | [Redirection 3xx](https://tools.ietf.org/html/rfc7231#section-6.4.1)|
|301           |The 301 (Moved Permanently) status code indicates that the target resource has been assigned a new permanent URI and any future references to this resource ought to use one of the enclosed URIs. Clients with link-editing capabilities ought to automatically re-link references to the effective request URI to one or more of the new references sent by the server, where possible. The server SHOULD generate a Location header field in the response containing a preferred URI reference for the new permanent URI. The user agent MAY use the Location field value for automatic redirection.  The server's response payload usually contains a short hypertext note with a hyperlink to the new URI(s). | [Redirection 3xx](https://tools.ietf.org/html/rfc7231#section-6.4.2)|

We are going to see the results through the terminal <p align="center"><img src="https://i.imgur.com/op2weqn.png" align="center"></p> 

At the beginning of the scan it was also indicated that a *report* was going to be generated in TXT format, we are going to directly filter the 200 OK by console to see more quickly with `grep -n 200 *.txt` where `-n` show line number where string was found and `*.txt` I use it because the file name is long and also I already know that it's only a single file has been created with that extension :).Then I run the same line again but I add `-c` at the end, this counts the occurrences in this case of 200 and tells me that I have 23 results with that search term.

<p align="center"><img src="https://i.imgur.com/5noHf0a.png" align="center"></p> 

If I look at the file names of grep it is a Worpress installation [`wp-login.php`](https://developer.wordpress.org/reference/files/wp-login.php/) so I proceed to use another tool:` wpscan` to gather more information, probably the dictionary (which I initially found with robots. txt) `fsocity.dic` helps us to check its content in conjunction with the tool, so I go step by step again, now I'm interested in seeing the content of fsocity.dic, so ` cat fsocity.dic | more ` and returns the following:
<p align="center"><img src="https://i.imgur.com/cYzX5fF.png" align="center"></p> 

This is a file with many lines, I am going to try to normalize the content, such as a database, check if there are duplicate words or symbols, in the case that there were to eliminate them, leave only the words or symbols that are unique, with This allows us to reduce the analysis time when we go to make the user/password validation.
The first thing I check is how many duplicates I have in fsocity.dic, using `sort fsocity.dic | uniq -c | more`
<p align="center"><img src="https://i.imgur.com/SQaukSU.png" align="center"></p>

I see that each line has an average repetition of up to 75 times, what interests me now is to remove what is additional and keep only the first occurrences of each iteration using `sort -u fsocity.dic | tee fsoc.dic` -> [TEE](https://www.man7.org/linux/man-pages/man1/tee.1.html).
<p align="center"><img src="https://i.imgur.com/M8szOZc.png" align="center"></p>

Once executed we have a new dictionary `fsoc.dic`,I execute again` sort fsoc.dic | uniq -c` and now only the number 1 appears in all the lines, thus indicating that there is only 1 single occurrence of each identifier in this dictionary, with this we improve the next step...
<p align="center"><img src="https://i.imgur.com/cZlMxCd.png" align="center"></p>

In short, sweeping and cleaning the dictionary obtained from robots.txt initially with **858160** possible *"combinations"* we end up with only **11451**
<p align="center"><img src="https://i.imgur.com/nPPmYLb.png" align="center"></p>

I run `wpscan` to see what information it returns, I don't have much hope but who knows ...
<p align="center"><img src="https://i.imgur.com/Qio2vyS.png" align="center"></p>

I see that the WP version is 4.3.1, with this in mind, what information do I get with the login area? Trying to enter as an administrator or some type of user with editor privileges from the wordpress control panel would be ideal to be able to do a reverse shell from "some" php file :).

The first thing that interests me is to see how the validation chain is composed with this version of wordpress, so I use a web proxy to see the logical flow step by step, among the tools that come to mind I have in mind ` ZAP` and `BURP`, I open Burp and see what happens (you can use one or the other, they both have many things you can do in their community versions).

I go to `http://192.168.234.4/wp-login.php`, *wp-login.php* I found it when I did the directory recognition previously, there I can see the login screen of the site in general, the profile of access that that user has assigned to interact with that instance of wordpress ... so let's get to work!
<p align="center"><img src="https://i.imgur.com/iPdHJpD.png" align="center"></p>
 
I am going to use any username and password, what interests me is to be able to analyze what happens when I want to enter, to see how the validation sequence is made up, to be able to see from the proxy what information I obtain to better organize the following steps
<p align="center"><img src="https://i.imgur.com/cm3YNVz.png" align="center"></p>

Observing the result of the request, in line 15 we have the `log` field and the` pwd` field, we basically need the same structure to use when trying the validations using the dictionary that we prepared previously.
One thing to keep in mind: this version of WP easily tells us the credential logic since if the `username` is incorrect, it is reported **ERROR: Invalid usename**... this is a great point of analysis, because if we know the user that is registered in the database, the only thing we would have to guess is the access password associated with that user, so first we will go through the user validation, when we have a match, just try with the password.
<p align="center"><img src="https://i.imgur.com/HCseX4w.png" align="center"></p>

In the sequence that Burp captured we see is POST. So we already know what type of request we have to make, where we have to send it and what parameters it must have to satisfy the validation logic. First I look for the username with the characters in the dictionary that I already have prepared, if there are positive results I will continue with the password, otherwise I will have to review the strategy again and start over until I achieve results. With `hydra` (*I could also use wpscan by the way*) I complete the necessary syntax to start trying to find an active user in the wp database.
<p align="center"><img src="https://i.imgur.com/tCg4WEJ.png" align="center"></p>

After a few minutes we have possible logins `elliot | Elliot | ELLIOT`
<p align="center"><img src="https://i.imgur.com/mM7Dddc.png" align="center"></p>

After a few minutes we have possible logins `elliot | Elliot | ELLIOT`, I create a new dictionary with `nano f-users.dic`, I insert the obtained variants, then I do `cat` to see the list and I re-execute the command prior to the verification of users but with some changes, now in `-L` I use the user dictionary specifically and for `-P` with the generic dictionary.
<p align="center"><img src="https://i.imgur.com/naz1mwo.png" align="center"></p>

We check the content of the file and the console and see that there are 3 matches for the user and its variants
<p align="center"><img src="https://i.imgur.com/nOiST4l.png" align="center"></p>

I check to see what happens, I do not want to be left with doubts about the type of user permissions :) I enter the data, I see that there are only two registered users and the profiles that have.
<p align="center"><img src="https://i.imgur.com/cP7sMl3.png" align="center"></p>

I dig into the profile of **mich05654** and what I read in `Biographical Info` catches my attention, I have in mind just in case.

## Second stage : Exploitation
I already have the access credentials, I verify that they work without any problem, but I cannot be logging in this way every time I want to do something, so what comes to my mind is to have a reverse shell whenever I want, from where I want and how want it.
This is easy to do, one enters the administration area, uploads the file and that's it, but I want to take advantage of the fact that I have the username and password to do it remotely with an xploit of `metasploit` :), basically, what it does is create a little plugin and it connects back to our machine and generates a reverse shell.
<p align="center"><img src="https://i.imgur.com/qY6UWzn.png" align="center"></p>

We look for what is available for wordpress by typing `search wordpres shell` and then we select the number 3
<p align="center"><img src="https://i.imgur.com/oHbgT6a.png" align="center"></p>

The next thing I do is see the configuration with `show options`, I am going to change the properties of the Payload (1) with the information of my IP and then the options of the Module (2) with all the data that I have been collecting
<p align="center"><img src="https://i.imgur.com/hpvUByz.png" align="center"></p>

<p align="center"><img src="https://i.imgur.com/pBmf8mo.png" align="center"></p>

Everything is ready and configured to run with `exploit` but.... I get a message informing that the objective is not a wordpress but I know that it is not, I am going to look at the code of the module to see what happens ...

<p align="center"><img src="https://i.imgur.com/QQbJjPN.png" align="center"></p>

`nano /usr/share/metasploit-framework/modules/exploits/unix/webapp/wp_admin_shell_upload.rb`
I commented out that line, save the changes and then I went back to reload on msf
![image](https://user-images.githubusercontent.com/76487325/114287336-5df53b80-9a3c-11eb-9caf-e254f6a89be9.png)

![image](https://user-images.githubusercontent.com/76487325/114287386-cb08d100-9a3c-11eb-946c-636bb82356e0.png)

**We got the shell up and running on the host!**

**ATTENTION !!! In case we see an error that the exploit could be executed but the session could not be created on our side, we check the firewall status of our terminal**
<p align="center"><img src="https://i.imgur.com/ylKboxj.png" align="center"></p>

Meterpreter is ready so let's see what we can do!, the first thing that comes to mind is to know where I am standing, for this I use [`pwd`](https://www.computerhope.com/unix/upwd.htm), then I use `cd /` to go to the root and do a `dir` to see what we have available...
![image](https://user-images.githubusercontent.com/76487325/114404601-36a58800-9b7c-11eb-9ef0-5bf0ce997d6e.png)

I have the home directory and inside the only thing I have is another directory `robot` I see the content, I can only see the` md5`, to see the content of the file `" key "` I need privileges that I do not have ... "yet"
What I see from md5 is that it has a `robot` user associated with it, so the next step is to revert that hash to a more human-readable format (?)
![image](https://i.imgur.com/lPBTUNt.png)

The next step then is to use [hashcat](https://hashcat.net/hashcat/), I can use some web sites that are for this too, in any of the scenarios I must have the file in my possession to be able to do what I need, -> `download password.raw- md5`, I observe where it was downloaded, in this case in the base directory that I am using, I open a new terminal, I look for the file, I move it where I want to have it ordered and then I see the content again

![image](https://i.imgur.com/hlXyvCP.png)

In this case it is easy to see the extension of the file that refers to MD5, but what happens if in other cases we find something but we don't know what it might be? for that we can use `"hash-identifier"`
![image](https://i.imgur.com/AlNpr0V.png)

To use hashcat I copy the downloaded file with another name and extension **.txt**, in this way I have a copy of the original file and at the same time the use I give to the content is more specific, I use the following command:
![image](https://i.imgur.com/5QDYXZk.png)

I am not convinced by the result from hashcat using the dictionary that I got at the beginning of the pentest, I would have to use other dictionaries, which would take me longer than desired, so I try with one that is [online](https://hashes.com/en/decrypt/hash)

![image](https://user-images.githubusercontent.com/76487325/114417741-3b703900-9b88-11eb-86fb-2990939f1c59.png)

The output is different from "local" to "online"
![image](https://user-images.githubusercontent.com/76487325/114417952-6f4b5e80-9b88-11eb-8348-d9e8dc183c83.png)

Comparing the two results I see that the online has a sequence that in hashcat I can do it using what is called **Mask Attack** , in this method rather than trying every possible word, we’ll limit the attempts in a predefined charset and password length.

A charset is defined by the characters allowed in the password.

|Charset| Chars allowed|
| :---: | :---:        |
|?l = | abcdefghijklmnopqrstuvwxyz|
|?u = | ABCDEFGHIJKLMNOPQRSTUVWXYZ|
|?d = | 0123456789|
|?h = | 0123456789abcdef|
|?H = | 0123456789ABCDEF|
|?s = | «space»!”#$%&'()*+,-./:;<=>?@[\]^_{|}~*|
|?a = | ?l?u?d?s|
|?b = | 0x00 – 0xff|

The other thing we can play with, is the password length. To indicates the password length option, we will generally repeat the charset character. Here are a few examples :

|Length | Meaning|
|:---:  |:---:|
|?l?l?l?l?l?l?l?l: | passwords between “aaaaaaaa” and “zzzzzzzz”|
|password?d: | passwords between “password0” and “password9”|
|?u?l?l: | passwords between “Aaa” and “Zzz”|

So I could try the netx time :)...


