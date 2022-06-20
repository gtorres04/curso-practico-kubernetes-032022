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

## Container Logging

Reference: https://kubernetes.io/docs/concepts/cluster-administration/logging/
logs.yml
```
apiVersion: v1
kind: Pod
metadata:
  name: milogspod
  namespace: paradigma
spec:
  containers:
  - name: ubuntu
    image: ubuntu:18.04
    args: [/bin/sh, -c, 'i=0; while true; do echo "$i: $(date)"; i=$((i+1)); sleep 1; done']
```

* Implementamos el pod del fichero logs.yml

```
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl apply -f logs.yml                                    
pod/milogspod created
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get pods -n paradigma -o wide                        
NAME                  READY   STATUS    RESTARTS       AGE     IP            NODE       NOMINATED NODE   READINESS GATES
miconfigmappod        1/1     Running   9 (17m ago)    3d1h    172.17.0.4    minikube   <none>           <none>
milogspod             1/1     Running   0              18s     172.17.0.10   minikube   <none>           <none>
mimulticontainerpod   2/2     Running   4 (17m ago)    27h     172.17.0.5    minikube   <none>           <none>
minamespacepod        1/1     Running   12 (17m ago)   3d18h   172.17.0.3    minikube   <none>           <none>
mireadinesspod        0/1     Running   1 (17m ago)    23h     172.17.0.7    minikube   <none>           <none>
miresourcepod         1/1     Running   7 (17m ago)    30h     172.17.0.2    minikube   <none>           <none>
miresourcepod2        1/1     Running   7 (17m ago)    29h     172.17.0.6    minikube   <none>           <none>
misecretpod           1/1     Running   9 (17m ago)    3d      172.17.0.9    minikube   <none>           <none>
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl logs milogspod -n paradigma                                
0: Mon Jun 20 15:01:18 UTC 2022
1: Mon Jun 20 15:01:19 UTC 2022
2: Mon Jun 20 15:01:20 UTC 2022
3: Mon Jun 20 15:01:21 UTC 2022
4: Mon Jun 20 15:01:22 UTC 2022
5: Mon Jun 20 15:01:23 UTC 2022
6: Mon Jun 20 15:01:24 UTC 2022
7: Mon Jun 20 15:01:25 UTC 2022
8: Mon Jun 20 15:01:26 UTC 2022
9: Mon Jun 20 15:01:27 UTC 2022
10: Mon Jun 20 15:01:28 UTC 2022
11: Mon Jun 20 15:01:29 UTC 2022
12: Mon Jun 20 15:01:30 UTC 2022
13: Mon Jun 20 15:01:31 UTC 2022
14: Mon Jun 20 15:01:32 UTC 2022
15: Mon Jun 20 15:01:33 UTC 2022
16: Mon Jun 20 15:01:34 UTC 2022
17: Mon Jun 20 15:01:35 UTC 2022
18: Mon Jun 20 15:01:36 UTC 2022
19: Mon Jun 20 15:01:37 UTC 2022
20: Mon Jun 20 15:01:38 UTC 2022
21: Mon Jun 20 15:01:39 UTC 2022
22: Mon Jun 20 15:01:40 UTC 2022
23: Mon Jun 20 15:01:41 UTC 2022
24: Mon Jun 20 15:01:42 UTC 2022
25: Mon Jun 20 15:01:43 UTC 2022
26: Mon Jun 20 15:01:44 UTC 2022
27: Mon Jun 20 15:01:45 UTC 2022
28: Mon Jun 20 15:01:46 UTC 2022
29: Mon Jun 20 15:01:47 UTC 2022
30: Mon Jun 20 15:01:48 UTC 2022
31: Mon Jun 20 15:01:49 UTC 2022
32: Mon Jun 20 15:01:50 UTC 2022
33: Mon Jun 20 15:01:51 UTC 2022
34: Mon Jun 20 15:01:52 UTC 2022
35: Mon Jun 20 15:01:53 UTC 2022
36: Mon Jun 20 15:01:54 UTC 2022
37: Mon Jun 20 15:01:55 UTC 2022
38: Mon Jun 20 15:01:56 UTC 2022
39: Mon Jun 20 15:01:57 UTC 2022
40: Mon Jun 20 15:01:58 UTC 2022
41: Mon Jun 20 15:01:59 UTC 2022
42: Mon Jun 20 15:02:00 UTC 2022
43: Mon Jun 20 15:02:01 UTC 2022
44: Mon Jun 20 15:02:02 UTC 2022
45: Mon Jun 20 15:02:03 UTC 2022
46: Mon Jun 20 15:02:04 UTC 2022
47: Mon Jun 20 15:02:05 UTC 2022
48: Mon Jun 20 15:02:06 UTC 2022
49: Mon Jun 20 15:02:07 UTC 2022
50: Mon Jun 20 15:02:08 UTC 2022
51: Mon Jun 20 15:02:09 UTC 2022
52: Mon Jun 20 15:02:10 UTC 2022
53: Mon Jun 20 15:02:11 UTC 2022
54: Mon Jun 20 15:02:12 UTC 2022
55: Mon Jun 20 15:02:13 UTC 2022
56: Mon Jun 20 15:02:14 UTC 2022
57: Mon Jun 20 15:02:15 UTC 2022
58: Mon Jun 20 15:02:16 UTC 2022
59: Mon Jun 20 15:02:17 UTC 2022
60: Mon Jun 20 15:02:18 UTC 2022
61: Mon Jun 20 15:02:19 UTC 2022
62: Mon Jun 20 15:02:20 UTC 2022
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl logs milogspod -n paradigma
0: Mon Jun 20 15:01:18 UTC 2022
1: Mon Jun 20 15:01:19 UTC 2022
2: Mon Jun 20 15:01:20 UTC 2022
3: Mon Jun 20 15:01:21 UTC 2022
4: Mon Jun 20 15:01:22 UTC 2022
5: Mon Jun 20 15:01:23 UTC 2022
6: Mon Jun 20 15:01:24 UTC 2022
7: Mon Jun 20 15:01:25 UTC 2022
8: Mon Jun 20 15:01:26 UTC 2022
9: Mon Jun 20 15:01:27 UTC 2022
10: Mon Jun 20 15:01:28 UTC 2022
11: Mon Jun 20 15:01:29 UTC 2022
12: Mon Jun 20 15:01:30 UTC 2022
13: Mon Jun 20 15:01:31 UTC 2022
14: Mon Jun 20 15:01:32 UTC 2022
15: Mon Jun 20 15:01:33 UTC 2022
16: Mon Jun 20 15:01:34 UTC 2022
17: Mon Jun 20 15:01:35 UTC 2022
18: Mon Jun 20 15:01:36 UTC 2022
19: Mon Jun 20 15:01:37 UTC 2022
20: Mon Jun 20 15:01:38 UTC 2022
21: Mon Jun 20 15:01:39 UTC 2022
22: Mon Jun 20 15:01:40 UTC 2022
23: Mon Jun 20 15:01:41 UTC 2022
24: Mon Jun 20 15:01:42 UTC 2022
25: Mon Jun 20 15:01:43 UTC 2022
26: Mon Jun 20 15:01:44 UTC 2022
27: Mon Jun 20 15:01:45 UTC 2022
28: Mon Jun 20 15:01:46 UTC 2022
29: Mon Jun 20 15:01:47 UTC 2022
30: Mon Jun 20 15:01:48 UTC 2022
31: Mon Jun 20 15:01:49 UTC 2022
32: Mon Jun 20 15:01:50 UTC 2022
33: Mon Jun 20 15:01:51 UTC 2022
34: Mon Jun 20 15:01:52 UTC 2022
35: Mon Jun 20 15:01:53 UTC 2022
36: Mon Jun 20 15:01:54 UTC 2022
37: Mon Jun 20 15:01:55 UTC 2022
38: Mon Jun 20 15:01:56 UTC 2022
39: Mon Jun 20 15:01:57 UTC 2022
40: Mon Jun 20 15:01:58 UTC 2022
41: Mon Jun 20 15:01:59 UTC 2022
42: Mon Jun 20 15:02:00 UTC 2022
43: Mon Jun 20 15:02:01 UTC 2022
44: Mon Jun 20 15:02:02 UTC 2022
45: Mon Jun 20 15:02:03 UTC 2022
46: Mon Jun 20 15:02:04 UTC 2022
47: Mon Jun 20 15:02:05 UTC 2022
48: Mon Jun 20 15:02:06 UTC 2022
49: Mon Jun 20 15:02:07 UTC 2022
50: Mon Jun 20 15:02:08 UTC 2022
51: Mon Jun 20 15:02:09 UTC 2022
52: Mon Jun 20 15:02:10 UTC 2022
53: Mon Jun 20 15:02:11 UTC 2022
54: Mon Jun 20 15:02:12 UTC 2022
55: Mon Jun 20 15:02:13 UTC 2022
56: Mon Jun 20 15:02:14 UTC 2022
57: Mon Jun 20 15:02:15 UTC 2022
58: Mon Jun 20 15:02:16 UTC 2022
59: Mon Jun 20 15:02:17 UTC 2022
60: Mon Jun 20 15:02:18 UTC 2022
61: Mon Jun 20 15:02:19 UTC 2022
62: Mon Jun 20 15:02:20 UTC 2022
63: Mon Jun 20 15:02:21 UTC 2022
64: Mon Jun 20 15:02:22 UTC 2022
65: Mon Jun 20 15:02:23 UTC 2022
66: Mon Jun 20 15:02:24 UTC 2022
67: Mon Jun 20 15:02:25 UTC 2022
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl logs mimulticontainerpod -c nginx -n paradigma
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2022/06/20 14:44:09 [notice] 1#1: using the "epoll" event method
2022/06/20 14:44:09 [notice] 1#1: nginx/1.21.6
2022/06/20 14:44:09 [notice] 1#1: built by gcc 10.2.1 20210110 (Debian 10.2.1-6) 
2022/06/20 14:44:09 [notice] 1#1: OS: Linux 5.10.104-linuxkit
2022/06/20 14:44:09 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2022/06/20 14:44:09 [notice] 1#1: start worker processes
2022/06/20 14:44:09 [notice] 1#1: start worker process 31
2022/06/20 14:44:09 [notice] 1#1: start worker process 32
2022/06/20 14:44:09 [notice] 1#1: start worker process 33
2022/06/20 14:44:09 [notice] 1#1: start worker process 34
2022/06/20 14:44:09 [notice] 1#1: start worker process 35
2022/06/20 14:44:09 [notice] 1#1: start worker process 36
2022/06/20 14:44:09 [notice] 1#1: start worker process 37
2022/06/20 14:44:09 [notice] 1#1: start worker process 38
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl describe pod milogspod -n paradigma     
Name:         milogspod
Namespace:    paradigma
Priority:     0
Node:         minikube/192.168.49.2
Start Time:   Mon, 20 Jun 2022 17:01:11 +0200
Labels:       <none>
Annotations:  <none>
Status:       Running
IP:           172.17.0.10
IPs:
  IP:  172.17.0.10
Containers:
  ubuntu:
    Container ID:  docker://4e3fc6e0abf87fd304fdf490f829aa7cf565d5973a28d47b4d4604b86ca6cc00
    Image:         ubuntu:18.04
    Image ID:      docker-pullable://ubuntu@sha256:478caf1bec1afd54a58435ec681c8755883b7eb843a8630091890130b15a79af
    Port:          <none>
    Host Port:     <none>
    Args:
      /bin/sh
      -c
      i=0; while true; do echo "$i: $(date)"; i=$((i+1)); sleep 1; done
    State:          Running
      Started:      Mon, 20 Jun 2022 17:01:18 +0200
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-rppng (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  kube-api-access-rppng:
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
  Type    Reason     Age    From               Message
  ----    ------     ----   ----               -------
  Normal  Scheduled  7m47s  default-scheduler  Successfully assigned paradigma/milogspod to minikube
  Normal  Pulling    7m47s  kubelet            Pulling image "ubuntu:18.04"
  Normal  Pulled     7m40s  kubelet            Successfully pulled image "ubuntu:18.04" in 6.359554253s
  Normal  Created    7m40s  kubelet            Created container ubuntu
  Normal  Started    7m40s  kubelet            Started container ubuntu
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get events -n paradigma
LAST SEEN   TYPE      REASON           OBJECT                    MESSAGE
23h         Normal    Pulled           pod/miconfigmappod        Container image "ubuntu:20.04" already present on machine
23h         Normal    Created          pod/miconfigmappod        Created container ubuntu
23h         Normal    Started          pod/miconfigmappod        Started container ubuntu
23h         Warning   NodeNotReady     pod/miconfigmappod        Node is not ready
22h         Warning   FailedMount      pod/miconfigmappod        MountVolume.SetUp failed for volume "kube-api-access-sv8tb" : failed to sync configmap cache: timed out waiting for the condition
27m         Normal    SandboxChanged   pod/miconfigmappod        Pod sandbox changed, it will be killed and re-created.
27m         Normal    Pulled           pod/miconfigmappod        Container image "ubuntu:20.04" already present on machine
27m         Normal    Created          pod/miconfigmappod        Created container ubuntu
27m         Normal    Started          pod/miconfigmappod        Started container ubuntu
10m         Normal    Scheduled        pod/milogspod             Successfully assigned paradigma/milogspod to minikube
10m         Normal    Pulling          pod/milogspod             Pulling image "ubuntu:18.04"
10m         Normal    Pulled           pod/milogspod             Successfully pulled image "ubuntu:18.04" in 6.359554253s
10m         Normal    Created          pod/milogspod             Created container ubuntu
10m         Normal    Started          pod/milogspod             Started container ubuntu
23h         Warning   NodeNotReady     pod/mimulticontainerpod   Node is not ready
22h         Warning   FailedMount      pod/mimulticontainerpod   MountVolume.SetUp failed for volume "kube-api-access-8578v" : failed to sync configmap cache: timed out waiting for the condition
27m         Normal    SandboxChanged   pod/mimulticontainerpod   Pod sandbox changed, it will be killed and re-created.
27m         Normal    Pulling          pod/mimulticontainerpod   Pulling image "nginx:latest"
27m         Normal    Pulled           pod/mimulticontainerpod   Successfully pulled image "nginx:latest" in 2.454432263s
27m         Normal    Created          pod/mimulticontainerpod   Created container nginx
27m         Normal    Started          pod/mimulticontainerpod   Started container nginx
27m         Normal    Pulled           pod/mimulticontainerpod   Container image "ubuntu:20.04" already present on machine
27m         Normal    Created          pod/mimulticontainerpod   Created container ubuntu
27m         Normal    Started          pod/mimulticontainerpod   Started container ubuntu
23h         Normal    Pulling          pod/minamespacepod        Pulling image "busybox:latest"
23h         Normal    Created          pod/minamespacepod        Created container busybox
23h         Normal    Started          pod/minamespacepod        Started container busybox
23h         Warning   NodeNotReady     pod/minamespacepod        Node is not ready
23h         Normal    Pulled           pod/minamespacepod        Successfully pulled image "busybox:latest" in 1.752896933s
22h         Warning   FailedMount      pod/minamespacepod        MountVolume.SetUp failed for volume "kube-api-access-ndnjx" : failed to sync configmap cache: timed out waiting for the condition
27m         Normal    SandboxChanged   pod/minamespacepod        Pod sandbox changed, it will be killed and re-created.
27m         Normal    Pulling          pod/minamespacepod        Pulling image "busybox:latest"
27m         Normal    Pulled           pod/minamespacepod        Successfully pulled image "busybox:latest" in 2.930356986s
27m         Normal    Created          pod/minamespacepod        Created container busybox
27m         Normal    Started          pod/minamespacepod        Started container busybox
22h         Warning   Unhealthy        pod/mireadinesspod        Readiness probe failed: cat: /tmp/readi: No such file or directory
27m         Normal    SandboxChanged   pod/mireadinesspod        Pod sandbox changed, it will be killed and re-created.
27m         Normal    Pulling          pod/mireadinesspod        Pulling image "nginx"
27m         Normal    Pulled           pod/mireadinesspod        Successfully pulled image "nginx" in 3.834753721s
27m         Normal    Created          pod/mireadinesspod        Created container nginx
27m         Normal    Started          pod/mireadinesspod        Started container nginx
2m21s       Warning   Unhealthy        pod/mireadinesspod        Readiness probe failed: cat: /tmp/readi: No such file or directory
23h         Normal    Pulled           pod/miresourcepod         Container image "ubuntu:20.04" already present on machine
23h         Normal    Created          pod/miresourcepod         Created container ubuntu
23h         Normal    Started          pod/miresourcepod         Started container ubuntu
23h         Warning   NodeNotReady     pod/miresourcepod         Node is not ready
22h         Warning   FailedMount      pod/miresourcepod         MountVolume.SetUp failed for volume "kube-api-access-5mw6l" : failed to sync configmap cache: timed out waiting for the condition
27m         Normal    SandboxChanged   pod/miresourcepod         Pod sandbox changed, it will be killed and re-created.
27m         Normal    Pulled           pod/miresourcepod         Container image "ubuntu:20.04" already present on machine
27m         Normal    Created          pod/miresourcepod         Created container ubuntu
27m         Normal    Started          pod/miresourcepod         Started container ubuntu
23h         Normal    Pulled           pod/miresourcepod2        Container image "ubuntu:20.04" already present on machine
23h         Normal    Created          pod/miresourcepod2        Created container ubuntu
23h         Normal    Started          pod/miresourcepod2        Started container ubuntu
23h         Warning   NodeNotReady     pod/miresourcepod2        Node is not ready
22h         Warning   FailedMount      pod/miresourcepod2        MountVolume.SetUp failed for volume "kube-api-access-xwjcj" : failed to sync configmap cache: timed out waiting for the condition
27m         Normal    SandboxChanged   pod/miresourcepod2        Pod sandbox changed, it will be killed and re-created.
27m         Normal    Pulled           pod/miresourcepod2        Container image "ubuntu:20.04" already present on machine
27m         Normal    Created          pod/miresourcepod2        Created container ubuntu
27m         Normal    Started          pod/miresourcepod2        Started container ubuntu
23h         Normal    Pulled           pod/misecretpod           Container image "ubuntu:20.04" already present on machine
23h         Normal    Created          pod/misecretpod           Created container ubuntu
23h         Normal    Started          pod/misecretpod           Started container ubuntu
23h         Warning   NodeNotReady     pod/misecretpod           Node is not ready
22h         Warning   FailedMount      pod/misecretpod           MountVolume.SetUp failed for volume "kube-api-access-tvqgc" : failed to sync configmap cache: timed out waiting for the condition
27m         Normal    SandboxChanged   pod/misecretpod           Pod sandbox changed, it will be killed and re-created.
27m         Normal    Pulled           pod/misecretpod           Container image "ubuntu:20.04" already present on machine
27m         Normal    Created          pod/misecretpod           Created container ubuntu
27m         Normal    Started          pod/misecretpod           Started container ubuntu
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get events -n paradigma | grep SandboxChanged
28m         Normal    SandboxChanged   pod/miconfigmappod        Pod sandbox changed, it will be killed and re-created.
28m         Normal    SandboxChanged   pod/mimulticontainerpod   Pod sandbox changed, it will be killed and re-created.
28m         Normal    SandboxChanged   pod/minamespacepod        Pod sandbox changed, it will be killed and re-created.
28m         Normal    SandboxChanged   pod/mireadinesspod        Pod sandbox changed, it will be killed and re-created.
28m         Normal    SandboxChanged   pod/miresourcepod         Pod sandbox changed, it will be killed and re-created.
28m         Normal    SandboxChanged   pod/miresourcepod2        Pod sandbox changed, it will be killed and re-created.
28m         Normal    SandboxChanged   pod/misecretpod           Pod sandbox changed, it will be killed and re-created.
```

