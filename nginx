# 1. Install NGINX
Use Ubuntu Server, run following command as root:
```
:~# apt update
:~# apt install nginx
```
After installion, run `:~# nginx -v` to verify the installation.

![](https://hackmd.io/_uploads/B1hp8dJJp.png)

Run `localhost`:
![](https://hackmd.io/_uploads/B1Nj8OyyT.png)
*Source link:* https://docs.nginx.com/nginx/admin-guide/installing-nginx/.

# 2. NGINX Web Server
Virtual host is a method of hosting multiple domain names on the same server.
Default `localhost` file is `index.html` placed in `/var/www/html`.
My attempt to run a domain name `toovy.com` with port `99` with my custom `index.html` file located on `/var/www/toovy.com`.
Firstly, create `index.html` file in this location:
```
:~# cd /var/www/
:/var/www# mkdir toovy.com
:/var/www# cd toovy.com
:/var/www/toovy.com# touch index.html
:/var/www/toovy.com# nano index.html
```
![](https://hackmd.io/_uploads/HJoVJ9Jy6.png)

Secondly, create `toovyserver` file in `/etc/nginx/sites-enabled/` directory.
```
:/var/www/toovy.com# cd /etc/nginx/sites-enabled/
:/etc/nginx/sites-enabled# touch toovyserver
```
Add `server` directive into this file.
`:/etc/nginx/sites-enabled# nano toovyserver`
```
server {
       listen 99;
       listen [::]:99;

       server_name toovy.com;

       root /var/www/toovy.com;
       index index.html;

       location / {
               try_files $uri $uri/ =404;
       }
}
```
Thirdly, change machine hosts to match `server_name` by editing `/etc/hosts` file.
`:/etc/nginx/sites-enabled# nano /etc/hosts`
![](https://hackmd.io/_uploads/HyG4Q9J1a.png)
In my case, I add `toovy.com` for `176.16.191.148` ens33 interface.
Finally, restart nginx service and check new site. 
`:/etc/nginx/sites-enabled# service nginx restart`

![](https://hackmd.io/_uploads/BJMFB9yka.png)
Source: https://ubuntu.com/tutorials/install-and-configure-nginx#1-overview
# 3. NGINX Reverse Proxy
In this mark, I will configure my server as a proxy serever. NGINX server will pass request to `gmail.com.`

Create a new file in `conf.d` directory:
```
:/etc/nginx/sites-enabled# cd /etc/nginx/conf.d
:/etc/nginx/conf.d# nano toovyserver.conf
```
Paste following script into `toovyserver.conf` file:
```
server {
        listen 99;
        server_name toovyrev.com;
 
        location / {
                proxy_pass https://gmail.com/;  
        }
}
```
Add `toovyrev.com` into `/etc/hosts` file. Then open browser and request `toovyrev.com:99`.

![](https://hackmd.io/_uploads/BJA_nRMJ6.png)

# 4.Load Balancer
Load balancing across multiple application instances is a commonly used technique for optimizing resource utilization, maximizing throughput, reducing latency, and ensuring fault‑tolerant configurations.
Building two http webserver with python. They both have `toovybl.com` domain.
```
from http.server import BaseHTTPRequestHandler, HTTPServer
import time

hostName = "127.1.1.1"
serverPort = #port

class MyServer(BaseHTTPRequestHandler):
    def do_GET(self):
        self.send_response(200)
        self.send_header("Content-type", "text/html")
        self.end_headers()
        self.wfile.write(bytes("server1/2", "utf-8"))

if __name__ == "__main__":        
    webServer = HTTPServer((hostName, serverPort), MyServer)
    print("Server started http://%s:%s" % (hostName, serverPort))

    try:
        webServer.serve_forever()
    except KeyboardInterrupt:
        pass

    webServer.server_close()
    print("Server stopped.")
```
* Server1: `127.1.1.1:9001`
* Server2: `127.1.1.1:9002`
Using NGINX to load balance HTTP traffic to a group of servers by defining with `upstream` dirctive. Create new file in `/etc/nginx/conf.d`:
```
upstream toovyserver{
    server 127.1.1.1:9001;
    server 127.1.1.1:9002;
}

server {
    listen 99;
    server_name toovybl.com;
    
    location / {
        proxy_pass http://toovyserver
    }
}
```
Run two server, do command `curl toovy.com:99`.
![](https://hackmd.io/_uploads/HycJGl716.png)

Server1 and Server2 alternate process request from proxy server `toovy.com:99`.
