## How to Use OpenVPN client

1. Install OpenVPN client.

On Ubuntu
```
sudo apt-get install openvpn -y
```

On CentOS: Enable epel and then get openvpn
```
sudo yum install epel-release -y
sudo yum install openvpn -y
```

2. Copy the configuration file to `/etc/openvpn/`

3. Run `sudo systemctl enable --now openvpn@config-file` if your config file is
`config-file.conf`. This will start openvpn connection  and will automatically
run on boot.

If you want to run openvpn manually each time, then run
```
sudo openvpn --config /path/to/config-file.conf
```

4. That's all. You should now be able to directly access networks from your local
machine.
