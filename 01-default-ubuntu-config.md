Study of the default config of nginx

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

AWESOME EXPERIENCE !! I love hacking !

---
