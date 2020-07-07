# Results


## Restricting passive port range

Setting `pasv_min_port` and `pasv_max_port` both to `20` results in _vsftpd_ using a dynamic port, seemingly even from outside the [typical ephemeral or dynamic range](https://en.wikipedia.org/wiki/Ephemeral_port#Range).

```
[vagrant@localhost vagrant]$ for i in {1..20} ; do ~/test-with-curl.sh |& egrep -o '\|\|\|[0-9]+\|' ; done | tr -d \| | sort -n
1454
6205
7084
7874
12635
14346
15318
21201
24738
30006
30578
32376
33084
34224
37657
46528
49488
50007
58656
63434
[vagrant@localhost vagrant]$
```

If the port range does not include 1024 or higher, then the above random behavior kicks in. If the port range crosses the 1023-1024 boundary, then the effective minimum port is 1024. For example, `pasv_min_port=1023` and `pasv_max_port=1024` results in only port 1024 being used.


## Requiring TLS session reuse

_lftp_ seems to work just fine with session reuse.

Attempting to set `require_ssl_reuse=YES` results in _curl_ complaining

```
> LIST
< 150 Here comes the directory listing.
* Maxdownload = -1
* Doing the SSL/TLS handshake on the data stream
* skipping SSL peer certificate verification
* NSS: client certificate not found (nickname not specified)
* SSL connection using TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
* Server certificate:
*       subject: E=root@localhost.localdomain,CN=localhost.localdomain,OU=SomeOrganizationalUnit,O=SomeOrganization,L=SomeCity,ST=SomeState,C=--
*       start date: Jul 06 18:40:04 2020 GMT
*       expire date: Jul 06 18:40:04 2021 GMT
*       common name: localhost.localdomain
*       issuer: E=root@localhost.localdomain,CN=localhost.localdomain,OU=SomeOrganizationalUnit,O=SomeOrganization,L=SomeCity,ST=SomeState,C=--
* Remembering we are in dir ""
< 522 SSL connection failed; session reuse required: see require_ssl_reuse option in vsftpd.conf man page
* server did not report OK, got 522
```

Red Hat seem to think this is [an issue in NSS](https://bugzilla.redhat.com/show_bug.cgi?id=1552927), not in libcurl.
