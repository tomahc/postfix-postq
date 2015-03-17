
```
usage: postq queue [-h] [-o time [time ...]] [-s sender] [-r recipient] [-g {sender,rcpt}] [-i filename] [-p] [-e]

positional arguments:
  queue                                            Select a queue

optional arguments:
  -h, --help                                       show this help message and exit
  -o time [time ...], --older time [time ...]      Valid format is: d|h|m|s
  -s sender, --sender sender
  -r recipient, --rcpt recipient
  -g {sender,rcpt}, --group {sender,rcpt}
  -i filename, --filename filename                 Read postqueue output from file
  -p, --print           					       Print IDs instead
  -e, --errors          					       Show error messages
```

I was pretty much annoyed to create ridiculous chains of grep and awk commands over and over again. So I decided to build this script, where I can easily find the IDs I am searching for.

```postq``` can group its output by sender and rcpt, give you the errors messages and return IDs as stdin for ```postsuper```.

- ```--rcpt``` and ```--sender``` has full regex support
- ```--group``` always defaults to sender

##### Searching for messages which are older than ```--older```
 
 ```--older``` arguments are more or less like those passed to ```date -d``` in a shorter notation:

 - Todo: passing a datetime

```postq all --older 3d 2h 11m```



##### _Example Output_

```postq all -f postqueue.example```
```
give.some@example.com
  AFC351AC602A - Mon Feb 16 07:05:26
     ftp@example.net
  AFC351AC602A - Mon Feb 16 07:05:26
     admin@random.net
------------------------------
MAILER-DAEMON
   7C0C313C0C7 - Mon Jan 26 05:56:53
     bounce-1422246829.7609.89425103@notexistant.com
------------------------------
some.one@example.com
   9C2AB13C0BE - Fri Jan 30 14:52:13
     user1@random.net
    E7688DE3FA - Wed Feb 11 15:31:03
     she@example.com, he@example.com
------------------------------
someone.else@example.com
   DD6C66A8064 - Mon Dec  1 17:18:29
     user1@random.net, user2@random.net, user3@random.net
------------------------------
```

##### with ```--errors```

- Todo: regex matching

```
give.some@example.com
  AFC351AC602A - Mon Feb 16 07:05:26
     ftp@example.net
      (connect to mx.random.net[999.888.777.666]:25: Connection timed out)
  AFC351AC602A - Mon Feb 16 07:05:26
     admin@random.net
      (connect to mx2.random.net[888.721.412.123]:25: Connection timed out)
------------------------------
MAILER-DAEMON
   7C0C313C0C7 - Mon Jan 26 05:56:53
     bounce-1422246829.7609.89425103@notexistant.com
      (Host or domain name not found. Name service error for name=notexistant.com type=MX: Host not found, try again)
------------------------------
some.one@example.com
   9C2AB13C0BE - Fri Jan 30 14:52:13
     user1@random.net
    E7688DE3FA - Wed Feb 11 15:31:03
     she@example.com, he@example.com
      (Host or domain name not found. Name service error for name=example.com type=MX: Host not found, try again)
------------------------------
someone.else@example.com
   DD6C66A8064 - Mon Dec  1 17:18:29
     user1@random.net, user2@random.net, user3@random.net
------------------------------
```

##### with ```--print``` - postqueue IDs to stdout
```
7C0C313C0C7
9C2AB13C0BE
DD6C66A8064
AFC351AC602A
E7688DE3FA
```

##### with ```--group rcpt``` - group output by rcpt

```
user2@random.net
   DD6C66A8064 - Mon Dec  1 17:18:29
     someone.else@example.com
------------------------------
user1@random.net
   9C2AB13C0BE - Fri Jan 30 14:52:13
     some.one@example.com
   DD6C66A8064 - Mon Dec  1 17:18:29
     someone.else@example.com
------------------------------
admin@random.net
  AFC351AC602A - Mon Feb 16 07:05:26
     give.some@example.com
------------------------------
she@example.com
    E7688DE3FA - Wed Feb 11 15:31:03
     some.one@example.com
------------------------------
user3@random.net
   DD6C66A8064 - Mon Dec  1 17:18:29
     someone.else@example.com
------------------------------
he@example.com
    E7688DE3FA - Wed Feb 11 15:31:03
     some.one@example.com
------------------------------
bounce-1422246829.7609.89425103@notexistant.com
   7C0C313C0C7 - Mon Jan 26 05:56:53
     MAILER-DAEMON
------------------------------
ftp@example.net
  AFC351AC602A - Mon Feb 16 07:05:26
     give.some@example.com
------------------------------
```