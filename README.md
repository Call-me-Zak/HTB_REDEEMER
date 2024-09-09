# CTF Write Up for HTB_REDEEMER

### Briefing
This is a CTF writeup for REDEEMER on HTB Platform.

In this CTF, I covered the following concepts : Redis, Vulnerability Assessment and Anonymous/Guest Login.

### Port Scan
So, the usual ```nmap -sV <ip>``` command returns that all 1000 ports are closed, which is expected since by default nmap scans the 1000 common ports.

![image](https://github.com/user-attachments/assets/3e2d50bb-96d2-4174-a020-6be2dbc26e01)

So, we gotta expand the search by adding ```-p-``` flag which specifies to scan all 65535 ports (this'll take forever) so we'll speed things up a bit with the help of 02 other lovely flags ```--min-rate 5000``` (send 5000 packets per second) and ```-T5``` (most aggressive scan timing parameter, controls how fast scans and retransmissions occur as well as delays)

```nmap -sV -p- --min-rate 5000 -T5```

Larger scope, more fish.

![image](https://github.com/user-attachments/assets/2efd20c2-402c-4c21-9115-3e8031aa1f19)

So since the port is open we can go ahead and use redis-cli to establish a connection, now to know more about redis-cli we need to use the help utility :

```redis-cli --help``` this shows that to connect to a host we can use ```-h``` flag.

So after using : ```redis-cli -h <ip>``` then pressing enter the command line changes :

And we've established a connection.

To find out more about the redis server, we can use the ```info``` command which returns all the stats about the server :

![image](https://github.com/user-attachments/assets/17167ffb-deed-4933-9c85-c929992d7b82)

More info...

![image](https://github.com/user-attachments/assets/fab9f462-9070-4668-b25b-c622f83d9093)

As cliché as this may sound, but we all know wherever, there're secrets, there needs to be keys, thus, after looking up the stats we can see that there's a key section that lists 4 keys total.
If we were to look up what command fetches those keys, it's none other than ```keys *``` the asterisk being the wild card that fetches everything.

![image](https://github.com/user-attachments/assets/e7a5a3b1-aa93-44e4-af13-25c6858a1482)

There're 04, and already guessed it, there's our flag, now to get that, we'll need to know what command reads a key, looking up redis-cli sorts that out for us.

So...turns out each type of key has a specific command for it to get it:

```
string : GET flag
list : LRANGE flag 0 -1 (start -> stop index)
set : SMEMBERS flag
zset (sorted set) : ZRANGE flag 0 -1 WITHSCORES (grabs scores)
hash :  HGETALL flag
```

And our flag type is a string

![image](https://github.com/user-attachments/assets/963f9cde-80a9-4943-bc8b-3d098a771626)

Thank you for your time.

![image](https://github.com/user-attachments/assets/59b331cc-02bb-4de2-8430-f7efa6c73f9d)