## Monitoring Aplications

Reference: https://kubernetes.io/docs/tasks/debug-application-cluster/resource-usage-monitoring/

* Activar el servidor de metricas de minikube

```
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml %  minikube addons enable metrics-server
    ▪ Using image k8s.gcr.io/metrics-server/metrics-server:v0.4.2
🌟  The 'metrics-server' addon is enabled
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get ns
NAME              STATUS   AGE
default           Active   4d18h
kube-node-lease   Active   4d18h
kube-public       Active   4d18h
kube-system       Active   4d18h
paradigma         Active   3d18h
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get pods -n kube-system
NAME                               READY   STATUS    RESTARTS       AGE
coredns-64897985d-d8vjh            1/1     Running   5 (36m ago)    4d18h
etcd-minikube                      1/1     Running   5 (36m ago)    4d18h
kube-apiserver-minikube            1/1     Running   5 (36m ago)    4d18h
kube-controller-manager-minikube   1/1     Running   5 (36m ago)    4d18h
kube-proxy-8tq68                   1/1     Running   5 (36m ago)    4d18h
kube-scheduler-minikube            1/1     Running   5 (36m ago)    4d18h
metrics-server-6b76bd68b6-rk6fs    1/1     Running   0              81s
storage-provisioner                1/1     Running   15 (35m ago)   4d18h
```

