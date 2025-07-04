# oembootstrap
to configure machines with git and ansible

```bash
bash <(curl -s https://raw.githubusercontent.com/rhildred/bootstrap/main/oembootstrap)
```

You will need to configure cloudflared for your own tunnel. To get a certificate from your cloudflare account:

```bash
cloudflared tunnel login
```

Then you can create a tunnel:

```bash
cloudflared tunnel create <tunelName>
```

You need the id from this to create a `/etc/cloudflared/config.yml` config file:

```yaml
tunnel: <tunnel-uuid>
credentials-file: /home/ubuntu/.cloudflared/<tunnel-uuid>.json 

ingress:
  - hostname: <your-domain>
    service: http://localhost:4180 # port oauth2-proxy is listening on
  - hostname: <your-dev-domain>
    service: http://localhost:8080 # anything you run on this port will be accessible from the hostname ... use to smoke test

  - service: http_status:404
```

Then you can create the dns entry on cloudflare:

```bash
cloudflared tunnel route dns <tunnel-uuid> <your-domain>
```

Then to get your tunnel started automatically:

```bash
sudo cloudflared --config /etc/cloudflared/config.yml service install
sudo systemctl start cloudflared
sudo systemctl status cloudflared
```

To add oauth2 proxy.

1. Generate a cookie_secret by running `python3 -c "import random, string; print(''.join(random.choices(string.ascii_uppercase + string.digits, k=32)))"`
2. Edit `/etc/oauth2-proxy.cfg` to have your_github_client_id, your_github_client_secret and your_cookie_secret the way you need them.
3. Edit `/etc/systemd/system/oauth2-proxy.service` to have github_usernames_separated_by_commas the way you need it.
4. run `sudo systemctl enable oauth2-proxy.service`
5. run `sudo systemctl start oauth2-proxy.service`

Now you should be able to login to your instance from the internet with your github credentials.