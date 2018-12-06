We are using vnc to share access for debugging current deployment.  Contact one of the developers to add your public key for ssh access.

Add following to your .ssh/config:

```
Host mocdebug
  HostName moc02.bu.edu
  ForwardX11 yes
  LocalForward 5908 localhost:5908
  LocalForward 5909 localhost:5909
  User mocdebug
```

There are two vncservers we will keep running there, a small one on port '5909 (vncclient :9)' and a small one on port '5908 (vncclient :8)'.  There are aliases in bash to restart them if they go down.  Once you have shed into mocdebug, you will be able to connect your favorite vncclient to either one on your localhost, e.g.:
    vncclient localhost:8

The password is "mocmoc"

Firefox is already set up with socks forwarding, and there is an entry in the .ssh/config file to ssh into the gateway server.