resourceConsumerBig.yml
```
apiVersion: v1
kind: Pod
metadata:
  name: miresourceconsumerbig
  namespace: paradigma
spec:
  containers:
  - name: resourceconsumer
    image: gcr.io/kubernetes-e2e-test-images/resource-consumer:1.4
    resources:
      requests:
        cpu: 500m
        memory: 128Mi
  - name: busybox-sidecar
    image: radial/busyboxplus:curl
    command: [/bin/sh, -c, 'until curl localhost:8080/ConsumeCPU -d "millicores=300&durationSec=3600"; do sleep 5; done && sleep 3700']
```

resourceConsumerSmall.yml
```
apiVersion: v1
kind: Pod
metadata:
  name: miresourceconsumersmall
  namespace: paradigma
spec:
  containers:
  - name: resourceconsumer
    image: gcr.io/kubernetes-e2e-test-images/resource-consumer:1.4
    resources:
      requests:
        cpu: 500m
        memory: 128Mi
  - name: busybox-sidecar
    image: radial/busyboxplus:curl
    command: [/bin/sh, -c, 'until curl localhost:8080/ConsumeCPU -d "millicores=100&durationSec=3600"; do sleep 5; done && sleep 3700']
```
* Se implementan los dos ficheros anteriores y se consulta el monitor
```
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get pods -n paradigma -o wide           
NAME                  READY   STATUS    RESTARTS       AGE     IP            NODE       NOMINATED NODE   READINESS GATES
miconfigmappod        1/1     Running   9 (42m ago)    3d1h    172.17.0.4    minikube   <none>           <none>
milogspod             1/1     Running   0              24m     172.17.0.10   minikube   <none>           <none>
mimulticontainerpod   2/2     Running   4 (42m ago)    28h     172.17.0.5    minikube   <none>           <none>
minamespacepod        1/1     Running   12 (42m ago)   3d18h   172.17.0.3    minikube   <none>           <none>
mireadinesspod        0/1     Running   1 (42m ago)    24h     172.17.0.7    minikube   <none>           <none>
miresourcepod         1/1     Running   7 (42m ago)    30h     172.17.0.2    minikube   <none>           <none>
miresourcepod2        1/1     Running   7 (42m ago)    30h     172.17.0.6    minikube   <none>           <none>
misecretpod           1/1     Running   9 (42m ago)    3d      172.17.0.9    minikube   <none>           <none>
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl apply -f resourceConsumerBig.yml        
pod/miresourceconsumerbig created
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get pods -n paradigma -o wide   
NAME                    READY   STATUS              RESTARTS       AGE     IP            NODE       NOMINATED NODE   READINESS GATES
miconfigmappod          1/1     Running             9 (42m ago)    3d1h    172.17.0.4    minikube   <none>           <none>
milogspod               1/1     Running             0              25m     172.17.0.10   minikube   <none>           <none>
mimulticontainerpod     2/2     Running             4 (42m ago)    28h     172.17.0.5    minikube   <none>           <none>
minamespacepod          1/1     Running             12 (42m ago)   3d18h   172.17.0.3    minikube   <none>           <none>
mireadinesspod          0/1     Running             1 (42m ago)    24h     172.17.0.7    minikube   <none>           <none>
miresourceconsumerbig   0/2     ContainerCreating   0              3s      <none>        minikube   <none>           <none>
miresourcepod           1/1     Running             7 (42m ago)    30h     172.17.0.2    minikube   <none>           <none>
miresourcepod2          1/1     Running             7 (42m ago)    30h     172.17.0.6    minikube   <none>           <none>
misecretpod             1/1     Running             9 (42m ago)    3d      172.17.0.9    minikube   <none>           <none>
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get pods -n paradigma -o wide
NAME                    READY   STATUS    RESTARTS       AGE     IP            NODE       NOMINATED NODE   READINESS GATES
miconfigmappod          1/1     Running   9 (42m ago)    3d1h    172.17.0.4    minikube   <none>           <none>
milogspod               1/1     Running   0              25m     172.17.0.10   minikube   <none>           <none>
mimulticontainerpod     2/2     Running   4 (42m ago)    28h     172.17.0.5    minikube   <none>           <none>
minamespacepod          1/1     Running   12 (42m ago)   3d18h   172.17.0.3    minikube   <none>           <none>
mireadinesspod          0/1     Running   1 (42m ago)    24h     172.17.0.7    minikube   <none>           <none>
miresourceconsumerbig   2/2     Running   0              19s     172.17.0.12   minikube   <none>           <none>
miresourcepod           1/1     Running   7 (42m ago)    30h     172.17.0.2    minikube   <none>           <none>
miresourcepod2          1/1     Running   7 (42m ago)    30h     172.17.0.6    minikube   <none>           <none>
misecretpod             1/1     Running   9 (42m ago)    3d      172.17.0.9    minikube   <none>           <none>
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl apply -f resourceConsumerSmall.yml
pod/miresourceconsumersmall created
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get pods -n paradigma -o wide     
NAME                      READY   STATUS    RESTARTS       AGE     IP            NODE       NOMINATED NODE   READINESS GATES
miconfigmappod            1/1     Running   9 (44m ago)    3d1h    172.17.0.4    minikube   <none>           <none>
milogspod                 1/1     Running   0              26m     172.17.0.10   minikube   <none>           <none>
mimulticontainerpod       2/2     Running   4 (44m ago)    28h     172.17.0.5    minikube   <none>           <none>
minamespacepod            1/1     Running   12 (44m ago)   3d18h   172.17.0.3    minikube   <none>           <none>
mireadinesspod            0/1     Running   1 (44m ago)    24h     172.17.0.7    minikube   <none>           <none>
miresourceconsumerbig     2/2     Running   0              91s     172.17.0.12   minikube   <none>           <none>
miresourceconsumersmall   2/2     Running   0              11s     172.17.0.13   minikube   <none>           <none>
miresourcepod             1/1     Running   7 (44m ago)    30h     172.17.0.2    minikube   <none>           <none>
miresourcepod2            1/1     Running   7 (44m ago)    30h     172.17.0.6    minikube   <none>           <none>
misecretpod               1/1     Running   9 (44m ago)    3d      172.17.0.9    minikube   <none>           <none>
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl top pods -n paradigma -o wide
error: unknown shorthand flag: 'o' in -o
See 'kubectl top pod --help' for usage.
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl top pods -n paradigma        
NAME                      CPU(cores)   MEMORY(bytes)   
miconfigmappod            0m           0Mi             
milogspod                 0m           0Mi             
mimulticontainerpod       0m           10Mi            
minamespacepod            0m           0Mi             
mireadinesspod            0m           6Mi             
miresourceconsumerbig     0m           1Mi             
miresourceconsumersmall   0m           1Mi             
miresourcepod             0m           1Mi             
miresourcepod2            0m           0Mi             
misecretpod               0m           0Mi             
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl top pods -n paradigma
NAME                      CPU(cores)   MEMORY(bytes)   
miconfigmappod            0m           0Mi             
milogspod                 0m           0Mi             
mimulticontainerpod       0m           10Mi            
minamespacepod            0m           0Mi             
mireadinesspod            0m           6Mi             
miresourceconsumerbig     0m           1Mi             
miresourceconsumersmall   0m           1Mi             
miresourcepod             0m           1Mi             
miresourcepod2            0m           0Mi             
misecretpod               0m           0Mi             
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl top pods -n paradigma
NAME                      CPU(cores)   MEMORY(bytes)   
miconfigmappod            0m           0Mi             
milogspod                 0m           0Mi             
mimulticontainerpod       0m           10Mi            
minamespacepod            0m           0Mi             
mireadinesspod            0m           6Mi             
miresourceconsumerbig     0m           1Mi             
miresourceconsumersmall   0m           1Mi             
miresourcepod             0m           1Mi             
miresourcepod2            0m           0Mi             
misecretpod               0m           0Mi             
```
# Pod Design

