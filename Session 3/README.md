# Service
Los deployment facilitan la creación de un conjunto de pods en réplica que se pueden escalar, actualizar y reemplazar dinámicamente. Sin embargo, es difícil proporcionar acceso de red a esos pods para otros componentes. Los services proporcionan una capa de abstracción que resuelve este problema. Los clientes simplemente pueden acceder al service, que representa dinámicamente el tráfico al conjunto actual de réplicas. Los services utilizan la etiqueta selector para buscar los pods que tengan esa label y dar acceso a ellos.

## ClusterIP
expone el Service en una dirección IP interna del clúster. Al escoger este valor el Service solo es alcanzable desde el clúster. Este es el ServiceType por defecto.

deploymentClusterIp.yml
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mideploymentclusterip
  namespace: paradigma
spec:
  strategy:
    type: RollingUpdate
  replicas: 3
  selector:
    matchLabels:
      app: miappclusterip
  template:
    metadata:
      labels:
        app: miappclusterip
    spec:
      containers:
        - image: nginx
          name: nginx
          ports:
          - containerPort: 80
```
serviceCluterIp.yml
```
kind: Service
apiVersion: v1
metadata:
  name: miserviceclusterip
  namespace: paradigma
spec:
  type: ClusterIP
  selector:
    app: miappclusterip
  ports:
  - protocol: TCP
    port: 8080
    targetPort: 80
