---
title: "How to Create a Quick Web Server"
date: 2017-10-11T17:58:37-04:00
draft: false
author: "Victor Mendonça"
description: "How to create a quick web server."
tags: ["Networking", "Linux", "Netcat", "python"]
---

Have you ever had the need to create a web server to perform a test? Well, here are a few quick options on how to do this.


Got Netcat?
---

If you have Netcat on the server (which is pretty easy to get), then you can do this.

**Example 1:** Display the date and the text "It works"

```
nc -kl 10001 -c 'echo -e "HTTPS/1.1 200 OK\r\n\r\n$(date)\r\n\r\nIt works"'
```

**Example 2:** Display an index.html file

```
nc -kl 10001 -c 'echo -e "HTTPS/1.1 200 OK\r\n\r\n$(cat index.html)"'
```

**Example 3:** Using HTTPS

a. First get a set of self-signed certificate and key (either generate or download here - [http://www.selfsignedcertificate.com/](http://www.selfsignedcertificate.com/))

b. Add the certificate to the server and run netcate with the following parameters

```
nc -l -p 10001 -k --ssl --ssl-cert ca.crt --ssl-key ca.key -c 'echo -e "HTTP/1.1 200 OK\r\n\r\n$(date)\r\n\r\nIt works"'
```

Python
---

This is the easiest one, as long as you have Python on that system. Just run the commands below and you are set. If you have an index file on that folder, Python will display, otherwise it will do a directory listing.

```
$ python -m SimpleHTTPServer 8888
Serving HTTP on 0.0.0.0 port 8888 ...
```

### Using HTTPS/SSL

And you can also create a SSL enabled listener with Python. For this test, you will need to have the certificate and key in `pem` format.

**Pyton 3**

```python
from http.server import HTTPServer, BaseHTTPRequestHandler
import ssl


httpd = HTTPServer(('localhost', 4443), SimpleHTTPRequestHandler)

httpd.socket = ssl.wrap_socket (httpd.socket,
        keyfile="path/to/key.pem",
        certfile='path/to/cert.pem', server_side=True)

httpd.serve_forever()
```

**Python 2.x**

```python
import BaseHTTPServer, SimpleHTTPServer
import ssl


httpd = BaseHTTPServer.HTTPServer(('localhost', 4443),
        SimpleHTTPServer.SimpleHTTPRequestHandler)

httpd.socket = ssl.wrap_socket (httpd.socket,
        keyfile="path/tp/key.pem",
        certfile='path/to/cert.pem', server_side=True)

httpd.serve_forever()
```