## Labels
reference: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/

labelProduccionPod.yml
```
apiVersion: v1
kind: Pod
metadata:
  name: milabelproduccionpod
  namespace: paradigma
  labels:
    app: miapp
    entorno: produccion
spec:
  containers:
  - name: nginx
    image: nginx
```
labelDesarrolloPod.yml
```
apiVersion: v1
kind: Pod
metadata:
  name: milabeldesarrollopod
  namespace: paradigma
  labels:
    app: miapp
    entorno: desarrollo
spec:
  containers:
  - name: nginx
    image: nginx
```
* implementamos los pod de los ficheros anteriores

```
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl apply -f labelProduccionPod.yml
pod/milabelproduccionpod created
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl apply -f labelDesarrolloPod.yml
pod/milabeldesarrollopod created
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get pods -n paradigma          
NAME                      READY   STATUS    RESTARTS       AGE
miconfigmappod            1/1     Running   10 (26m ago)   3d3h
milabeldesarrollopod      1/1     Running   0              17s
milabelproduccionpod      1/1     Running   0              27s
milogspod                 1/1     Running   1 (26m ago)    132m
mimulticontainerpod       2/2     Running   6 (26m ago)    29h
minamespacepod            1/1     Running   13 (26m ago)   3d20h
mireadinesspod            0/1     Running   2 (26m ago)    25h
miresourceconsumerbig     2/2     Running   2 (26m ago)    106m
miresourceconsumersmall   2/2     Running   2 (26m ago)    105m
miresourcepod             1/1     Running   8 (26m ago)    32h
miresourcepod2            1/1     Running   8 (26m ago)    32h
misecretpod               1/1     Running   10 (26m ago)   3d2h
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get pods -n paradigma -o wide
NAME                      READY   STATUS    RESTARTS       AGE     IP            NODE       NOMINATED NODE   READINESS GATES
miconfigmappod            1/1     Running   10 (27m ago)   3d3h    172.17.0.3    minikube   <none>           <none>
milabeldesarrollopod      1/1     Running   0              99s     172.17.0.15   minikube   <none>           <none>
milabelproduccionpod      1/1     Running   0              109s    172.17.0.14   minikube   <none>           <none>
milogspod                 1/1     Running   1 (27m ago)    133m    172.17.0.13   minikube   <none>           <none>
mimulticontainerpod       2/2     Running   6 (27m ago)    29h     172.17.0.10   minikube   <none>           <none>
minamespacepod            1/1     Running   13 (27m ago)   3d20h   172.17.0.7    minikube   <none>           <none>
mireadinesspod            0/1     Running   2 (27m ago)    25h     172.17.0.4    minikube   <none>           <none>
miresourceconsumerbig     2/2     Running   2 (27m ago)    108m    172.17.0.6    minikube   <none>           <none>
miresourceconsumersmall   2/2     Running   2 (27m ago)    106m    172.17.0.5    minikube   <none>           <none>
miresourcepod             1/1     Running   8 (27m ago)    32h     172.17.0.12   minikube   <none>           <none>
miresourcepod2            1/1     Running   8 (27m ago)    32h     172.17.0.2    minikube   <none>           <none>
misecretpod               1/1     Running   10 (27m ago)   3d2h    172.17.0.11   minikube   <none>           <none>
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get pods -n paradigma -o wide --show-label
error: unknown flag: --show-label
See 'kubectl get --help' for usage.
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get pods -n paradigma -o wide --show-labels
NAME                      READY   STATUS    RESTARTS       AGE     IP            NODE       NOMINATED NODE   READINESS GATES   LABELS
miconfigmappod            1/1     Running   10 (27m ago)   3d3h    172.17.0.3    minikube   <none>           <none>            <none>
milabeldesarrollopod      1/1     Running   0              116s    172.17.0.15   minikube   <none>           <none>            app=miapp,entorno=desarrollo
milabelproduccionpod      1/1     Running   0              2m6s    172.17.0.14   minikube   <none>           <none>            app=miapp,entorno=produccion
milogspod                 1/1     Running   1 (27m ago)    133m    172.17.0.13   minikube   <none>           <none>            <none>
mimulticontainerpod       2/2     Running   6 (27m ago)    29h     172.17.0.10   minikube   <none>           <none>            <none>
minamespacepod            1/1     Running   13 (27m ago)   3d20h   172.17.0.7    minikube   <none>           <none>            <none>
mireadinesspod            0/1     Running   2 (27m ago)    25h     172.17.0.4    minikube   <none>           <none>            <none>
miresourceconsumerbig     2/2     Running   2 (27m ago)    108m    172.17.0.6    minikube   <none>           <none>            <none>
miresourceconsumersmall   2/2     Running   2 (27m ago)    107m    172.17.0.5    minikube   <none>           <none>            <none>
miresourcepod             1/1     Running   8 (27m ago)    32h     172.17.0.12   minikube   <none>           <none>            <none>
miresourcepod2            1/1     Running   8 (27m ago)    32h     172.17.0.2    minikube   <none>           <none>            <none>
misecretpod               1/1     Running   10 (27m ago)   3d2h    172.17.0.11   minikube   <none>           <none>            <none>
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % 
```

