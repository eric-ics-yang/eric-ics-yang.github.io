### Add more control on header in proxy_pass on nginx 

[how to add-a-response-header-on-nginx-when-using-proxy-pass](https://stackoverflow.com/questions/14501047/how-to-add-a-response-header-on-nginx-when-using-proxy-pass
) gives a solution on this problem.


There is a module called HttpHeadersMoreModule that gives you more control over headers. It does not come with Nginx and 

requires additional installation. With it, you can do something like this:
```
location ... {
  more_set_headers "Server: my_server";
}
```
That will "set the Server output header to the custom value for any status code and any content type". It will replace headers that are already set or add them if unset.

