# Multicontainer Pods
multiContainerPod.yml
```
apiVersion: v1
kind: Pod
metadata:
  name: mimulticontainerpod
  namespace: paradigma
spec:
  containers:
  - name: nginx
    image: nginx:latest
    ports:
    - containerPort: 80
  - name: ubuntu
    image: ubuntu:20.04
    command: ['sh', '-c', 'while true; do sleep 30; done;']
```
* Creamos un pod multicontainer usando el fichero multiContainerPod.yml y accedemos al nginx desde ubuntu
```
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl apply -f multiContainerPod.yml
pod/mimulticontainerpod unchanged
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl describe pod mimulticontainerpod -n paradigma
Name:         mimulticontainerpod
Namespace:    paradigma
Priority:     0
Node:         minikube/192.168.49.2
Start Time:   Sun, 19 Jun 2022 13:25:00 +0200
Labels:       <none>
Annotations:  <none>
Status:       Running
IP:           172.17.0.4
IPs:
  IP:  172.17.0.4
Containers:
  nginx:
    Container ID:   docker://d2136000453fad195fb09e9eefc3044897d6745b99f4ec0cfc024955c2be7c66
    Image:          nginx:latest
    Image ID:       docker-pullable://nginx@sha256:2bcabc23b45489fb0885d69a06ba1d648aeda973fae7bb981bafbb884165e514
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Sun, 19 Jun 2022 16:06:08 +0200
    Last State:     Terminated
      Reason:       Error
      Exit Code:    255
      Started:      Sun, 19 Jun 2022 13:25:12 +0200
      Finished:     Sun, 19 Jun 2022 16:05:46 +0200
    Ready:          True
    Restart Count:  1
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-8578v (ro)
  ubuntu:
    Container ID:  docker://8dc02a0fc580a8bda2ce473796047def9f5330fdf70636802146104fad3024eb
    Image:         ubuntu:20.04
    Image ID:      docker-pullable://ubuntu@sha256:fd92c36d3cb9b1d027c4d2a72c6bf0125da82425fc2ca37c414d4f010180dc19
    Port:          <none>
    Host Port:     <none>
    Command:
      sh
      -c
      while true; do sleep 30; done;
    State:          Running
      Started:      Sun, 19 Jun 2022 16:06:08 +0200
    Last State:     Terminated
      Reason:       Error
      Exit Code:    255
      Started:      Sun, 19 Jun 2022 13:25:12 +0200
      Finished:     Sun, 19 Jun 2022 16:05:46 +0200
    Ready:          True
    Restart Count:  1
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-8578v (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  kube-api-access-8578v:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason          Age    From     Message
  ----    ------          ----   ----     -------
  Normal  SandboxChanged  4m3s   kubelet  Pod sandbox changed, it will be killed and re-created.
  Normal  Pulling         4m2s   kubelet  Pulling image "nginx:latest"
  Normal  Pulled          3m59s  kubelet  Successfully pulled image "nginx:latest" in 2.983635339s
  Normal  Created         3m59s  kubelet  Created container nginx
  Normal  Started         3m59s  kubelet  Started container nginx
  Normal  Pulled          3m59s  kubelet  Container image "ubuntu:20.04" already present on machine
  Normal  Created         3m59s  kubelet  Created container ubuntu
  Normal  Started         3m59s  kubelet  Started container ubuntu
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl exec -it mimulticontainerpod -c ubuntu -n paradigma -- bash
root@mimulticontainerpod:/# curl localhost:80
bash: curl: command not found
root@mimulticontainerpod:/# apt update
Get:1 http://security.ubuntu.com/ubuntu focal-security InRelease [114 kB]
Get:2 http://archive.ubuntu.com/ubuntu focal InRelease [265 kB]
Get:3 http://security.ubuntu.com/ubuntu focal-security/universe amd64 Packages [881 kB]
Get:4 http://archive.ubuntu.com/ubuntu focal-updates InRelease [114 kB]
Get:5 http://archive.ubuntu.com/ubuntu focal-backports InRelease [108 kB]
Get:6 http://archive.ubuntu.com/ubuntu focal/multiverse amd64 Packages [177 kB]
Get:7 http://archive.ubuntu.com/ubuntu focal/main amd64 Packages [1275 kB]
Get:8 http://security.ubuntu.com/ubuntu focal-security/multiverse amd64 Packages [27.5 kB]
Get:9 http://security.ubuntu.com/ubuntu focal-security/restricted amd64 Packages [1324 kB]
Get:10 http://archive.ubuntu.com/ubuntu focal/restricted amd64 Packages [33.4 kB]       
Get:11 http://archive.ubuntu.com/ubuntu focal/universe amd64 Packages [11.3 MB]             
Get:12 http://security.ubuntu.com/ubuntu focal-security/main amd64 Packages [1974 kB]       
Get:13 http://archive.ubuntu.com/ubuntu focal-updates/multiverse amd64 Packages [30.3 kB]     
Get:14 http://archive.ubuntu.com/ubuntu focal-updates/restricted amd64 Packages [1404 kB]
Get:15 http://archive.ubuntu.com/ubuntu focal-updates/universe amd64 Packages [1170 kB]
Get:16 http://archive.ubuntu.com/ubuntu focal-updates/main amd64 Packages [2407 kB]
Get:17 http://archive.ubuntu.com/ubuntu focal-backports/universe amd64 Packages [27.1 kB]
Get:18 http://archive.ubuntu.com/ubuntu focal-backports/main amd64 Packages [54.2 kB]
Fetched 22.7 MB in 3s (8696 kB/s)                             
Reading package lists... Done
Building dependency tree       
Reading state information... Done
7 packages can be upgraded. Run 'apt list --upgradable' to see them.
root@mimulticontainerpod:/# apt install curl
Reading package lists... Done
Building dependency tree       
Reading state information... Done
The following additional packages will be installed:
  ca-certificates krb5-locales libasn1-8-heimdal libbrotli1 libcurl4 libgssapi-krb5-2 libgssapi3-heimdal libhcrypto4-heimdal libheimbase1-heimdal libheimntlm0-heimdal libhx509-5-heimdal libk5crypto3
  libkeyutils1 libkrb5-26-heimdal libkrb5-3 libkrb5support0 libldap-2.4-2 libldap-common libnghttp2-14 libpsl5 libroken18-heimdal librtmp1 libsasl2-2 libsasl2-modules libsasl2-modules-db libsqlite3-0
  libssh-4 libssl1.1 libwind0-heimdal openssl publicsuffix
Suggested packages:
  krb5-doc krb5-user libsasl2-modules-gssapi-mit | libsasl2-modules-gssapi-heimdal libsasl2-modules-ldap libsasl2-modules-otp libsasl2-modules-sql
The following NEW packages will be installed:
  ca-certificates curl krb5-locales libasn1-8-heimdal libbrotli1 libcurl4 libgssapi-krb5-2 libgssapi3-heimdal libhcrypto4-heimdal libheimbase1-heimdal libheimntlm0-heimdal libhx509-5-heimdal libk5crypto3
  libkeyutils1 libkrb5-26-heimdal libkrb5-3 libkrb5support0 libldap-2.4-2 libldap-common libnghttp2-14 libpsl5 libroken18-heimdal librtmp1 libsasl2-2 libsasl2-modules libsasl2-modules-db libsqlite3-0
  libssh-4 libssl1.1 libwind0-heimdal openssl publicsuffix
0 upgraded, 32 newly installed, 0 to remove and 7 not upgraded.
Need to get 5445 kB of archives.
After this operation, 16.7 MB of additional disk space will be used.
Do you want to continue? [Y/n] Y
Get:1 http://archive.ubuntu.com/ubuntu focal-updates/main amd64 libssl1.1 amd64 1.1.1f-1ubuntu2.13 [1321 kB]
Get:2 http://archive.ubuntu.com/ubuntu focal-updates/main amd64 openssl amd64 1.1.1f-1ubuntu2.13 [620 kB]
Get:3 http://archive.ubuntu.com/ubuntu focal-updates/main amd64 ca-certificates all 20211016~20.04.1 [144 kB]
Get:4 http://archive.ubuntu.com/ubuntu focal-updates/main amd64 libsqlite3-0 amd64 3.31.1-4ubuntu0.3 [549 kB]
Get:5 http://archive.ubuntu.com/ubuntu focal-updates/main amd64 krb5-locales all 1.17-6ubuntu4.1 [11.4 kB]
Get:6 http://archive.ubuntu.com/ubuntu focal-updates/main amd64 libkrb5support0 amd64 1.17-6ubuntu4.1 [30.9 kB]
Get:7 http://archive.ubuntu.com/ubuntu focal-updates/main amd64 libk5crypto3 amd64 1.17-6ubuntu4.1 [79.9 kB]
Get:8 http://archive.ubuntu.com/ubuntu focal-updates/main amd64 libkeyutils1 amd64 1.6-6ubuntu1.1 [10.3 kB]
Get:9 http://archive.ubuntu.com/ubuntu focal-updates/main amd64 libkrb5-3 amd64 1.17-6ubuntu4.1 [330 kB]
Get:10 http://archive.ubuntu.com/ubuntu focal-updates/main amd64 libgssapi-krb5-2 amd64 1.17-6ubuntu4.1 [121 kB]
Get:11 http://archive.ubuntu.com/ubuntu focal/main amd64 libpsl5 amd64 0.21.0-1ubuntu1 [51.5 kB]
Get:12 http://archive.ubuntu.com/ubuntu focal/main amd64 publicsuffix all 20200303.0012-1 [111 kB]
Get:13 http://archive.ubuntu.com/ubuntu focal-updates/main amd64 libbrotli1 amd64 1.0.7-6ubuntu0.1 [267 kB]
Get:14 http://archive.ubuntu.com/ubuntu focal/main amd64 libroken18-heimdal amd64 7.7.0+dfsg-1ubuntu1 [41.8 kB]
Get:15 http://archive.ubuntu.com/ubuntu focal/main amd64 libasn1-8-heimdal amd64 7.7.0+dfsg-1ubuntu1 [181 kB]
Get:16 http://archive.ubuntu.com/ubuntu focal/main amd64 libheimbase1-heimdal amd64 7.7.0+dfsg-1ubuntu1 [29.7 kB]
Get:17 http://archive.ubuntu.com/ubuntu focal/main amd64 libhcrypto4-heimdal amd64 7.7.0+dfsg-1ubuntu1 [87.9 kB]
Get:18 http://archive.ubuntu.com/ubuntu focal/main amd64 libwind0-heimdal amd64 7.7.0+dfsg-1ubuntu1 [48.0 kB]
Get:19 http://archive.ubuntu.com/ubuntu focal/main amd64 libhx509-5-heimdal amd64 7.7.0+dfsg-1ubuntu1 [107 kB]
Get:20 http://archive.ubuntu.com/ubuntu focal/main amd64 libkrb5-26-heimdal amd64 7.7.0+dfsg-1ubuntu1 [208 kB]
Get:21 http://archive.ubuntu.com/ubuntu focal/main amd64 libheimntlm0-heimdal amd64 7.7.0+dfsg-1ubuntu1 [15.1 kB]
Get:22 http://archive.ubuntu.com/ubuntu focal/main amd64 libgssapi3-heimdal amd64 7.7.0+dfsg-1ubuntu1 [96.1 kB]
Get:23 http://archive.ubuntu.com/ubuntu focal-updates/main amd64 libsasl2-modules-db amd64 2.1.27+dfsg-2ubuntu0.1 [14.7 kB]
Get:24 http://archive.ubuntu.com/ubuntu focal-updates/main amd64 libsasl2-2 amd64 2.1.27+dfsg-2ubuntu0.1 [49.3 kB]
Get:25 http://archive.ubuntu.com/ubuntu focal-updates/main amd64 libldap-common all 2.4.49+dfsg-2ubuntu1.9 [16.6 kB]
Get:26 http://archive.ubuntu.com/ubuntu focal-updates/main amd64 libldap-2.4-2 amd64 2.4.49+dfsg-2ubuntu1.9 [155 kB]
Get:27 http://archive.ubuntu.com/ubuntu focal/main amd64 libnghttp2-14 amd64 1.40.0-1build1 [78.7 kB]
Get:28 http://archive.ubuntu.com/ubuntu focal/main amd64 librtmp1 amd64 2.4+20151223.gitfa8646d.1-2build1 [54.9 kB]
Get:29 http://archive.ubuntu.com/ubuntu focal-updates/main amd64 libssh-4 amd64 0.9.3-2ubuntu2.2 [170 kB]
Get:30 http://archive.ubuntu.com/ubuntu focal-updates/main amd64 libcurl4 amd64 7.68.0-1ubuntu2.11 [235 kB]
Get:31 http://archive.ubuntu.com/ubuntu focal-updates/main amd64 curl amd64 7.68.0-1ubuntu2.11 [162 kB]
Get:32 http://archive.ubuntu.com/ubuntu focal-updates/main amd64 libsasl2-modules amd64 2.1.27+dfsg-2ubuntu0.1 [48.8 kB]
Fetched 5445 kB in 0s (11.5 MB/s)       
debconf: delaying package configuration, since apt-utils is not installed
Selecting previously unselected package libssl1.1:amd64.
(Reading database ... 4126 files and directories currently installed.)
Preparing to unpack .../00-libssl1.1_1.1.1f-1ubuntu2.13_amd64.deb ...
Unpacking libssl1.1:amd64 (1.1.1f-1ubuntu2.13) ...
Selecting previously unselected package openssl.
Preparing to unpack .../01-openssl_1.1.1f-1ubuntu2.13_amd64.deb ...
Unpacking openssl (1.1.1f-1ubuntu2.13) ...
Selecting previously unselected package ca-certificates.
Preparing to unpack .../02-ca-certificates_20211016~20.04.1_all.deb ...
Unpacking ca-certificates (20211016~20.04.1) ...
Selecting previously unselected package libsqlite3-0:amd64.
Preparing to unpack .../03-libsqlite3-0_3.31.1-4ubuntu0.3_amd64.deb ...
Unpacking libsqlite3-0:amd64 (3.31.1-4ubuntu0.3) ...
Selecting previously unselected package krb5-locales.
Preparing to unpack .../04-krb5-locales_1.17-6ubuntu4.1_all.deb ...
Unpacking krb5-locales (1.17-6ubuntu4.1) ...
Selecting previously unselected package libkrb5support0:amd64.
Preparing to unpack .../05-libkrb5support0_1.17-6ubuntu4.1_amd64.deb ...
Unpacking libkrb5support0:amd64 (1.17-6ubuntu4.1) ...
Selecting previously unselected package libk5crypto3:amd64.
Preparing to unpack .../06-libk5crypto3_1.17-6ubuntu4.1_amd64.deb ...
Unpacking libk5crypto3:amd64 (1.17-6ubuntu4.1) ...
Selecting previously unselected package libkeyutils1:amd64.
Preparing to unpack .../07-libkeyutils1_1.6-6ubuntu1.1_amd64.deb ...
Unpacking libkeyutils1:amd64 (1.6-6ubuntu1.1) ...
Selecting previously unselected package libkrb5-3:amd64.
Preparing to unpack .../08-libkrb5-3_1.17-6ubuntu4.1_amd64.deb ...
Unpacking libkrb5-3:amd64 (1.17-6ubuntu4.1) ...
Selecting previously unselected package libgssapi-krb5-2:amd64.
Preparing to unpack .../09-libgssapi-krb5-2_1.17-6ubuntu4.1_amd64.deb ...
Unpacking libgssapi-krb5-2:amd64 (1.17-6ubuntu4.1) ...
Selecting previously unselected package libpsl5:amd64.
Preparing to unpack .../10-libpsl5_0.21.0-1ubuntu1_amd64.deb ...
Unpacking libpsl5:amd64 (0.21.0-1ubuntu1) ...
Selecting previously unselected package publicsuffix.
Preparing to unpack .../11-publicsuffix_20200303.0012-1_all.deb ...
Unpacking publicsuffix (20200303.0012-1) ...
Selecting previously unselected package libbrotli1:amd64.
Preparing to unpack .../12-libbrotli1_1.0.7-6ubuntu0.1_amd64.deb ...
Unpacking libbrotli1:amd64 (1.0.7-6ubuntu0.1) ...
Selecting previously unselected package libroken18-heimdal:amd64.
Preparing to unpack .../13-libroken18-heimdal_7.7.0+dfsg-1ubuntu1_amd64.deb ...
Unpacking libroken18-heimdal:amd64 (7.7.0+dfsg-1ubuntu1) ...
Selecting previously unselected package libasn1-8-heimdal:amd64.
Preparing to unpack .../14-libasn1-8-heimdal_7.7.0+dfsg-1ubuntu1_amd64.deb ...
Unpacking libasn1-8-heimdal:amd64 (7.7.0+dfsg-1ubuntu1) ...
Selecting previously unselected package libheimbase1-heimdal:amd64.
Preparing to unpack .../15-libheimbase1-heimdal_7.7.0+dfsg-1ubuntu1_amd64.deb ...
Unpacking libheimbase1-heimdal:amd64 (7.7.0+dfsg-1ubuntu1) ...
Selecting previously unselected package libhcrypto4-heimdal:amd64.
Preparing to unpack .../16-libhcrypto4-heimdal_7.7.0+dfsg-1ubuntu1_amd64.deb ...
Unpacking libhcrypto4-heimdal:amd64 (7.7.0+dfsg-1ubuntu1) ...
Selecting previously unselected package libwind0-heimdal:amd64.
Preparing to unpack .../17-libwind0-heimdal_7.7.0+dfsg-1ubuntu1_amd64.deb ...
Unpacking libwind0-heimdal:amd64 (7.7.0+dfsg-1ubuntu1) ...
Selecting previously unselected package libhx509-5-heimdal:amd64.
Preparing to unpack .../18-libhx509-5-heimdal_7.7.0+dfsg-1ubuntu1_amd64.deb ...
Unpacking libhx509-5-heimdal:amd64 (7.7.0+dfsg-1ubuntu1) ...
Selecting previously unselected package libkrb5-26-heimdal:amd64.
Preparing to unpack .../19-libkrb5-26-heimdal_7.7.0+dfsg-1ubuntu1_amd64.deb ...
Unpacking libkrb5-26-heimdal:amd64 (7.7.0+dfsg-1ubuntu1) ...
Selecting previously unselected package libheimntlm0-heimdal:amd64.
Preparing to unpack .../20-libheimntlm0-heimdal_7.7.0+dfsg-1ubuntu1_amd64.deb ...
Unpacking libheimntlm0-heimdal:amd64 (7.7.0+dfsg-1ubuntu1) ...
Selecting previously unselected package libgssapi3-heimdal:amd64.
Preparing to unpack .../21-libgssapi3-heimdal_7.7.0+dfsg-1ubuntu1_amd64.deb ...
Unpacking libgssapi3-heimdal:amd64 (7.7.0+dfsg-1ubuntu1) ...
Selecting previously unselected package libsasl2-modules-db:amd64.
Preparing to unpack .../22-libsasl2-modules-db_2.1.27+dfsg-2ubuntu0.1_amd64.deb ...
Unpacking libsasl2-modules-db:amd64 (2.1.27+dfsg-2ubuntu0.1) ...
Selecting previously unselected package libsasl2-2:amd64.
Preparing to unpack .../23-libsasl2-2_2.1.27+dfsg-2ubuntu0.1_amd64.deb ...
Unpacking libsasl2-2:amd64 (2.1.27+dfsg-2ubuntu0.1) ...
Selecting previously unselected package libldap-common.
Preparing to unpack .../24-libldap-common_2.4.49+dfsg-2ubuntu1.9_all.deb ...
Unpacking libldap-common (2.4.49+dfsg-2ubuntu1.9) ...
Selecting previously unselected package libldap-2.4-2:amd64.
Preparing to unpack .../25-libldap-2.4-2_2.4.49+dfsg-2ubuntu1.9_amd64.deb ...
Unpacking libldap-2.4-2:amd64 (2.4.49+dfsg-2ubuntu1.9) ...
Selecting previously unselected package libnghttp2-14:amd64.
Preparing to unpack .../26-libnghttp2-14_1.40.0-1build1_amd64.deb ...
Unpacking libnghttp2-14:amd64 (1.40.0-1build1) ...
Selecting previously unselected package librtmp1:amd64.
Preparing to unpack .../27-librtmp1_2.4+20151223.gitfa8646d.1-2build1_amd64.deb ...
Unpacking librtmp1:amd64 (2.4+20151223.gitfa8646d.1-2build1) ...
Selecting previously unselected package libssh-4:amd64.
Preparing to unpack .../28-libssh-4_0.9.3-2ubuntu2.2_amd64.deb ...
Unpacking libssh-4:amd64 (0.9.3-2ubuntu2.2) ...
Selecting previously unselected package libcurl4:amd64.
Preparing to unpack .../29-libcurl4_7.68.0-1ubuntu2.11_amd64.deb ...
Unpacking libcurl4:amd64 (7.68.0-1ubuntu2.11) ...
Selecting previously unselected package curl.
Preparing to unpack .../30-curl_7.68.0-1ubuntu2.11_amd64.deb ...
Unpacking curl (7.68.0-1ubuntu2.11) ...
Selecting previously unselected package libsasl2-modules:amd64.
Preparing to unpack .../31-libsasl2-modules_2.1.27+dfsg-2ubuntu0.1_amd64.deb ...
Unpacking libsasl2-modules:amd64 (2.1.27+dfsg-2ubuntu0.1) ...
Setting up libkeyutils1:amd64 (1.6-6ubuntu1.1) ...
Setting up libpsl5:amd64 (0.21.0-1ubuntu1) ...
Setting up libssl1.1:amd64 (1.1.1f-1ubuntu2.13) ...
debconf: unable to initialize frontend: Dialog
debconf: (No usable dialog-like program is installed, so the dialog based frontend cannot be used. at /usr/share/perl5/Debconf/FrontEnd/Dialog.pm line 76.)
debconf: falling back to frontend: Readline
debconf: unable to initialize frontend: Readline
debconf: (Can't locate Term/ReadLine.pm in @INC (you may need to install the Term::ReadLine module) (@INC contains: /etc/perl /usr/local/lib/x86_64-linux-gnu/perl/5.30.0 /usr/local/share/perl/5.30.0 /usr/lib/x86_64-linux-gnu/perl5/5.30 /usr/share/perl5 /usr/lib/x86_64-linux-gnu/perl/5.30 /usr/share/perl/5.30 /usr/local/lib/site_perl /usr/lib/x86_64-linux-gnu/perl-base) at /usr/share/perl5/Debconf/FrontEnd/Readline.pm line 7.)
debconf: falling back to frontend: Teletype
Setting up libbrotli1:amd64 (1.0.7-6ubuntu0.1) ...
Setting up libsqlite3-0:amd64 (3.31.1-4ubuntu0.3) ...
Setting up libsasl2-modules:amd64 (2.1.27+dfsg-2ubuntu0.1) ...
Setting up libnghttp2-14:amd64 (1.40.0-1build1) ...
Setting up krb5-locales (1.17-6ubuntu4.1) ...
Setting up libldap-common (2.4.49+dfsg-2ubuntu1.9) ...
Setting up libkrb5support0:amd64 (1.17-6ubuntu4.1) ...
Setting up libsasl2-modules-db:amd64 (2.1.27+dfsg-2ubuntu0.1) ...
Setting up librtmp1:amd64 (2.4+20151223.gitfa8646d.1-2build1) ...
Setting up libk5crypto3:amd64 (1.17-6ubuntu4.1) ...
Setting up libsasl2-2:amd64 (2.1.27+dfsg-2ubuntu0.1) ...
Setting up libroken18-heimdal:amd64 (7.7.0+dfsg-1ubuntu1) ...
Setting up libkrb5-3:amd64 (1.17-6ubuntu4.1) ...
Setting up openssl (1.1.1f-1ubuntu2.13) ...
Setting up publicsuffix (20200303.0012-1) ...
Setting up libheimbase1-heimdal:amd64 (7.7.0+dfsg-1ubuntu1) ...
Setting up libasn1-8-heimdal:amd64 (7.7.0+dfsg-1ubuntu1) ...
Setting up libhcrypto4-heimdal:amd64 (7.7.0+dfsg-1ubuntu1) ...
Setting up ca-certificates (20211016~20.04.1) ...
debconf: unable to initialize frontend: Dialog
debconf: (No usable dialog-like program is installed, so the dialog based frontend cannot be used. at /usr/share/perl5/Debconf/FrontEnd/Dialog.pm line 76.)
debconf: falling back to frontend: Readline
debconf: unable to initialize frontend: Readline
debconf: (Can't locate Term/ReadLine.pm in @INC (you may need to install the Term::ReadLine module) (@INC contains: /etc/perl /usr/local/lib/x86_64-linux-gnu/perl/5.30.0 /usr/local/share/perl/5.30.0 /usr/lib/x86_64-linux-gnu/perl5/5.30 /usr/share/perl5 /usr/lib/x86_64-linux-gnu/perl/5.30 /usr/share/perl/5.30 /usr/local/lib/site_perl /usr/lib/x86_64-linux-gnu/perl-base) at /usr/share/perl5/Debconf/FrontEnd/Readline.pm line 7.)
debconf: falling back to frontend: Teletype
Updating certificates in /etc/ssl/certs...
127 added, 0 removed; done.
Setting up libwind0-heimdal:amd64 (7.7.0+dfsg-1ubuntu1) ...
Setting up libgssapi-krb5-2:amd64 (1.17-6ubuntu4.1) ...
Setting up libssh-4:amd64 (0.9.3-2ubuntu2.2) ...
Setting up libhx509-5-heimdal:amd64 (7.7.0+dfsg-1ubuntu1) ...
Setting up libkrb5-26-heimdal:amd64 (7.7.0+dfsg-1ubuntu1) ...
Setting up libheimntlm0-heimdal:amd64 (7.7.0+dfsg-1ubuntu1) ...
Setting up libgssapi3-heimdal:amd64 (7.7.0+dfsg-1ubuntu1) ...
Setting up libldap-2.4-2:amd64 (2.4.49+dfsg-2ubuntu1.9) ...
Setting up libcurl4:amd64 (7.68.0-1ubuntu2.11) ...
Setting up curl (7.68.0-1ubuntu2.11) ...
Processing triggers for libc-bin (2.31-0ubuntu9.9) ...
Processing triggers for ca-certificates (20211016~20.04.1) ...
Updating certificates in /etc/ssl/certs...
0 added, 0 removed; done.
Running hooks in /etc/ca-certificates/update.d...
done.
root@mimulticontainerpod:/# curl localhost:80
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
root@mimulticontainerpod:/# 
```
# Observability
## Liveness and Readiness Probes
Reference: https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/