## Selectors

reference: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/

```
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get pods -n paradigma -l app=miapp
NAME                   READY   STATUS    RESTARTS   AGE
milabeldesarrollopod   1/1     Running   0          9m14s
milabelproduccionpod   1/1     Running   0          9m24s
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get pods -n paradigma -l app=miapp -o wide --show-labels
NAME                   READY   STATUS    RESTARTS   AGE     IP            NODE       NOMINATED NODE   READINESS GATES   LABELS
milabeldesarrollopod   1/1     Running   0          9m32s   172.17.0.15   minikube   <none>           <none>            app=miapp,entorno=desarrollo
milabelproduccionpod   1/1     Running   0          9m42s   172.17.0.14   minikube   <none>           <none>            app=miapp,entorno=produccion
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get pods -n paradigma -l app=miapp,entorno=desarrollo -o wide --show-labels
NAME                   READY   STATUS    RESTARTS   AGE     IP            NODE       NOMINATED NODE   READINESS GATES   LABELS
milabeldesarrollopod   1/1     Running   0          9m49s   172.17.0.15   minikube   <none>           <none>            app=miapp,entorno=desarrollo
```

## Annotations

reference: https://kubernetes.io/docs/concepts/overview/working-with-objects/annotations/

annotationPod.yml
```
apiVersion: v1
kind: Pod
metadata:
  name: miannotationpod
  namespace: paradigma
  annotations:
    owner: people@paradigmadigital.com
    empresa: Paradigma
spec:
  containers:
  - name: nginx
    image: nginx
```
* Implementamos el pod del fichero anterior

