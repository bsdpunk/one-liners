For mac, finding largest files: 

```
sudo du -hcx | perl -nle 'print "$_" IF(/^(\s|)(\d\.\d+G|\dG)/);' | sort -rnk1,1 
```

Mac's have some interesting compatibility quirks with certain things in the *nix universe. And I believe I composed this particular one liner because the -P switch on grep is not valid on a mac. So what I do was a standard, what files with du -hcx, piped that to perl and used perl in lieu of grep -P so I could seperate, then pipe to sort, what I needed to do to find the largest files and directories on my mac. 

Ok so for linux, and again remember this is files over a gig, you might do:
```
sudo du -hcx | grep -P '\dG' | sort -rnk1,
```


Ok so here is one on how to find bots / spiders / crawlers, in a certain time frame: 

```
cat /var/log/httpd/access_log | perl -nle 'print "$_" if(/02:0(\d):(\d+)/);' | egrep 'bot|crawl|spider'
```

So it searches your log for any time between 2:00:00 and 2:09:59, with bot, crawl, or spider. This is useful if you are trying to determine if a site is down because yandex and google-bot are slamming it at the same time. This defeats a certain test at a certain hosting company: 

```
perl -nle ‘print “$1” if(/(Question (\d+|\d)(.*)| (correct answer.*))/);’ quest
```

I've said to much already. But for all of this shit, where I have used perl...awk is probably the better, more elegant solution. 
Occasionally at my hosting job, some ubuntu boxes would just forget what happened, and where there root directory was supposed to be mounted, this is the quickest, though not the recommended way to fix that:


```
cat /proc/mounts > /etc/mtab
```

List directories and sort them by largest:


```
du -hcx –max-depth=5 | grep [0-9]G | sort -rnk1,1
```


To Reduce Reserve Space to 2 percent:

```
sudo tune2fs -m 2 /dev/sda1
```

Now if you need to check what your reserve space is currently set at:


```
sudo dumpe2fs -h /dev/sda1 2> /dev/null | awk -F ':' '{ if($1 == "Reserved block count") { rescnt=$2 } } { if($1 == "Block count") { blkcnt=$2 } } END { print "Reserved blocks: "(rescnt/blkcnt)*100"%" }'

```


Parsing an html table using perl:

```
perl -pe "s/.*<tr><th><b>(.*): <\/b><\/th><th.*<\/th><td>(.*)<\/td><\/tr>/\"\1\":\"\2\"/gi"
```

This is a thing I was working on, maybe it becomes something later
```
IFS=$(echo -en "\n\b");for i in $(last|awk '$4!~/^[0-9]/ {print $4 " " $5 " " $6 " 2015"}'); do node -e "var vdate = '$i';date = new
 Date(vdate);console.log( (new Date).getTime() -  date.getTime())"; done
```


Get all the repos of a user on github(replace bsdpunk):
```
http https://api.github.com/users/bsdpunk/repos |python -m json.tool| grep '"name"' | awk -F: '{print $2}' | tr -d \" | tr -d , > repos
```


Then use that list to backup all your projects(replace bsdpunk):
```
 for i in $(cat repos); do git clone https://github.com/bsdpunk/$i; done
```
If you want to find the amount of stars a repo has(replace trash and bsdpunk):
```
dusty@xor:~$ http https://api.github.com/repos/bsdpunk/trash/stargazers | python -m json.tool | grep login | wc -l
```
Some aliases for getting progress on dd:
```
alias findd="ps aux | grep dd | grep 'if='"
alias dprog='for i in $(findd |awk "{print $2}");do sudo kill -USR1 ;done'
```

Check battery life(Fedora):
```
alias power="upower -i /org/freedesktop/UPower/devices/battery_BAT0"
```

##Relative path
```
dirname $0
```
##Absolute path
It is worth noting that the -f flag on readlink is GNU related and  may not be on BSD systems
```
readlink -f
```


#More Bash Stuff
```
cat /sys/class/backlight/acpi_video0/max_brightness
sudo tee /sys/class/backlight/acpi_video0/brightness
alias g='google-chrome &'
alias gh='xclip -selection c -i ~/.gh'
cat /etc/motd
```
curl replace

```
http --verify no GET https://mechanicalpinata.com/bullshit X-Auth-Token:shittoken content-type:application/json
```

#Additions to my Bash Profile on Mac
Git function that does all the git things, you add your update quote like EX: gitall 'update message'
```
gitall () { git add . && git commit -m "$@" && git push origin master;}
```
Count the csv cols
```
function csvcount () { head -n1 "$@" | grep -o , | tr -d "\n" | wc -c; }
```
Google Chrome shortcut, mostly I use this for images, via: google loc.png
```
alias google='/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome'
```



#JESUS CHRIST
Extended permissions on macs are nightmares
```
chflags noschg CrashPlan.app # this removes system immutable flag
chflags nouchg CrashPlan.app # this removes the user immutable flags
xattr -l filename
xattr -d com.apple.FinderInfo
```

##Get the last file saved in a dir
```
function ffile () { echo $(ls -t | head -n1); }
```
###Then you could do something like
```
keybase decrypt `ffile`
```
##Wget Mirror, Recurse magic for video files
```
function wet () { wget -nc -c -r -A'*.flv' -A'*mp4' -A'*mkv' -A'*wmv' -A'*webm' -A'*.mpg' -A'*.mov' -A'.gif' -A'.jpg' -A'.wmv' $@ ;}
```

###Do something like
```
wet weirdo-porn.com/videos
```
##Open Unsecure Chrome that can do Cross Domain
```
alias ug='sudo open -a /Applications/Google\ Chrome.app --args --disable-web-security --user-data-dir'
```
