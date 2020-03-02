# For mac, finding largest files: 

```
sudo du -hcx | perl -nle 'print "$_" if(/^(\s|)(\d\.\d+G|\dG)/);' | sort -rnk1,1 
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

Total Ram:
```bash
free -k | grep "Mem:" | grep -o -P "Mem:\s+\d+" | grep -o -P "\d+"
OR
awk '/MemTotal/ {print $2}' /proc/meminfo
```

Available Ram:
```bash
free -k | grep "buffers/" | awk -F"[:space:]+" '{print $5}' | grep -o -P "\d+$"
OR
cat /proc/meminfo | grep MemAvailable | awk '{print $2}'
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

## Relative path
```
dirname $0
```
## Absolute path
It is worth noting that the -f flag on readlink is GNU related and  may not be on BSD systems
```
readlink -f
```


# More Bash Stuff
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

# Additions to my Bash Profile on Mac
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



# JESUS CHRIST
Extended permissions on macs are nightmares
```
chflags noschg CrashPlan.app # this removes system immutable flag
chflags nouchg CrashPlan.app # this removes the user immutable flags
xattr -l filename
xattr -d com.apple.FinderInfo
```

## Get the last file saved in a dir
```
function ffile () { echo $(ls -t | head -n1); }
```
### Then you could do something like
```
keybase decrypt `ffile`
```
## Wget Mirror, Recurse magic for video files
```
function wet () { wget -nc -c -r -A'*.flv' -A'*mp4' -A'*mkv' -A'*wmv' -A'*webm' -A'*.mpg' -A'*.mov' -A'*.gif' -A'*.jpg' -A'*.wmv' $@ ;}
```

### Do something like
```
wet weirdo-porn.com/videos
```
## Open Unsecure Chrome that can do Cross Domain - On Mac
```
alias ug='sudo open -a /Applications/Google\ Chrome.app --args --disable-web-security --user-data-dir'
```
# Grep the titles out of an imdb page
```
curl http://www.imdb.com/name/nm0799777/  | grep '>.*</a></b>'
```
# Remove quotes from csv, and if there are commas in those quotes remove those as well

So if you put this in your .bashrc / .bash_profile
For Linux:
```
function rqc () { awk -F'"' -v OFS='' '{ for (i=2; i<=NF; i+=2) gsub(",", "", $i) } 1' $@ | sed 's/"//g' ;}
```
For Mac:
```
function rqc () { awk -F'"' -v OFS='' '{ for (i=2; i<=NF; i+=2) gsub(",", "", $i) } 1' $@ | gsed 's/"//g' ;}
```

## You might run something like this
```
tinyLove:Downloads dusty$ cat test.csv
"Heyo, comma",1,2,9879.232,"1,000","No comma",3
tinyLove:Downloads dusty$ rqc test.csv
Heyo comma,1,2,9879.232,1000,No comma,3
```


## Change csv's value, using a csv of key value pairs
```
function fcsvt () { awk -F, -v file="$1" -v sq="'" '{system("gsed -i "sq"s/,"$1",/,"$2",/"sq" "file""); print $2}' $2; }
```

### rDNS check
Start and end refer to the last octet numbers you wish to loop through
```
for i in {$START..END}; do host -t ptr $FIRSTTHREEOCTETS.$i $SERVERTOCHECK ; done | grep -F addr
```

### Find movies in subdirectories and Play them


Linux
```
function findmovies () { printf "find $(pwd) -type f \\( -name \"*mpg\" $(cat filetypes | grep -P -o "A\'\*\w+" | sed "s/A'\*\([a-z0-9]\+\)/\-o \-name \"\*\1\" /"| tr -d '\n')" | sed 's/$/ \\) \-exec mpv {} +/'; }
function playmovies () { eval "$(findmovies)";  }
```
Mac
```

function findmovies () { printf "find $(pwd) -type f \\( -name \"*mpg\" $( cat ~/filetypes | grep -o "A\'\*[A-Za-z0-9]\+'" | sed -e "s/A'\*\([a-zA-Z0-9]\{1,\}\)'/\-o \-name \"\*\1\" /" | tr -d '\n')" | sed 's/$/ \\) \-exec mpv {} +/'; }
function playmovies () { eval "$(findmovies)";  }
```
### Import zfs drive on mac/linux, replace d01 with your pool name
``` 
sudo zpool import
sudo zpool import -f d01
```

### Find and delete files in a directory and below, that are lower than 10MB
```
find . -type f -size -10M -exec rm -rf {} +/
```
## RabbitMQ

### Create exchange with $USER and $PASS
```
curl -i -u $USER:$PASS -H "content-type:application/json"     -XPUT -d'{"type":"direct","durable":true}'     http://10.1.10.200:15672/api/exchanges/%2ftest/signal-exchange
```

### Create God Like rabbitmq user on $VHOST
```
rabbitmqctl set_permissions -p $VHOST bsdpunk ".*" ".*" ".*"
```

### NVM settings for bash
```
export PATH=$PATH:$GOROOT/bin:~/.nvm/versions/node/v8.6.0/bin
export NVM_DIR="$HOME/.nvm" 
alias nvminit='. $(brew --prefix nvm)/nvm.sh'
```

### I need pidstat logging, that overwrites at the 24 hour hour mark, every five minutes
I seem to remember crontab not liking when any, stderr or stdin, so this creates a log every five minutes, and after 24 hours they begin to overwrite themselves.
Note: That within the date command substitution double quotes, with backslashes for the percentages.

```
*/5 * * * * /usr/bin/pidstat > /var/log/pidstat/pid.$(date "+\%H:\%M").log 2> /dev/null
```

### Port forwarding vcenter requires two ports
You need sudo to use a standard port and if your key is on your non-root user, you need to specify your key. You need to port forward 80, because you have to go in on 80, you need to port forward 443, because you will be switched to port 443. It has to be on your port 443. 10.1.10.211 is the foreign server, and bastion.beastie.com is the intermediate server, you are sshing too.
```
sudo ssh -L 443:10.1.10.211:443 bsdpunk@bastion.beastie.com -N -i /Users/dusty/.ssh/id_rsa
ssh -L 8888:10.1.10.211:80 bsdpunk@bastion.beastie.com -N
```
### Ansible shortcut for AdHoc command
```
ashell command file match
function ashell() { ansible -b -m shell -a "$1" -i "$2" "$3"; }
```

### Prep a pdf for Tesseract
```
convert -density 300 dc.pdf -depth 8 -strip -background white -alpha off file.tiff
```

### Finding the differences in two files
 There's no way this works. No money back guarantee. Forfiet all you have those who tread here.
 
 missing file1 file2
 
 Output is the difference of the two files.
```
 function missing() {     cat $1 <(cat $1 $2 ) <(cat $2 $1 | sort | uniq -c | tr -d ' ' | grep '^1' | sed 's/1//') | sort |  uniq -c | tr -d ' ' | grep '^2' | sed 's/2//'; }
 ```
 
### Add to the start of a file

```
sed -i '1 i\[' file
```


### Docker shit


kill all docker containers
```
function dockerkill { for i in $(sudo docker ps | awk '{ print $1 }' | sed 1d ); do echo $i; sudo docker kill $i; done; }
```

### ffmpeg shit

Record, my second hd monitor, capture sound(make adjustments in pavucontrol)
```
function magicRec { ffmpeg -video_size 3440x1440 -framerate 25 -f x11grab -i  :0.0+1920,0 -f pulse -ac 2 -i default $1.flv;   }
```