```
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get pods -n paradigma                                                      
NAME                      READY   STATUS    RESTARTS       AGE
miconfigmappod            1/1     Running   10 (40m ago)   3d3h
milabeldesarrollopod      1/1     Running   0              14m
milabelproduccionpod      1/1     Running   0              14m
milogspod                 1/1     Running   1 (40m ago)    146m
mimulticontainerpod       2/2     Running   6 (40m ago)    30h
minamespacepod            1/1     Running   13 (40m ago)   3d20h
mireadinesspod            0/1     Running   2 (40m ago)    26h
miresourceconsumerbig     2/2     Running   2 (40m ago)    121m
miresourceconsumersmall   2/2     Running   2 (40m ago)    120m
miresourcepod             1/1     Running   8 (40m ago)    32h
miresourcepod2            1/1     Running   8 (40m ago)    32h
misecretpod               1/1     Running   10 (40m ago)   3d2h
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl apply -f annotationPod.yml                                                 
pod/miannotationpod created
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get pods -n paradigma     
NAME                      READY   STATUS              RESTARTS       AGE
miannotationpod           0/1     ContainerCreating   0              2s
miconfigmappod            1/1     Running             10 (41m ago)   3d3h
milabeldesarrollopod      1/1     Running             0              15m
milabelproduccionpod      1/1     Running             0              15m
milogspod                 1/1     Running             1 (41m ago)    146m
mimulticontainerpod       2/2     Running             6 (41m ago)    30h
minamespacepod            1/1     Running             13 (41m ago)   3d20h
mireadinesspod            0/1     Running             2 (41m ago)    26h
miresourceconsumerbig     2/2     Running             2 (41m ago)    121m
miresourceconsumersmall   2/2     Running             2 (41m ago)    120m
miresourcepod             1/1     Running             8 (41m ago)    32h
miresourcepod2            1/1     Running             8 (41m ago)    32h
misecretpod               1/1     Running             10 (41m ago)   3d2h
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get pods -n paradigma -o wide --show-labels                   
NAME                      READY   STATUS    RESTARTS       AGE     IP            NODE       NOMINATED NODE   READINESS GATES   LABELS
miannotationpod           1/1     Running   0              16s     172.17.0.16   minikube   <none>           <none>            <none>
miconfigmappod            1/1     Running   10 (41m ago)   3d3h    172.17.0.3    minikube   <none>           <none>            <none>
milabeldesarrollopod      1/1     Running   0              15m     172.17.0.15   minikube   <none>           <none>            app=miapp,entorno=desarrollo
milabelproduccionpod      1/1     Running   0              15m     172.17.0.14   minikube   <none>           <none>            app=miapp,entorno=produccion
milogspod                 1/1     Running   1 (41m ago)    147m    172.17.0.13   minikube   <none>           <none>            <none>
mimulticontainerpod       2/2     Running   6 (41m ago)    30h     172.17.0.10   minikube   <none>           <none>            <none>
minamespacepod            1/1     Running   13 (41m ago)   3d20h   172.17.0.7    minikube   <none>           <none>            <none>
mireadinesspod            0/1     Running   2 (41m ago)    26h     172.17.0.4    minikube   <none>           <none>            <none>
miresourceconsumerbig     2/2     Running   2 (41m ago)    121m    172.17.0.6    minikube   <none>           <none>            <none>
miresourceconsumersmall   2/2     Running   2 (41m ago)    120m    172.17.0.5    minikube   <none>           <none>            <none>
miresourcepod             1/1     Running   8 (41m ago)    32h     172.17.0.12   minikube   <none>           <none>            <none>
miresourcepod2            1/1     Running   8 (41m ago)    32h     172.17.0.2    minikube   <none>           <none>            <none>
misecretpod               1/1     Running   10 (41m ago)   3d3h    172.17.0.11   minikube   <none>           <none>            <none>
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl describe miannotationpod -n paradigma
error: the server doesn't have a resource type "miannotationpod"
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl describe pod miannotationpod -n paradigma
Name:         miannotationpod
Namespace:    paradigma
Priority:     0
Node:         minikube/192.168.49.2
Start Time:   Mon, 20 Jun 2022 19:28:02 +0200
Labels:       <none>
Annotations:  empresa: Paradigma
              owner: people@paradigmadigital.com
Status:       Running
IP:           172.17.0.16
IPs:
  IP:  172.17.0.16
Containers:
  nginx:
    Container ID:   docker://8bf0b6261c7c0935b703ac1a1ce11fde73d133318ef05e6ccf09bf0d2664b3b1
    Image:          nginx
    Image ID:       docker-pullable://nginx@sha256:2bcabc23b45489fb0885d69a06ba1d648aeda973fae7bb981bafbb884165e514
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Mon, 20 Jun 2022 19:28:04 +0200
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-sfkhz (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  kube-api-access-sfkhz:
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
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  58s   default-scheduler  Successfully assigned paradigma/miannotationpod to minikube
  Normal  Pulling    58s   kubelet            Pulling image "nginx"
  Normal  Pulled     56s   kubelet            Successfully pulled image "nginx" in 1.702404652s
  Normal  Created    56s   kubelet            Created container nginx
  Normal  Started    56s   kubelet            Started container nginx
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % 
```
## Deployment

reference: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/

deployment.yml
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mideployment
  namespace: paradigma
spec:
  strategy:
    type: RollingUpdate
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - image: nginx
          name: nginx
          ports:
          - containerPort: 80
