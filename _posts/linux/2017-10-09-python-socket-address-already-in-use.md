---
layout: post
title:  "Socket address already in use"
date:   2017-10-09 15:50:19 +0800
categories: linux
---

A python program listen on port 12017 at localhost as a server, another program make tcp request on this port at local host.

When restart server, i got an error "Address already in use".This confused me for some months.

Now i find a solution at [1].

I should set SO_REUSEADDR option on when create socket.

```
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
```

  [1]: https://docs.python.org/2/library/socket.html