### Liveness

Configuraciones que se crean para cuando queremos que un pod se reinicie.

livenessPod.yml
```
apiVersion: v1
kind: Pod
metadata:
  name: milivenesspod
  namespace: paradigma
spec:
  containers:
    - name: nginx
      image: nginx
      livenessProbe:
        exec:
          command:
          - cat
          - /tmp/live
        initialDelaySeconds: 5
        periodSeconds: 5
```
* Se implementa usando el fichero 

```
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get pods -n paradigma -o wide
NAME                  READY   STATUS    RESTARTS      AGE     IP           NODE       NOMINATED NODE   READINESS GATES
miconfigmappod        1/1     Running   6 (89m ago)   2d1h    172.17.0.5   minikube   <none>           <none>
mimulticontainerpod   2/2     Running   2 (53m ago)   3h33m   172.17.0.4   minikube   <none>           <none>
minamespacepod        1/1     Running   9 (53m ago)   2d18h   172.17.0.2   minikube   <none>           <none>
miresourcepod         1/1     Running   4 (53m ago)   6h1m    172.17.0.8   minikube   <none>           <none>
miresourcepod2        1/1     Running   4 (53m ago)   5h47m   172.17.0.7   minikube   <none>           <none>
misecretpod           1/1     Running   6 (89m ago)   2d      172.17.0.3   minikube   <none>           <none>
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl apply -f livenessPod.yml                                   
pod/milivenesspod created
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get pods -n paradigma -o wide
NAME                  READY   STATUS    RESTARTS      AGE     IP           NODE       NOMINATED NODE   READINESS GATES
miconfigmappod        1/1     Running   6 (90m ago)   2d1h    172.17.0.5   minikube   <none>           <none>
milivenesspod         1/1     Running   0             4s      172.17.0.9   minikube   <none>           <none>
mimulticontainerpod   2/2     Running   2 (53m ago)   3h34m   172.17.0.4   minikube   <none>           <none>
minamespacepod        1/1     Running   9 (53m ago)   2d18h   172.17.0.2   minikube   <none>           <none>
miresourcepod         1/1     Running   4 (53m ago)   6h1m    172.17.0.8   minikube   <none>           <none>
miresourcepod2        1/1     Running   4 (53m ago)   5h47m   172.17.0.7   minikube   <none>           <none>
misecretpod           1/1     Running   6 (90m ago)   2d      172.17.0.3   minikube   <none>           <none>
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get pods -n paradigma -w     
NAME                  READY   STATUS    RESTARTS      AGE
miconfigmappod        1/1     Running   6 (90m ago)   2d1h
milivenesspod         1/1     Running   0             17s
mimulticontainerpod   2/2     Running   2 (53m ago)   3h34m
minamespacepod        1/1     Running   9 (53m ago)   2d18h
miresourcepod         1/1     Running   4 (53m ago)   6h2m
miresourcepod2        1/1     Running   4 (53m ago)   5h48m
misecretpod           1/1     Running   6 (90m ago)   2d
milivenesspod         1/1     Running   1 (2s ago)    22s
milivenesspod         1/1     Running   2 (2s ago)    42s
milivenesspod         1/1     Running   3 (2s ago)    62s
milivenesspod         1/1     Running   4 (2s ago)    82s
milivenesspod         0/1     CrashLoopBackOff   4 (0s ago)    100s
milivenesspod         1/1     Running            5 (45s ago)   2m25s
milivenesspod         0/1     CrashLoopBackOff   5 (1s ago)    2m41s
milivenesspod         1/1     Running            6 (82s ago)   4m2s
milivenesspod         0/1     CrashLoopBackOff   6 (0s ago)    4m20s
^[[Cmiresourcepod         0/1     Completed          4 (60m ago)   6h8m
misecretpod           0/1     Completed          6 (97m ago)   2d
minamespacepod        0/1     Completed          9 (60m ago)   2d18h
miresourcepod2        0/1     Completed          4 (60m ago)   5h54m
miconfigmappod        0/1     Completed          6 (97m ago)   2d1h
miconfigmappod        1/1     Running            7 (2s ago)    2d1h
miresourcepod         1/1     Running            5 (2s ago)    6h8m
misecretpod           1/1     Running            7 (2s ago)    2d
miresourcepod2        1/1     Running            5 (3s ago)    5h54m
minamespacepod        1/1     Running            10 (2s ago)   2d18h
milivenesspod         1/1     Running            7 (2m46s ago)   7m6s
^C%                                                                                                                                                                                                           
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl delete pod milivenesspod -n paradigma    
pod "milivenesspod" deleted
```

