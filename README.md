# network-installations

## pi-hole

https://pi-hole.net/

### DNS-over-TLS (DoT) for pi-hole

With Raspbian 10 / buster

DNS resolvers i've choosen:
* https://dismail.de/info.html#dns
* https://digitalcourage.de/support/zensurfreier-dns-server

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

add my choosen servers:
```yaml
upstream_recursive_servers:
  - address_data: 80.241.218.68
    tls_auth_name: "fdns1.dismail.de"
    tls_pubkey_pinset:
      - digest: "sha256"
        value: MMi3E2HZr5A5GL+badqe3tzEPCB00+OmApZqJakbqUU=
  - address_data: 46.182.19.48
    tls_auth_name: "dns2.digitalcourage.dedns2.digitalcourage.de"
    tls_pubkey_pinset:
      - digest: "sha256"
        value: v7rm6OtQQD3x/wbsdHDZjiDg+utMZvnoX3jq3Vi8tGU=
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
