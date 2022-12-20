**kali GPG error**

```
方案一
https://http.kali.org/kali/pool/main/k/kali-archive-keyring/ 
wget https://http.kali.org/kali/pool/main/k/kali-archive-keyring/kali-archive-keyring_2022.1_all.deb --no-check-certificate
apt install ./kali-archive-keyring_2022.1_all.deb
方案二
apt -o Acquire::AllowInsecureRepositories=true -o Acquire::AllowDowngradeToInsecureRepositories=true update
apt-get --allow-unauthenticated upgrade
方案三
wget archive.kali.org/archive-key.asc   //下载签名
apt-key add archive-key.asc   //安装签名
```

**Setting up libc6:amd64 (2.34-4) ...**
**/usr/bin/perl: error while loading shared libraries: libcrypt.so.1: cannot open shared object file: No such file or directory**
**dpkg: error processing package libc6:amd64 (--configure):**
 **installed libc6:amd64 package post-installation script subprocess returned error exit status 127**
**Errors were encountered while processing:**
 **libc6:amd64**
**E: Sub-process /usr/bin/dpkg returned an error code (1)**

```
https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=993755

$ cd /tmp
$ apt -y download libcrypt1
dpkg-deb -x 
wsl -d kali-linux -u root dpkg-deb -x /tmp/libcrypt1_1%3a4.4.28-2_amd64.deb /tmp
wsl -d kali-linux -u root cp -av /tmp/lib/x86_64-linux-gnu/* /lib/x86_64-linux-gnu/
wsl -d kali-linux -u root apt -y --fix-broken install
wsl -d kali-linux -u root apt upgrade
```

**WSL 中获取宿主机 IP**

```
WSL 每次启动的时候都会有不同的 IP 地址，所以并不能直接用静态的方式来设置代理。WSL2 会把 IP 写在 `/etc/resolv.conf` 中，因此可以用 `cat /etc/resolv.conf | grep nameserver | awk '{ print $2 }'` 这条指令获得宿主机 IP 。

WSL2 自己的 IP 可以用 `hostname -I | awk '{print $1}'` 得到。
```

