# Reset Password in Kaizen Openstack

### Instructions
This document describes how to reset the password.
1. Logon to the helpdesk VM.
`ssh helpdesk@helpdeskvm.massopen.cloud`

2. Run the command to reset the password.
`moc reset-password <username, which is usually the email> <pin requested by the user`
For example:
`moc reset-password user@example.com 1321`

The command will return
```
Password successfully reset for user user@example.com
```

### Where does that PIN come from?
* Some people get password reset emails with the user emails and the pins.
* There's also a google doc that has this information and should be linked here.

### How to get access to the helpdesk VM.
* Talk to Naved/Rado to give you access on that machine.

