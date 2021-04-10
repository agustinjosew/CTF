# Mr Robot CTF
Based on the show, Mr. Robot. This VM has three *keys* hidden in different locations. Your goal is to find all *three*. Each key is progressively difficult to find. The VM isn’t too difficult. There isn’t any advanced exploitation or reverse engineering. The level is considered beginner-intermediate.
 
<p align="center"><img src="https://i.imgur.com/Ybn9MlI.gif" align="center"></p>


|Name of the CTF |Date of release | Type | Size|
| :---: | :---: | :---: | :---: |
|[MR-ROBOT: 1](https://download.vulnhub.com/mrrobot/mrRobot.ova) | 2016 | Virtual Machine (Virtualbox - OVA)|704MB|

## CTF Walk-Through
### Things we have to do to achieve attack vector recognition
- [ ] Determine the IP address of the server/machine
- [ ] List of open or working ports and services
- [ ] Try to determine what type of content is that we will be able to access according to the information obtained in the audit
- [ ] Observe the type of information or documentation that arises from the audit
- [ ] Carry out an effective organization of the data we discover through the use of tools or lateral/vertical thinking, we must always remember that the information we find is always powerful with the correct logic.

##

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

I see that the WP version is 4.3.1, with this in mind, what information do I get with the login area? Trying to enter as an administrator or some type of user with editor privileges from the wordpress control panel would be ideal to be able to do a reverse shell from "some" php file :)


