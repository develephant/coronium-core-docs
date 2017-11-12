## CLI Tools

### webmin-passwd

Used to change the Coronium Core Webmin password. Default is __cloudadmin__.

!!! info "Webmin User"
    The Coronium Core Webmin user is always __cloudwebmin__.

Run the following on the command line and follow the prompts:

```
sudo webmin-passwd
```

### webmin-apihost

When changing you Coronium Core server from IP to domain based, you must update the Webmin Confirmation Host using this tool ___or___ from the __Config__ section in the Webmin itself.

From the command line, run the following, using your registered domain name:

```
sudo webmin-apihost http://<your.coronium.host>
```

!!! note "SSL/HTTPS"
    When using the SSL/HTTPS updater, the Webmin API host will automatically be updated.