```

* Se despliega el deploy

```
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get pods -n paradigma                                          
No resources found in paradigma namespace.
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl apply -f deploymentClusterIp.yml
Error from server (NotFound): error when creating "deploymentClusterIp.yml": namespaces "paradigma" not found
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl create ns paradigma
namespace/paradigma created
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl apply -f deploymentClusterIp.yml
deployment.apps/mideploymentclusterip created
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get deployment -n paradigma     
NAME                    READY   UP-TO-DATE   AVAILABLE   AGE
mideploymentclusterip   3/3     3            3           56s
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get pods -n paradigma
NAME                                    READY   STATUS    RESTARTS   AGE
mideploymentclusterip-8d55c59cf-g5xfj   1/1     Running   0          66s
mideploymentclusterip-8d55c59cf-ls65f   1/1     Running   0          66s
mideploymentclusterip-8d55c59cf-s5pz9   1/1     Running   0          66s
```
* Se despliega el service

```
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl apply -f serviceClusterIp.yml
service/miserviceclusterip created
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get service -n paradigma
NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
miserviceclusterip   ClusterIP   10.99.193.111   <none>        8080/TCP   9s
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get service -n paradigma -o wide 
NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE   SELECTOR
miserviceclusterip   ClusterIP   10.99.193.111   <none>        8080/TCP   42s   app=miappclusterip
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl describe service miserviceclusterip -n paradigma
Name:              miserviceclusterip
Namespace:         paradigma
Labels:            <none>
Annotations:       <none>
Selector:          app=miappclusterip
Type:              ClusterIP
IP Family Policy:  SingleStack
IP Families:       IPv4
IP:                10.99.193.111
IPs:               10.99.193.111
Port:              <unset>  8080/TCP
TargetPort:        80/TCP
Endpoints:         172.17.0.3:80,172.17.0.4:80,172.17.0.5:80
Session Affinity:  None
Events:            <none>
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % 
```

* Probar desde un pod dentro del cluster de kubernetes probar acceso al service

```
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl apply -f namespacePod.yml       
pod/minamespacepod created
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get pods -n paradigma           
NAME                                    READY   STATUS              RESTARTS   AGE
mideploymentclusterip-8d55c59cf-g5xfj   1/1     Running             0          4m20s
mideploymentclusterip-8d55c59cf-ls65f   1/1     Running             0          4m20s
mideploymentclusterip-8d55c59cf-s5pz9   1/1     Running             0          4m20s
minamespacepod                          0/1     ContainerCreating   0          3s
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get pods -n paradigma
NAME                                    READY   STATUS    RESTARTS   AGE
mideploymentclusterip-8d55c59cf-g5xfj   1/1     Running   0          4m27s
mideploymentclusterip-8d55c59cf-ls65f   1/1     Running   0          4m27s
mideploymentclusterip-8d55c59cf-s5pz9   1/1     Running   0          4m27s
minamespacepod                          1/1     Running   0          10s
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl exec -it minamespacepod -c busybox -n paradigma -- sh
/ # ls
bin   dev   etc   home  proc  root  sys   tmp   usr   var
/ # curl localhost:80
sh: curl: not found
/ # apt update
sh: apt: not found
/ # exit
command terminated with exit code 127
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl apply -f configMap.yml                               
configmap/miconfigmap created
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl apply -f configMapPod.yml
pod/miconfigmappod created
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get pods -n paradigma                                
NAME                                    READY   STATUS              RESTARTS   AGE
miconfigmappod                          0/1     ContainerCreating   0          4s
mideploymentclusterip-8d55c59cf-g5xfj   1/1     Running             0          7m13s
mideploymentclusterip-8d55c59cf-ls65f   1/1     Running             0          7m13s
mideploymentclusterip-8d55c59cf-s5pz9   1/1     Running             0          7m13s
minamespacepod                          1/1     Running             0          2m56s
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get pods -n paradigma
NAME                                    READY   STATUS    RESTARTS   AGE
miconfigmappod                          1/1     Running   0          10s
mideploymentclusterip-8d55c59cf-g5xfj   1/1     Running   0          7m19s
mideploymentclusterip-8d55c59cf-ls65f   1/1     Running   0          7m19s
mideploymentclusterip-8d55c59cf-s5pz9   1/1     Running   0          7m19s
minamespacepod                          1/1     Running   0          3m2s
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl exec -it miconfigmappod -c ubuntu -n paradigma -- sh
# ls
bin  boot  dev  etc  home  lib  lib32  lib64  libx32  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
# curl localhost:80
sh: 2: curl: not found
# apt update
Get:1 http://archive.ubuntu.com/ubuntu focal InRelease [265 kB]
Get:2 http://security.ubuntu.com/ubuntu focal-security InRelease [114 kB]
Get:3 http://security.ubuntu.com/ubuntu focal-security/universe amd64 Packages [881 kB]
Get:4 http://archive.ubuntu.com/ubuntu focal-updates InRelease [114 kB]
Get:5 http://archive.ubuntu.com/ubuntu focal-backports InRelease [108 kB]
Get:6 http://archive.ubuntu.com/ubuntu focal/restricted amd64 Packages [33.4 kB]
Get:7 http://archive.ubuntu.com/ubuntu focal/main amd64 Packages [1275 kB]
Get:8 http://security.ubuntu.com/ubuntu focal-security/restricted amd64 Packages [1324 kB]
Get:9 http://archive.ubuntu.com/ubuntu focal/universe amd64 Packages [11.3 MB]
Get:10 http://security.ubuntu.com/ubuntu focal-security/multiverse amd64 Packages [27.5 kB]
Get:11 http://security.ubuntu.com/ubuntu focal-security/main amd64 Packages [1974 kB]     
Get:12 http://archive.ubuntu.com/ubuntu focal/multiverse amd64 Packages [177 kB]              
Get:13 http://archive.ubuntu.com/ubuntu focal-updates/multiverse amd64 Packages [30.3 kB]
Get:14 http://archive.ubuntu.com/ubuntu focal-updates/restricted amd64 Packages [1404 kB]
Get:15 http://archive.ubuntu.com/ubuntu focal-updates/universe amd64 Packages [1161 kB]
Get:16 http://archive.ubuntu.com/ubuntu focal-updates/main amd64 Packages [2415 kB]
Get:17 http://archive.ubuntu.com/ubuntu focal-backports/main amd64 Packages [54.2 kB]
Get:18 http://archive.ubuntu.com/ubuntu focal-backports/universe amd64 Packages [27.1 kB]
Fetched 22.7 MB in 3s (7443 kB/s)                      
Reading package lists... Done
Building dependency tree       
Reading state information... Done
7 packages can be upgraded. Run 'apt list --upgradable' to see them.
# apt install curl
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
Fetched 5445 kB in 2s (2919 kB/s)           
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
# curl 10.99.193.111:80
^C
# curl 10.99.193.111:8080
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
# 
```

## NodePort

expone el Service en cada IP del nodo en un puerto estático (el NodePort). Automáticamente se crea un Service ClusterIP, al cual enruta el NodePort del Service. Podrás alcanzar el Service NodePort desde fuera del clúster, haciendo una petición a <NodeIP>:<NodePort>. Si no especificamos el nodeport en el yml kubernetes asigna uno automáticamente en el rango 30000:40000

deploymentClusterNodeport.yml
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mideploymentclusternodeport
  namespace: paradigma
spec:
  strategy:
    type: RollingUpdate
  replicas: 3
  selector:
    matchLabels:
      app: miappclusternodeport
  template:
    metadata:
      labels:
        app: miappclusternodeport
    spec:
      containers:
        - image: nginx
          name: nginx
          ports:
          - containerPort: 80
```
serviceClusterNodeport.yml
```
apiVersion: v1
kind: Service
metadata:
  name: miservicenodeport
  namespace: paradigma
spec:
  type: NodePort
  selector:
    app: miappclusternodeport
  ports:
  - nodePort: 30100
    port: 8080
    targetPort: 80
    protocol: TCP
```

* Practica

