## POC build log

### 1. setup DNS records:

On the DNS provider web interface, setup registers like this:

```
CNAME	*.foo.example.com	foo.example.com	43200
A	foo.example.com		93.184.216.34	3600
```

Change `foo.example.com` and `93.184.216.34` to your values.


### 2. test the DNS records:

```bash
host client1.foo.example.com && host client2.foo.example.com
```

They should point to the same IP address, the IP address of the router (set on step 1.).


### 3. Get a Let's Encrypt wildcard SSL certificate with certbot

```bash
# install certbot (ubuntu 22.04)
sudo snap install certbot --classic
sudo snap set certbot trust-plugin-with-root=ok

# using a DNS Provider plugin is the recomended way
sudo snap install certbot-dns-digitalocean

# create a ini file with digital ocean access tokens
# the content should be something like:
# dns_digitalocean_token = XXXXXX
vim ~/certbot-creds.ini
chmod 600 ~/certbot-creds.ini

# check that the files are downloaded
sudo ls /etc/letsencrypt/live
```

based on https://www.digitalocean.com/community/tutorials/how-to-create-let-s-encrypt-wildcard-certificates-with-certbot

### 4. Install Caddy

From: https://caddyserver.com/docs/install#debian-ubuntu-raspbian

```bash
sudo apt install -y debian-keyring debian-archive-keyring apt-transport-https
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/gpg.key' | sudo gpg --dearmor -o /usr/share/keyrings/caddy-stable-archive-keyring.gpg
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/debian.deb.txt' | sudo tee /etc/apt/sources.list.d/caddy-stable.list
sudo apt update
sudo apt install caddy
```


### 5. Basic Caddyfile with 1 subdomain:

```
client1.foo.example.com {
  reverse_proxy {
    to https://142.251.129.46
    transport http {
      tls
      tls_insecure_skip_verify
    }
  }
}
```

Ex: the example above skips tls handshake, this is not recommended.


### 6. Expose the api endpoint

Caddy API is exposed by default on port 2019, and the list of origins that
can consume this api is set using the property "admin/origins" in the Caddyfile:

https://caddyserver.com/docs/json/#admin/origins






