```
* Se implementa el fichero anterior

```
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get pods -n paradigma -o wide --show-labels
NAME                      READY   STATUS    RESTARTS       AGE     IP            NODE       NOMINATED NODE   READINESS GATES   LABELS
miannotationpod           1/1     Running   0              16m     172.17.0.16   minikube   <none>           <none>            <none>
miconfigmappod            1/1     Running   10 (57m ago)   3d3h    172.17.0.3    minikube   <none>           <none>            <none>
milabeldesarrollopod      1/1     Running   0              31m     172.17.0.15   minikube   <none>           <none>            app=miapp,entorno=desarrollo
milabelproduccionpod      1/1     Running   0              31m     172.17.0.14   minikube   <none>           <none>            app=miapp,entorno=produccion
milogspod                 1/1     Running   1 (57m ago)    162m    172.17.0.13   minikube   <none>           <none>            <none>
mimulticontainerpod       2/2     Running   6 (57m ago)    30h     172.17.0.10   minikube   <none>           <none>            <none>
minamespacepod            1/1     Running   13 (57m ago)   3d20h   172.17.0.7    minikube   <none>           <none>            <none>
mireadinesspod            0/1     Running   2 (57m ago)    26h     172.17.0.4    minikube   <none>           <none>            <none>
miresourceconsumerbig     2/2     Running   2 (57m ago)    137m    172.17.0.6    minikube   <none>           <none>            <none>
miresourceconsumersmall   2/2     Running   2 (57m ago)    136m    172.17.0.5    minikube   <none>           <none>            <none>
miresourcepod             1/1     Running   8 (57m ago)    32h     172.17.0.12   minikube   <none>           <none>            <none>
miresourcepod2            1/1     Running   8 (57m ago)    32h     172.17.0.2    minikube   <none>           <none>            <none>
misecretpod               1/1     Running   10 (57m ago)   3d3h    172.17.0.11   minikube   <none>           <none>            <none>
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl apply -f deployment.yml                    
deployment.apps/mideployment created
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get pods -n paradigma -o wide --show-labels
NAME                            READY   STATUS              RESTARTS       AGE     IP            NODE       NOMINATED NODE   READINESS GATES   LABELS
miannotationpod                 1/1     Running             0              16m     172.17.0.16   minikube   <none>           <none>            <none>
miconfigmappod                  1/1     Running             10 (57m ago)   3d3h    172.17.0.3    minikube   <none>           <none>            <none>
mideployment-74d589986c-gvxdq   0/1     ContainerCreating   0              5s      <none>        minikube   <none>           <none>            app=nginx,pod-template-hash=74d589986c
mideployment-74d589986c-tpzhp   1/1     Running             0              5s      172.17.0.18   minikube   <none>           <none>            app=nginx,pod-template-hash=74d589986c
mideployment-74d589986c-vl6kc   1/1     Running             0              5s      172.17.0.17   minikube   <none>           <none>            app=nginx,pod-template-hash=74d589986c
milabeldesarrollopod            1/1     Running             0              31m     172.17.0.15   minikube   <none>           <none>            app=miapp,entorno=desarrollo
milabelproduccionpod            1/1     Running             0              31m     172.17.0.14   minikube   <none>           <none>            app=miapp,entorno=produccion
milogspod                       1/1     Running             1 (57m ago)    163m    172.17.0.13   minikube   <none>           <none>            <none>
mimulticontainerpod             2/2     Running             6 (57m ago)    30h     172.17.0.10   minikube   <none>           <none>            <none>
minamespacepod                  1/1     Running             13 (57m ago)   3d20h   172.17.0.7    minikube   <none>           <none>            <none>
mireadinesspod                  0/1     Running             2 (57m ago)    26h     172.17.0.4    minikube   <none>           <none>            <none>
miresourceconsumerbig           2/2     Running             2 (57m ago)    138m    172.17.0.6    minikube   <none>           <none>            <none>
miresourceconsumersmall         2/2     Running             2 (57m ago)    136m    172.17.0.5    minikube   <none>           <none>            <none>
miresourcepod                   1/1     Running             8 (57m ago)    32h     172.17.0.12   minikube   <none>           <none>            <none>
miresourcepod2                  1/1     Running             8 (57m ago)    32h     172.17.0.2    minikube   <none>           <none>            <none>
misecretpod                     1/1     Running             10 (57m ago)   3d3h    172.17.0.11   minikube   <none>           <none>            <none>
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get pods -n paradigma -o wide --show-labels
NAME                            READY   STATUS    RESTARTS       AGE     IP            NODE       NOMINATED NODE   READINESS GATES   LABELS
miannotationpod                 1/1     Running   0              16m     172.17.0.16   minikube   <none>           <none>            <none>
miconfigmappod                  1/1     Running   10 (57m ago)   3d3h    172.17.0.3    minikube   <none>           <none>            <none>
mideployment-74d589986c-gvxdq   1/1     Running   0              12s     172.17.0.19   minikube   <none>           <none>            app=nginx,pod-template-hash=74d589986c
mideployment-74d589986c-tpzhp   1/1     Running   0              12s     172.17.0.18   minikube   <none>           <none>            app=nginx,pod-template-hash=74d589986c
mideployment-74d589986c-vl6kc   1/1     Running   0              12s     172.17.0.17   minikube   <none>           <none>            app=nginx,pod-template-hash=74d589986c
milabeldesarrollopod            1/1     Running   0              31m     172.17.0.15   minikube   <none>           <none>            app=miapp,entorno=desarrollo
milabelproduccionpod            1/1     Running   0              31m     172.17.0.14   minikube   <none>           <none>            app=miapp,entorno=produccion
milogspod                       1/1     Running   1 (57m ago)    163m    172.17.0.13   minikube   <none>           <none>            <none>
mimulticontainerpod             2/2     Running   6 (57m ago)    30h     172.17.0.10   minikube   <none>           <none>            <none>
minamespacepod                  1/1     Running   13 (57m ago)   3d20h   172.17.0.7    minikube   <none>           <none>            <none>
mireadinesspod                  0/1     Running   2 (57m ago)    26h     172.17.0.4    minikube   <none>           <none>            <none>
miresourceconsumerbig           2/2     Running   2 (57m ago)    138m    172.17.0.6    minikube   <none>           <none>            <none>
miresourceconsumersmall         2/2     Running   2 (57m ago)    136m    172.17.0.5    minikube   <none>           <none>            <none>
miresourcepod                   1/1     Running   8 (57m ago)    32h     172.17.0.12   minikube   <none>           <none>            <none>
miresourcepod2                  1/1     Running   8 (57m ago)    32h     172.17.0.2    minikube   <none>           <none>            <none>
misecretpod                     1/1     Running   10 (57m ago)   3d3h    172.17.0.11   minikube   <none>           <none>            <none>
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl delete pod mideployment-74d589986c-vl6kc -n paradigma
pod "mideployment-74d589986c-vl6kc" deleted
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % 
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get pods -n paradigma -o wide --show-labels          
NAME                            READY   STATUS    RESTARTS        AGE     IP            NODE       NOMINATED NODE   READINESS GATES   LABELS
miannotationpod                 1/1     Running   0               21m     172.17.0.16   minikube   <none>           <none>            <none>
miconfigmappod                  1/1     Running   11 (2m8s ago)   3d4h    172.17.0.3    minikube   <none>           <none>            <none>
mideployment-74d589986c-gvxdq   1/1     Running   0               4m54s   172.17.0.19   minikube   <none>           <none>            app=nginx,pod-template-hash=74d589986c
mideployment-74d589986c-n299t   1/1     Running   0               5s      172.17.0.17   minikube   <none>           <none>            app=nginx,pod-template-hash=74d589986c
mideployment-74d589986c-tpzhp   1/1     Running   0               4m54s   172.17.0.18   minikube   <none>           <none>            app=nginx,pod-template-hash=74d589986c
milabeldesarrollopod            1/1     Running   0               36m     172.17.0.15   minikube   <none>           <none>            app=miapp,entorno=desarrollo
milabelproduccionpod            1/1     Running   0               36m     172.17.0.14   minikube   <none>           <none>            app=miapp,entorno=produccion
milogspod                       1/1     Running   1 (62m ago)     168m    172.17.0.13   minikube   <none>           <none>            <none>
mimulticontainerpod             2/2     Running   6 (62m ago)     30h     172.17.0.10   minikube   <none>           <none>            <none>
minamespacepod                  1/1     Running   14 (2m3s ago)   3d21h   172.17.0.7    minikube   <none>           <none>            <none>
mireadinesspod                  0/1     Running   2 (62m ago)     26h     172.17.0.4    minikube   <none>           <none>            <none>
miresourceconsumerbig           2/2     Running   3 (26s ago)     142m    172.17.0.6    minikube   <none>           <none>            <none>
miresourceconsumersmall         2/2     Running   3 (26s ago)     141m    172.17.0.5    minikube   <none>           <none>            <none>
miresourcepod                   1/1     Running   9 (2m5s ago)    32h     172.17.0.12   minikube   <none>           <none>            <none>
miresourcepod2                  1/1     Running   9 (2m8s ago)    32h     172.17.0.2    minikube   <none>           <none>            <none>
misecretpod                     1/1     Running   11 (2m4s ago)   3d3h    172.17.0.11   minikube   <none>           <none>            <none>
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml %  
```

## Jobs

reference: https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/

job.yml
```
apiVersion: batch/v1
kind: Job
metadata:
  name: mijob
  namespace: paradigma
