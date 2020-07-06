# Results

Setting `pasv_min_port` and `pasv_max_port` both to `20` results in _vsftpd_ using a dynamic port, seemingly even from outside the [typical ephemeral or dynamic range](https://en.wikipedia.org/wiki/Ephemeral_port#Range).

```
[vagrant@localhost vagrant]$ for i in {1..20} ; do ~/testftp.sh |& egrep -o '\|\|\|[0-9]+\|' ; done | tr -d \| | sort -n
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
