For mac, finding largest files: 

```
sudo du -hcx | perl -nle 'print "$_" IF(/^(\s|)(\d\.\d+G|\dG)/);' | sort -rnk1,1 
```

Mac's have some interesting compatibility quirks with certain things in the *nix universe. And I believe I composed this particular one liner because the -P switch on grep is not valid on a mac. So what I do was a standard, what files with du -hcx, piped that to perl and used perl in lieu of grep -P so I could seperate, then pipe to sort, what I needed to do to find the largest files and directories on my mac. Ok so here is one on how to find bots / spiders / crawlers, in a certain time frame: 

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
