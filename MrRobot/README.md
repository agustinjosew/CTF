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



