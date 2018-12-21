From original fork, here are the changes:
- increase BUFSIZE as 4K is not enough for my usage, had to increase it to 8K
- run in RHEL 7 environments
- was not able to make it work on Windows 10, with or without cygwin

My personal usage:
- write a dedicated `krb.conf` file for kerberos configuration
- write a dedicated `cntlm.conf` file for cntlm configuration
- run kinit to authenticate: `KRB5_CONFIG=krb.conf kinit USERNAME`
- run cntlm-gss: `KRB5_CONFIG=krb.conf ./cntlm -c cntlm.conf -a gss -fv`

Example of `cntlm.conf`:
```
Listen   1234
Proxy    my.proxy.com
NoProxy  localhost, [0-9]*
```

Example of `krb.conf`:
```
[logging]
 default = FILE:krb5libs.log
 kdc = FILE:krb5kdc.log
 admin_server = FILE:kadmind.log

[libdefaults]
 dns_lookup_realm = false
 ticket_lifetime = 24h
 renew_lifetime = 7d
 forwardable = true
 rdns = false
 default_realm = MY.DOMAIN.COM

[realms]
MY.DOMAIN.COM = {
  kdc = ad.my.domain.com
}

[domain_realms]
ad.my.domain.com =  MY.DOMAIN.COM
```

---

This is [Cntlm](http://cntlm.sourceforge.net/) **with Kerberos patch applied**.

Works on a Ubuntu 12.04 box, at least for me.

Dependency: [Kerberos](http://web.mit.edu/kerberos/).

If Kerberos is compiled to a different location, say, $HOME/usr, compile Cntlm with

`./configure --enable-kerberos`

`export LIBRARY_PATH=$HOME/usr/lib`

`export C_INCLUDE_PATH=$HOME/usr/include`

`make`

To run it, try `cntlm --help` or `cntlm -v` and fix whatever it complains.

I have only the following lines in my ctnlm.conf file:

```
Username	
Domain		
Password	
Proxy		proxy.server.domain.com:3128
NoProxy		localhost, 127.0.0.*, 10.*, 192.168.*
Listen		3128
```

The username, domain and password are all unset.

I could start it with `/home/me/usr/opt/cntlm-0.92.3/cntlm -a gss -c /home/me/usr/opt/cntlm-0.92.3/cntlm.conf` .
