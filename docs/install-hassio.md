1) Install [Home Assistant](https://www.home-assistant.io/getting-started/).

(optional) Expose your Home Assistant to the internet:

2) Install https://github.com/brenner-tobias/addon-cloudflared (prefered - free, but requires you to buy or own a domain name)
> I only had one domain name that was already pointing to a website.  By default, this addon creates a sub-domain.  You can run both side by side without issues.   Example: wwww.<yourdomain.xyz> and homeassistant.<yourdomain.xyz> 
 
or  
Add DuckDNS add-on.  See add-on store under Supervisor.  
This add-on incudes Let's Encrypt. 

Tip: block IPs that try to brute force logins.
```yaml
# configuration.yaml
http:
  ssl_certificate: /ssl/fullchain.pem
  ssl_key: /ssl/privkey.pem 
  ip_ban_enabled: true
  login_attempts_threshold: 3
```

[<- back to Read Me](https://github.com/defcon24bit/record-and-replay-RF-remote#integrate-with-home-assistant)
