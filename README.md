# oembootstrap
to configure machines with git and ansible

```bash
bash <(curl -s https://raw.githubusercontent.com/salesucation/oembootstrap/main/oembootstrap)
```

You will need to configure cloudflared for your own tunnel. To get a certificate from your cloudflare account:

```bash
cloudflared tunnel login
```

Then you can create a tunnel:

```bash
cloudflared tunnel create <tunelName>
```

You need the id from this to create a `~/.cloudflared/config.yml` config file:

```yaml
tunnel: <tunnel-uuid>
credentials-file: /home/ubuntu/.cloudflared/<tunnel-uuid>.json 

ingress:
  - hostname: <your-domain>
    service: http://localhost:5051 # port oauth2-proxy is listening on

  - service: http_status:404
```

Then you can create the dns entry on cloudflare:

```bash
cloudflared tunnel route dns <tunnel-uuid> <your-domain>
```

Then to get your tunnel started automatically:

```bash
sudo cloudflared --config ~/.cloudflared/config.yml service install
sudo systemctl start cloudflared
sudo systemctl status cloudflared
```

1. need to add oauth2-proxy