spec:
  template:
    spec:
      containers:
      - name: busybox
        image: busybox
        args:
        - /bin/sh
        - -c
        - date; echo Ejecutando un job
      restartPolicy: Never
  backoffLimit: 4
```
* Desplegar el jobs

```
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get pods -n paradigma -o wide --show-labels
NAME                            READY   STATUS    RESTARTS       AGE     IP            NODE       NOMINATED NODE   READINESS GATES   LABELS
miannotationpod                 1/1     Running   1 (46s ago)    100m    172.17.0.4    minikube   <none>           <none>            <none>
miconfigmappod                  1/1     Running   12 (46s ago)   3d5h    172.17.0.19   minikube   <none>           <none>            <none>
mideployment-74d589986c-gvxdq   1/1     Running   1 (46s ago)    83m     172.17.0.9    minikube   <none>           <none>            app=nginx,pod-template-hash=74d589986c
mideployment-74d589986c-n299t   1/1     Running   1 (46s ago)    79m     172.17.0.5    minikube   <none>           <none>            app=nginx,pod-template-hash=74d589986c
mideployment-74d589986c-tpzhp   1/1     Running   1 (46s ago)    83m     172.17.0.2    minikube   <none>           <none>            app=nginx,pod-template-hash=74d589986c
milabeldesarrollopod            1/1     Running   1 (46s ago)    115m    172.17.0.16   minikube   <none>           <none>            app=miapp,entorno=desarrollo
milabelproduccionpod            1/1     Running   1 (46s ago)    115m    172.17.0.12   minikube   <none>           <none>            app=miapp,entorno=produccion
milogspod                       1/1     Running   2 (46s ago)    4h7m    172.17.0.3    minikube   <none>           <none>            <none>
mimulticontainerpod             2/2     Running   8 (46s ago)    31h     172.17.0.13   minikube   <none>           <none>            <none>
minamespacepod                  1/1     Running   15 (46s ago)   3d22h   172.17.0.11   minikube   <none>           <none>            <none>
mireadinesspod                  0/1     Running   3 (46s ago)    27h     172.17.0.6    minikube   <none>           <none>            <none>
miresourceconsumerbig           2/2     Running   5 (46s ago)    3h42m   172.17.0.7    minikube   <none>           <none>            <none>
miresourceconsumersmall         2/2     Running   5 (46s ago)    3h40m   172.17.0.18   minikube   <none>           <none>            <none>
miresourcepod                   1/1     Running   10 (46s ago)   34h     172.17.0.10   minikube   <none>           <none>            <none>
miresourcepod2                  1/1     Running   10 (46s ago)   33h     172.17.0.14   minikube   <none>           <none>            <none>
misecretpod                     1/1     Running   12 (46s ago)   3d4h    172.17.0.17   minikube   <none>           <none>            <none>
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl apply -f job.yml                           
job.batch/mijob created
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get pods -n paradigma -o wide --show-labels
NAME                            READY   STATUS      RESTARTS       AGE     IP            NODE       NOMINATED NODE   READINESS GATES   LABELS
miannotationpod                 1/1     Running     1 (56s ago)    100m    172.17.0.4    minikube   <none>           <none>            <none>
miconfigmappod                  1/1     Running     12 (56s ago)   3d5h    172.17.0.19   minikube   <none>           <none>            <none>
mideployment-74d589986c-gvxdq   1/1     Running     1 (56s ago)    84m     172.17.0.9    minikube   <none>           <none>            app=nginx,pod-template-hash=74d589986c
mideployment-74d589986c-n299t   1/1     Running     1 (56s ago)    79m     172.17.0.5    minikube   <none>           <none>            app=nginx,pod-template-hash=74d589986c
mideployment-74d589986c-tpzhp   1/1     Running     1 (56s ago)    84m     172.17.0.2    minikube   <none>           <none>            app=nginx,pod-template-hash=74d589986c
mijob-tkvpm                     0/1     Completed   0              3s      172.17.0.20   minikube   <none>           <none>            controller-uid=6f637201-8119-4585-9e53-6b05711ffd65,job-name=mijob
milabeldesarrollopod            1/1     Running     1 (56s ago)    115m    172.17.0.16   minikube   <none>           <none>            app=miapp,entorno=desarrollo
milabelproduccionpod            1/1     Running     1 (56s ago)    115m    172.17.0.12   minikube   <none>           <none>            app=miapp,entorno=produccion
milogspod                       1/1     Running     2 (56s ago)    4h7m    172.17.0.3    minikube   <none>           <none>            <none>
mimulticontainerpod             2/2     Running     8 (56s ago)    31h     172.17.0.13   minikube   <none>           <none>            <none>
minamespacepod                  1/1     Running     15 (56s ago)   3d22h   172.17.0.11   minikube   <none>           <none>            <none>
mireadinesspod                  0/1     Running     3 (56s ago)    27h     172.17.0.6    minikube   <none>           <none>            <none>
miresourceconsumerbig           2/2     Running     5 (56s ago)    3h42m   172.17.0.7    minikube   <none>           <none>            <none>
miresourceconsumersmall         2/2     Running     5 (56s ago)    3h40m   172.17.0.18   minikube   <none>           <none>            <none>
miresourcepod                   1/1     Running     10 (56s ago)   34h     172.17.0.10   minikube   <none>           <none>            <none>
miresourcepod2                  1/1     Running     10 (56s ago)   33h     172.17.0.14   minikube   <none>           <none>            <none>
misecretpod                     1/1     Running     12 (56s ago)   3d4h    172.17.0.17   minikube   <none>           <none>            <none>
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get jobs -n paradigma
NAME    COMPLETIONS   DURATION   AGE
mijob   1/1           3s         82s
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get pod mijob-tkvpm -n paradigma
NAME          READY   STATUS      RESTARTS   AGE
mijob-tkvpm   0/1     Completed   0          96s
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl logs mijob-tkvpm -n paradigma 
Mon Jun 20 19:08:34 UTC 2022
Ejecutando un job
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % 
```
