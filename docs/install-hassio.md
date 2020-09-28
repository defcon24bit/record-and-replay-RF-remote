[<- back to Read Me](https://github.com/defcon24bit/record-and-replay-RF-remote)

1. Install [Home Assistant](https://www.home-assistant.io/getting-started/).
2. Add DuckDNS add-on. This incudes Let's Encrypt.  See Add-on store under Supervisor.   

Tip: block IPs that try to brute force logins.
```yaml
# configuration.yaml
http:
  ssl_certificate: /ssl/fullchain.pem
  ssl_key: /ssl/privkey.pem 
  ip_ban_enabled: true
  login_attempts_threshold: 3
```

[<- back to Read Me](https://github.com/defcon24bit/record-and-replay-RF-remote)
