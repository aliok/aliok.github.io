---
layout: post
title:  "Horror Story"
date:   2014-05-15 01:07:43
banner: "/post-assets/layout/temp_picture.jpg"

---

Yesterday when I was trying to create a quality system for the upcoming release of our tool, Innbound, I accidentally messed up the data in production system!
<!--more-->

![Screenshot](/post-assets/horror-story-screenshot0.png)


I didn’t know what to do and started learning about the MySQL recovery options.

I want to note that it happened at *17:02*, since I am gonna refer that time in the blog post.

Here is the deal:

* MySQL logs every update/insert/delete/create/drop etc. modification statements into files called “binary logs”
* We had the back up from the morning

So, what I needed to do was

* Stop the application which accesses the MySQL database immediately
* Note the time of the accident (17:02)
* Load the back up from the morning (11:00)
* Get the modification statements for 11:00 – 17:02 from binary logs
* Apply the modification scripts

Binary logs are stored in the folder defined in the my.cnf file:
{% highlight properties %}
...
log_bin                 = /var/log/mysql/mysql-bin.log
...
{% endhighlight %}

And here they are:

{% highlight bash %}
root@innbound:/var/lib/mysql# cd /var/log/mysql/
root@innbound:/var/log/mysql# ls -lah
total 1,1G
drwxr-s---  2 mysql adm   4,0K Mai 14 17:52 .
drwxr-xr-x 10 root  root  4,0K Mai 14 06:25 ..
...
...
-rw-rw----  1 mysql adm    54M Mai 12 06:25 mysql-bin.000291
-rw-rw----  1 mysql adm   101M Mai 13 05:38 mysql-bin.000292
-rw-rw----  1 mysql adm   2,8M Mai 13 06:25 mysql-bin.000293
-rw-rw----  1 mysql adm    92M Mai 14 06:25 mysql-bin.000294        <<
-rw-rw----  1 mysql adm    42M Mai 14 17:02 mysql-bin.000295        <<
-rw-rw----  1 mysql adm    512 Mai 14 06:25 mysql-bin.index
-rw-r-----  1 mysql adm    51M Mai 14 17:47 mysql-slow.log
-rw-r-----  1 mysql adm   2,9M Mai 14 06:25 mysql-slow.log.1.gz
-rw-r-----  1 mysql adm   2,9M Mai 13 06:25 mysql-slow.log.2.gz
-rw-r-----  1 mysql adm  1017K Mai 12 06:25 mysql-slow.log.3.gz
...
{% endhighlight %}

Only the files marked with `<<` was relevant for me since I had the back up from 11:00.

To learn exact back up moment, we first checked the file produced by `mysqlbinlog` a date range around the back up moment:
{% highlight bash %}
mysqlbinlog --start-datetime="2014-05-14 10:58:00" --stop-datetime="2014-05-14 11:02:00" mysql-bin.000294 mysql-bin.000295 > modifications_around_back_up_moment.sql
{% endhighlight %}

With the output of this command, you can determine when the back up is executed. There will be some gap of a couple of seconds at least.
From that file, I learnt the back up happened at 11:02:35.

I executed following to get the scripts executed between 11:02:30 and 17:02:

{% highlight bash %}
mysqlbinlog --start-datetime="2014-05-14 11:02:30" --stop-datetime="2014-05-14 17:02:00" mysql-bin.000294 mysql-bin.000295 > modifications_since_back_up_moment.sql
{% endhighlight %}

Please note I didn’t specify exactly 11:02:35 as the `start-datetime`, since there can be a lot of statements in a second.
So, I had to modify the file manually afterwards to remove everything that is before the back up moment.

Then the remaining is easy. 

Just loaded the dump from morning into database:

{% highlight bash %}
mysql -u somebody mydatabasename < dump_from_the_morning.sql
{% endhighlight %}

and then executed the modification statements:
{% highlight bash %}
mysql -u somebody mydatabasename < modifications_since_back_up_moment.sql
{% endhighlight %}

You can use the following bash script to get the row counts of tables and then compare the corrupt database with the database after recovery applied:
{% highlight bash %}
sourceDb=$1;
mysql_command="mysql --user=somebody --password=somepassword"
if [ -z "$1" ]
  then
    echo "Usage : $0 <sourceDb>"
exit 1
fi
for table in `$mysql_command -s -N -e "show tables from $sourceDb"`;
  do 
    echo "$table";
$mysql_command -s -N -e "select count(*) from $sourceDb.$table";
done;
{% endhighlight %}
 
*NOTE*: My recovery was more painful than this. I also wanted to do the operations in my local environment and because of 
max-packet-size and encoding problems in my laptop, I wasn’t successful. Because of those problems, 
doing these stuff took me 6 hours. Next time something like this happens, I would definitely do it on a machine which 
is identical to the production server.
 
#### Resources:
<http://dev.mysql.com/doc/refman/5.0/en/point-in-time-recovery-times.html>