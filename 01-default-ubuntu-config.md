Study of the default config of nginx

Here are the modules involved in this config:
- ngx_http_log_module
- ngx_http_core_module
- ngx_http_gzip_module
- ngx_http_proxy_module
- ngx_http_ssl_module
- ngx_mail_core_module
- ngx_mail_auth_http_module
- ngx_mail_imap_module
- ngx_mail_pop3_module

---
```
user www-data;
```

https://nginx.org/en/docs/ngx_core_module.html#user

defines `_user_` and `_group_` credentials used by worker processes

What user ?
I didn't know but www-data is an available user in my machine (okay cool)
It's there by default
This implies that nginx will only have the permissions of www-data
This might be important for security.

> ?? Apparently my nginx has his files in /var/www/html folder and not /usr/share/nginx/html as usual

You can forbid access to files by nginx by doing `chmod 600` iff the owner is not www-data

> Remember ! `chown` changer ownership

---

```
worker_processes auto;
```

https://nginx.org/en/docs/ngx_core_module.html#worker_processes

sets the number of workers

auto means the number of available cpu cores (maybe)

I do not know how to know much is needed or is good

they recommend the number of cpu cores of the machine

https://linuxhint.com/what-are-worker-connections-nginx/#:~:text=The%20worker_connections%20are%20the%20maximum,to%20accommodate%20a%20higher%20value.

They recommend it too but say to increase it in case of high io with disk

---

```
pid /run/nginx.pid;
```

https://nginx.org/en/docs/ngx_core_module.html#pid

file that stores the process id of nginx

Why do we need that ?
I guess to have access to it fast
because you can do `ps aux | grep nginx` to find it (althought woo pids appear)

This is used to "control nginx"

Here's the doc about that : http://nginx.org/en/docs/control.html

Let's read this later

---
```
include /etc/nginx/modules-enabled/*.conf;
```
https://nginx.org/en/docs/ngx_core_module.html#include

Include directives from other files

Good for structuring your config

Btw, there is something called njs that allows to do programmatic configs

---
## events

We enter the events block:
```
events {

	worker_connections 768;
	
	# multi_accept on;

}
```
https://nginx.org/en/docs/ngx_core_module.html#events

Allows the configuration of connection processing

---
```
worker_connections 768;
```

https://nginx.org/en/docs/ngx_core_module.html#worker_connections

maximum number of simultaneous connections per worker

... Let's fry my server

I did it but I needed to lower worker_connections and worker_processes.

I would have needed to increase the size of my website and above all increase the number of instances attacking to make it work.

AWESOME EXPERIENCE !! I love hacking ! AwS allows you to get multiple instances and experiment at will. (don't do anything crazy pls)

---
```
multi_accept on;
```
each worker accept all new connection at a time

---
## http
We are entering http
```
http { ... }
```
http server directives

---
```
sendfile on;
```
allows the use of `sendfile()` (which is a linux c function)
it deals with the concept of file descriptors which I do not understand fully 

Okay I understand now:
- file descriptor = number that represents an open file by the current process (worker)
- socket descriptor = type of file descriptor
	- it reads what the port receives
	- it write data to the port
- so send file copies the content of one file descriptor to another
- and in the case on nginx I am guessing one of the file desc is a socket one

I need to learn about networking !!

> I recommend reviewing C language, in particular the unix part !! (build a kernel if you have a time pls)
---
```
tcp_nopush on;
```
activate the TCP_CORK option
deals with tcp header i think

LEARN NETWORKING

read: https://baus.net/on-tcp_cork/

--------------
```
types_hash_max_size 2048;
```
all the stuff nginx does is saved in a hash table, you specify here the max size of each index. If it goes above, more hashes are added

---
```
server_tokens off;
```
toggle that server field of response

---
```
server_names_hash_bucket_size 64;
```
http://nginx.org/en/docs/hash.html

"The size of a table is expressed in buckets. The adjustment is continued until the table size exceeds the hash max size parameter."

There are a lot of things that are stored in tables, server_names is one of them (useful when there a lot (??))

---
```
server_name_in_redirect off;
```
Enables or disables the use of the primary server name (...) in [absolute](http://nginx.org/en/docs/http/ngx_http_core_module.html#absolute_redirect) redirects issued by nginx.

Now what are redirects ?

I think I'll understand once I move to server

---
```
include /etc/nginx/mime.types;
```
I need to check that `mime.types` file.

---
```
default_type application/octet-stream;
```
Read these:
- https://developer.mozilla.org/fr/docs/Web/HTTP/Basics_of_HTTP/MIME_Types#types_mime_utiles_pour_les_d%C3%A9veloppeurs_web
- https://developer.mozilla.org/fr/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Common_types

type MIME = **type Multipurpose Internet Mail Extensions (type MIME)**
Used to identify the type of data sent
Examples:
- text/plain
- text/html
- image/jpeg
- image/png
- audio/mpeg
- audio/ogg
- audio/*
- video/mp4
- application/octet-stream

The default type is 'application/octet-stream'.  Unknown file type.

---
```
ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3; # Dropping SSLv3, ref: POODLE
```
**ssl_protocols** \[`SSLv2`] \[`SSLv3`] \[`TLSv1`] \[`TLSv1.1`] \[`TLSv1.2`] \[`TLSv1.3`];

ssl is deprecated as a i understand
tls is a more updated protocol of ciphered protocol

----
```
ssl_prefer_server_ciphers on;
```
use our own cipher instead of the client's if possible

(ngl i am far from this)

---
```
access_log /var/log/nginx/access.log;
```
very long doc

Here's the format btw:

```
access_log path [format [buffer=size] [gzip[=level]] [flush=time] [if=condition]];
```
---
```
error_log /var/log/nginx/error.log;
```
same
```
error_log file [level];
```

----
```
gzip on;
```
gzip responses

---
```
gzip_vary on;
```
Vary: Accept-Encoding added to header

---
```
gzip_proxied any;
```

syntax: `gzip_proxied off | expired | no-cache | no-store | private | no_last_modified | no_etag | auth | any ...;`

---
```
gzip_comp_level 6;
```
compression level

---
```
gzip_buffers 16 8k;
```
gzip has some sort of buffers but I dont know

---
```
gzip_http_version 1.1;
```
min http version of request to allow gzip of response

---
```
gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;
```
enables gzip for those types

default = `text/html`

text/html is always compressed

---
```
include /etc/nginx/conf.d/*.conf;

include /etc/nginx/sites-enabled/*;
```
In Virtual Host Configs

What is a virtual host ??

---
## mail

```
mail { }
```
config of mail server

----
```
auth_http localhost/auth.php;
```
url of the http authentification of mail

---
```
pop3_capabilities "TOP" "USER";
```
sets pop3 protocol extensions

Let's create our own mail server ! (not now, let's move !)

---
```
imap_capabilities "IMAP4rev1" "UIDPLUS";
```
imap extensions

---

## server
```
server { ... }
```
Finally a server configuration

---
```
listen localhost:110;
```

Here's the syntax:
```
listen address:port [ssl] [proxy_protocol] [backlog=number] [rcvbuf=size] [sndbuf=size] [bind] [ipv6only=on|off] [so_keepalive=on|off|[keepidle]:[keepintvl]:[keepcnt]];
```
---
```
protocol pop3;
```
syntax:
```
protocol imap | pop3 | smtp;
```
---
```
proxy on;
```
Didn't find it ...


Voilà !