### Readness

Configuraciones que se crean para cuando queremos que un pod se inactive.

readnessPod.yml
```
apiVersion: v1
kind: Pod
metadata:
  name: mireadinesspod
  namespace: paradigma
spec:
  containers:
    - name: nginx
      image: nginx
      readinessProbe:
        exec:
          command:
          - cat
          - /tmp/readi
        initialDelaySeconds: 5
        periodSeconds: 5
```
* Se despliega un pod con la configuracion readiness.
```
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get pods -n paradigma -o wide        
NAME                  READY   STATUS    RESTARTS       AGE     IP           NODE       NOMINATED NODE   READINESS GATES
miconfigmappod        1/1     Running   7 (16m ago)    2d1h    172.17.0.5   minikube   <none>           <none>
mimulticontainerpod   2/2     Running   2 (77m ago)    3h57m   172.17.0.4   minikube   <none>           <none>
minamespacepod        1/1     Running   10 (16m ago)   2d18h   172.17.0.2   minikube   <none>           <none>
miresourcepod         1/1     Running   5 (16m ago)    6h25m   172.17.0.8   minikube   <none>           <none>
miresourcepod2        1/1     Running   5 (16m ago)    6h11m   172.17.0.7   minikube   <none>           <none>
misecretpod           1/1     Running   7 (16m ago)    2d      172.17.0.3   minikube   <none>           <none>
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl apply -f readnessPod.yml         
error: the path "readnessPod.yml" does not exist
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl apply -f readinessPod.yml
pod/mireadinesspod created
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get pods -n paradigma -o wide
NAME                  READY   STATUS    RESTARTS       AGE     IP           NODE       NOMINATED NODE   READINESS GATES
miconfigmappod        1/1     Running   7 (17m ago)    2d1h    172.17.0.5   minikube   <none>           <none>
mimulticontainerpod   2/2     Running   2 (77m ago)    3h58m   172.17.0.4   minikube   <none>           <none>
minamespacepod        1/1     Running   10 (17m ago)   2d18h   172.17.0.2   minikube   <none>           <none>
mireadinesspod        0/1     Running   0              4s      172.17.0.9   minikube   <none>           <none>
miresourcepod         1/1     Running   5 (17m ago)    6h25m   172.17.0.8   minikube   <none>           <none>
miresourcepod2        1/1     Running   5 (17m ago)    6h11m   172.17.0.7   minikube   <none>           <none>
misecretpod           1/1     Running   7 (17m ago)    2d      172.17.0.3   minikube   <none>           <none>
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get pods -n paradigma -w     
NAME                  READY   STATUS    RESTARTS       AGE
miconfigmappod        1/1     Running   7 (17m ago)    2d1h
mimulticontainerpod   2/2     Running   2 (77m ago)    3h58m
minamespacepod        1/1     Running   10 (17m ago)   2d18h
mireadinesspod        0/1     Running   0              21s
miresourcepod         1/1     Running   5 (17m ago)    6h26m
miresourcepod2        1/1     Running   5 (17m ago)    6h12m
misecretpod           1/1     Running   7 (17m ago)    2d
^C%                                                                                                                                                                                                           
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl exec -it mireadinesspod -n paradigma -- touch/tmp/readi
OCI runtime exec failed: exec failed: container_linux.go:380: starting container process caused: exec: "touch/tmp/readi": stat touch/tmp/readi: no such file or directory: unknown
command terminated with exit code 126
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl exec -it mireadinesspod -n paradigma -- touch /tmp/readi
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get pods -n paradigma -w                               
NAME                  READY   STATUS    RESTARTS       AGE
miconfigmappod        1/1     Running   7 (28m ago)    2d1h
mimulticontainerpod   2/2     Running   2 (88m ago)    4h9m
minamespacepod        1/1     Running   10 (28m ago)   2d18h
mireadinesspod        1/1     Running   0              10m
miresourcepod         1/1     Running   5 (28m ago)    6h36m
miresourcepod2        1/1     Running   5 (28m ago)    6h22m
misecretpod           1/1     Running   7 (28m ago)    2d1h
^C%                                                                                                                                                                                                           
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl exec -it mireadinesspod -n paradigma -- rm /tmp/readi
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get pods -n paradigma -w                             
NAME                  READY   STATUS    RESTARTS       AGE
miconfigmappod        1/1     Running   7 (28m ago)    2d1h
mimulticontainerpod   2/2     Running   2 (89m ago)    4h9m
minamespacepod        1/1     Running   10 (28m ago)   2d18h
mireadinesspod        1/1     Running   0              11m
miresourcepod         1/1     Running   5 (28m ago)    6h37m
miresourcepod2        1/1     Running   5 (28m ago)    6h23m
misecretpod           1/1     Running   7 (28m ago)    2d1h
mireadinesspod        0/1     Running   0              11m
^C%
```

# Container Logging

Reference: https://kubernetes.io/docs/concepts/cluster-administration/logging/