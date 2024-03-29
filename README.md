# network-installations

## router

Linksys E3200 v1.0 with [FreshTomtato](https://freshtomato.org)

To use own DNS resolvers with Stubby:

1. ssh onto router
2. download my config file `wget -P /etc/stubby/ https://raw.githubusercontent.com/pred2k/network-installations/main/etc/stubby/stubby_alt.yml`
3. check if stubby config file is valid: `stubby -C /etc/stubby/stubby_alt.yml -i`
4. Enable Stubby in Router Settings (**Advanced -> DHCP/DNS**)

### Settings

#### Basic -> Network

WAN Settings:

DNS Server **manual** with:
* 176.9.93.198 (https://dnsforge.de/)
* 176.9.1.117 (https://dnsforge.de/)

LAN:

- [x] Enable DNSSEC
- [x] Use Stubby (DNS-over-TLS)  
  Priority: **No-Resolv**
  
#### Advanced -> DHCP/DNS

- [x] Intercept DNS port

## pi-hole

https://pi-hole.net/

### DNS-over-TLS (DoT) for pi-hole

For Raspbian 10 / buster!

DNS resolvers i've choosen:
* https://dnsforge.de/
* https://dismail.de/info.html#dns
* https://digitalcourage.de/support/zensurfreier-dns-server
* https://www.digitale-gesellschaft.ch/dns/

`sudo apt update && sudo apt install -y stubby`

`sudo vim /etc/stubby/stubby.yml`

change listen ports:
```yaml
listen_addresses:
  - address_data: 127.0.0.1
    port: 5053
  - address_data: 0::1
    port: 5053
```

uncomment line:

```yaml
dnssec: GETDNS_EXTENSION_TRUE
```

add my choosen servers:
```yaml
upstream_recursive_servers:
  - address_data: 80.241.218.68
    tls_auth_name: "fdns1.dismail.de"
    tls_pubkey_pinset:
      - digest: "sha256"
        value: MMi3E2HZr5A5GL+badqe3tzEPCB00+OmApZqJakbqUU=
  - address_data: 159.69.114.157
    tls_auth_name: "fdns2.dismail.de"
    tls_pubkey_pinset:
      - digest: "sha256"
        value: yJYDim2Wb6tbxUB3yA5ElU/FsRZZhyMXye8sXhKEd1w=
  - address_data: 46.182.19.48
    tls_auth_name: "dns2.digitalcourage.de"
    tls_pubkey_pinset:
      - digest: "sha256"
        value: v7rm6OtQQD3x/wbsdHDZjiDg+utMZvnoX3jq3Vi8tGU=
  - address_data: 5.9.164.112
    tls_auth_name: "dns3.digitalcourage.de"
    tls_pubkey_pinset:
      - digest: "sha256"
        value: 2WFzfO2/56HpeR+v/l25NPf5dacfxLrudH5yZbWCfdo=
  - address_data: 185.95.218.42
    tls_auth_name: "dns.digitale-gesellschaft.ch"
  - address_data: 185.95.218.43
    tls_auth_name: "dns.digitale-gesellschaft.ch"
```

check if config file is valid: `stubby -C /etc/stubby/stubby.yml -i`

```
sudo systemctl restart stubby.service
sudo systemctl status stubby.service
```

test if stubby can resolve:
`dig @127.0.0.1 -p 5053 heise.de`

now to go http://pi.hole/admin/settings.php?tab=dns and add `127.0.0.1#5053` as a custom upstream dns server.

 Read more
 * https://dnsprivacy.org/wiki/display/DP/Configuring+Stubby
 
 # DNSSEC tests

* https://rootcanary.org/test.html
* https://dnssec-tools.org/test/
* https://en.internet.nl/
* https://0skar.cz/dns/en/
* https://cmdns.dev.dns-oarc.net/
