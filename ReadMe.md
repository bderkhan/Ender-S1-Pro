# Ender 3 S1 PRO - Klipper - Tuarus V5 Cooler - 4020 Cooler
This Ender 3 S1 Pro setup has 3 4020 fans, 2 for the hot end, and 1 for the print head.

Tuarus Cooler: https://www.printables.com/model/511962-taurus-v5-cooling-duct-for-creality-sprite-extrude
Hot end duct: https://cults3d.com/en/3d-model/tool/ender-3-s1-pro-4020-fan-cr-touch-no-y-offset

# Obico Server Setup Info:

### How to change ip address and External ip address: 
```cd ~/Downloads/obico-server ```

```nano ~/Downloads/obico-server/backend/config/settings.py```


Then find:

```ALLOWED_HOSTS = ['localhost', '127.0.0.1', '192.168.86.45', '<External IP>']```

and add/change the ports (internal and external) 

Stay CD'd into ```~/Downloads/obico-server ``` 

to then restart the docker stuff: 

```docker-compose down && docker-compose up -d```

### Obico Client Setup:
To update the server address on the raspberry pi: 
Check the file called: moonraker-obico.cfg
and change the address there:
```
[server]
url = http://192.168.86.45:3334
```

also change the client IP address as well 

```
[moonraker]
host = 192.168.86.20
port = 7125
```
