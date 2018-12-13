# Kaizen User Registratin
To work on user registration for the kaizen clusters, install [moc-openstack-tools](https://github.com/CCI-MOC/moc-openstack-tools) and [setpass](https://github.com/CCI-MOC/setpass) on separate virtual machines on the same network. 

The image source should be either ubuntu or centos and the flavor should at least m1.small.

### MOC-openstack-tools
The script also requires a TLS-capable mail server to be running. We have used this code with both Sendmail and Postfix. See [below](#mail-server-config) for the Postfix config required to enable TLS.

### Getting the Signed Credentials from Google
1. Goto [Google Developers Console](https://console.developers.google.com/project) and create a new project or select the existing one.
2. In "Auth & API", enable it and create "Service account key". [Referral link](http://gspread.readthedocs.org/en/latest/oauth2.html)
3. Place the "service account key" file in addusers directory and update the name in settings.ini file.
4. Share the google-spreadsheet with the email-address found within the "service-account-key" file.
5. Get the spreadsheet id and put it in settings.ini file.

*Site-note to developer:

Ask infrastructure team for the url of the Google spreadsheet for User Administration and Quota. In addition, in the User Administration there are two sheets. 

The first sheet is for the users that submitted their application to the form. The second sheet is for the users that have been admitted to use the Kaizen clusters.*

### Usecase
These scripts simplify the process of:
1. Creating users and projects in OpenStack, including defining project quotas.
2. Sending a welcome email to new users.
3. Using [Setpass](https://github.com/CCI-MOC/setpass) to email a link that allows new users to set their password securely.
4. Resetting a user's password if they forget it, also via Setpass.

New user data is assumed to be in a Google Sheet. The function parse_rows in addusers.py handles parsing the spreadsheet data, you may need to modify it to work with your particular spreadsheet format.

### Setup
During initial setup, run these commands to install the required dependencies:
```
*Possibly in a virtual environment*
$ pip install -r requirements.txt 
```

### How to use example_settings.ini
Copy the examplee_settings.ini to settings.ini and then fill the fields required in that file.

### Mail Server Config
The mail server needs to have TLS enabled. If using postfix, add the following lines to /etc/postfix/main.cf:

     smtpd_tls_cert_file = /path/to/cert/file
     smtpd_tls_key_file = /path/to/key/file
     smtpd_tls_security_level = may

If no certificate exists, one can be generated:
     
     openssl req -new -x509 -nodes -out /etc/postfix/postfix.pem -keyout /etc/postfix/postfix.pem -days 3650


*Side-note for developer:

The reminder emails are handled by cronjobs. This can be configured by crontabs.*

### Setpass
Microservice that provides users with a easy interface to set their first OpenStack password.

When registering for the cloud service (registration not included in this script) the user provides a secret 4-digit pin. They need to remember this
pin as it will be used as the second factor when authenticating for the first time.

The administrator receives and approves the registration request, and creates an OpenStack account with a random password. The administrator then adds the user_id and password to setpass, receiving a token in response. 

This will be the first factor of authentication, and its time validity is set in the configuration file.

This token is sent to the user as part of a url, for example:

``https://example.com/?token=c35ee31e-3ee2-4a38-9eae-e738bafdccb5``

The user will input the his 4-digit pin, and his desired password.

What setpass then does, is use the random admin assigned password to login to the OpenStack Identity service and change the password to the user-provided
one.

### Usage
To run it:

```
# Possibly in a virtual environment
$ pip install -r requirements.txt
$ python -m setpass.api
```

### Adding a new user

| URL      | /token/\<user_id\>                               |
|----------|--------------------------------------------------|
| Method   | PUT                                              |
| Headers  | X-Auth-Token                                     |
| Body     | {password: \<random_pass\>, pin: \<user_pin\>}   |
| Response | Token

Please note, to authorize the request to add a new user, setpass checks for a valid token in ``x-auth-token``. If it is able to use this token to scope to
the admin project, then the call is authorized.

The response to this call is the token that will be sent to the user. Please note, this is not a valid OpenStack token. This is only to authenticate back
to the setpass service.

This call is idempotent, and can be repeated to set a new pin or password. In each of these cases (including the case with empty json body), a new token
will be generated. So this can be used to renew the token if they expire.

*Side-note to developer

Recommend using Linux Screen to run server to prevent unexpected loss of connection to the virtual machine.

Alternatively, to make the call through the API, you can instantiate a new
keystoneauth session:*

```python
from keystoneauth1.identity import v3
from keystoneauth1 import session

# Create a new keystoneauth auth object, it doesn't need to be scoped to
# any project, but the user needs to have a role in the admin project.
auth = v3.Password(auth_url='https://example.com:5000/v3',
                   username='admin_username',
                   user_domain_id='default',
                   password='admin_password')

sess = session.Session(auth=auth)

body = { 'password': 'openstack_password', 'pin': 'user_pin' }
r = sess.put('https://example.com/token/%s' % 'user_id', json=body)

# this is the token that will be sent to the user
token = r.text
```