```
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get pods -n paradigma                               
NAME                                    READY   STATUS    RESTARTS   AGE
miconfigmappod                          1/1     Running   0          35m
mideploymentclusterip-8d55c59cf-g5xfj   1/1     Running   0          42m
mideploymentclusterip-8d55c59cf-ls65f   1/1     Running   0          42m
mideploymentclusterip-8d55c59cf-s5pz9   1/1     Running   0          42m
minamespacepod                          1/1     Running   0          38m
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl apply -f deploymentClusterNodeport.yml              
deployment.apps/mideploymentclusternodeport created
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get pods -n paradigma                 
NAME                                           READY   STATUS              RESTARTS   AGE
miconfigmappod                                 1/1     Running             0          35m
mideploymentclusterip-8d55c59cf-g5xfj          1/1     Running             0          42m
mideploymentclusterip-8d55c59cf-ls65f          1/1     Running             0          42m
mideploymentclusterip-8d55c59cf-s5pz9          1/1     Running             0          42m
mideploymentclusternodeport-694657b969-jfb75   0/1     ContainerCreating   0          3s
mideploymentclusternodeport-694657b969-k7cjf   0/1     ContainerCreating   0          3s
mideploymentclusternodeport-694657b969-wskjc   1/1     Running             0          3s
minamespacepod                                 1/1     Running             0          38m
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl apply -f serviceClusterNodeport.yml
service/miservicenodeport created
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl get service -n paradigma -o wide        
NAME                 TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE   SELECTOR
miserviceclusterip   ClusterIP   10.99.193.111   <none>        8080/TCP         41m   app=miappclusterip
miservicenodeport    NodePort    10.100.97.25    <none>        8080:30100/TCP   16s   app=miappclusternodeport
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % kubectl describe service miservicenodeport -n paradigma
Name:                     miservicenodeport
Namespace:                paradigma
Labels:                   <none>
Annotations:              <none>
Selector:                 app=miappclusternodeport
Type:                     NodePort
IP Family Policy:         SingleStack
IP Families:              IPv4
IP:                       10.100.97.25
IPs:                      10.100.97.25
Port:                     <unset>  8080/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  30100/TCP
Endpoints:                172.17.0.10:80,172.17.0.8:80,172.17.0.9:80
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % curl 10.100.97.25:30100
^C
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % curl 10.100.97.25:8080 
^C
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % curl 10.100.97.25:80  
^C
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % curl localhost:30100   
curl: (7) Failed to connect to localhost port 30100 after 8 ms: Connection refused
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % curl localhost:8080 
curl: (7) Failed to connect to localhost port 8080 after 8 ms: Connection refused
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % curl localhost:80  
curl: (7) Failed to connect to localhost port 80 after 8 ms: Connection refused
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % ping 10.100.97.25
PING 10.100.97.25 (10.100.97.25): 56 data bytes
Request timeout for icmp_seq 0
Request timeout for icmp_seq 1
Request timeout for icmp_seq 2
Request timeout for icmp_seq 3
Request timeout for icmp_seq 4
Request timeout for icmp_seq 5
Request timeout for icmp_seq 6
Request timeout for icmp_seq 7
Request timeout for icmp_seq 8
Request timeout for icmp_seq 9
Request timeout for icmp_seq 10
Request timeout for icmp_seq 11
Request timeout for icmp_seq 12
^C
--- 10.100.97.25 ping statistics ---
14 packets transmitted, 0 packets received, 100.0% packet loss
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % ping 127.0.0.1   
PING 127.0.0.1 (127.0.0.1): 56 data bytes
64 bytes from 127.0.0.1: icmp_seq=0 ttl=64 time=0.048 ms
64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.066 ms
64 bytes from 127.0.0.1: icmp_seq=2 ttl=64 time=0.061 ms
64 bytes from 127.0.0.1: icmp_seq=3 ttl=64 time=0.058 ms
^C
--- 127.0.0.1 ping statistics ---
4 packets transmitted, 4 packets received, 0.0% packet loss
round-trip min/avg/max/stddev = 0.048/0.058/0.066/0.007 ms
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % curl
curl: try 'curl --help' or 'curl --manual' for more information
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % curl --help
Usage: curl [options...] <url>
 -d, --data <data>   HTTP POST data
 -f, --fail          Fail silently (no output at all) on HTTP errors
 -h, --help <category>  Get help for commands
 -i, --include       Include protocol response headers in the output
 -o, --output <file>  Write to file instead of stdout
 -O, --remote-name   Write output to a file named as the remote file
 -s, --silent        Silent mode
 -T, --upload-file <file>  Transfer local FILE to destination
 -u, --user <user:password>  Server user and password
 -A, --user-agent <name>  Send User-Agent <name> to server
 -v, --verbose       Make the operation more talkative
 -V, --version       Show version number and quit

This is not the full help, this menu is stripped into categories.
Use "--help category" to get an overview of all categories.
For all options use the manual or "--help all".
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % curl http://10.100.97.25:30100
^C
gerlinorlandotorres@MacBook-Pro-de-Gerlin yml % curl http://localhost:30100   
curl: (7) Failed to connect to localhost port 30100 after 7 ms: Connection refused
```
No se pudo acceder desde el ordenador local a minikube
