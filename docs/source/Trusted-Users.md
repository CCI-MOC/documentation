# Trusted Users
[Online user request form](https://massopen.cloud/blog/user-account-request-form/)

### Steps to use addusers script
*If you already have the code directory setup, you can jump to step of running adduser.py*
* Get the [code](https://github.com/CCI-MOC/moc/tree/master/scripts/addusers)
* Copy template_settings.ini to settings.ini and add the parameters required. Here is the sample:
```
[auth]
admin_user = admin
admin_pwd = <set password here>
admin_tenant = admin
auth_url = https://controller-0.kaizen.massopencloud.org:5000/v2.0/

[nova]
version = 2

[templates]
email_template = ./email-template.txt
password_template = ./password-template.txt

[output]
email_path = ./user_emails/
password_path = ./password_emails/

[gmail]
email = kaizen@lists.massopen.cloud
password =
cc_list = kaizen@lists.massopen.cloud

[excelsheet]
auth_file = project-mocwebsite-5b5ccc23e55c.json
worksheet_key = 10MF5OYuwxiB537IpuKwBDKy388KRKCMJLabA3D28Ztw
```
* It uses [this](https://docs.google.com/spreadsheets/d/10MF5OYuwxiB537IpuKwBDKy388KRKCMJLabA3D28Ztw/edit) excel sheet. Make sure the users are trusted/approved users in the sheet.
* Run`python addusers.py`
* The quotas are not set by default. You need to modify the quotas for project created.
* Profit

Eventually, we will have this online registration. For now, we will use a simple form that can be checked out from MOC repo. 

The request form file name is [MOCAccountRequestForm_TrustedUser.docx](_static/docx/MOCAccountRequestForm_TrustedUser.docx) or [MOCAccountRequestForm_TrustedUser.pdf](_static/MOCAccountRequestForm_TrustedUser.pdf)

### Steps after moc-kaizen-l receives the request form:
1. create the account for the user using the default quota below
2. send a welcome email to user and cc kaizen@lists.massopen.cloud
3. send a separate email with the password just to the user
4. add user to the kaizen-users@lists.massopen.cloud mailing list
5. add entry to the end of the following table

| Requested Date | Name             | Project Name     | Contact info.       | Sponsor              | Account Created  | Notes            |
| -------------- | ---------------- | ---------------- | ------------------- | -------------------- | ---------------- | ---------------- |
| 12-7-2015 | John Goodhue | jtgoodhue | jtgoodhue@mghpcc.org ; 617-834-5601 | n/a | Laura Kamfonik  | Created 12/8/2015 | - |
| 12-8-2015 | Chris Hill | cnh | cnh@mit.edu ; 617-253-6430 | n/a | Laura Kamfonik | Created 12/8/2015 | - |
| 12-10-2015 | Jim Culbert | culbertj | culbertj@mghpcc.org ; 782-290-6804 | n/a | Laura Kamfonik | Created 12/10/2015 | - |
| 12-14-2015 | Piyanai Saowarattitada | cms | piyanai@bu.edu ; 603-305-3767 | n/a | Laura Kamfonik | Created 12/14/2015 | - |
| 01-06-2016 | Peter Walsh | cons3rt | peter.walsh@jackpinetech.com ; 617-816-6001 | Orran Krieger | Laura Kamfonik | Created 01-06-2016 | - |
| 01-06-2016 | Drew Janotta | cons3rt | drew.janotta@jackpinetech.com ; 978-637-2923 x213 | Peter Walsh | Laura Kamfonik | Created 01-07-2016 | - |
| 01-06-2016 | James Medeiros | cons3rt | james.medeiros@jackpinetech.com ; 978-637-2923 x213 | Peter Walsh | Laura Kamfonik | Created 01-07-2016 | - |
| 01-06-2016 | CONS3RT.orchestration | cons3rt | support@jackpinetech.com ; 978/637-2923 x201 | Peter Walsh | Laura Kamfonik | machine account; created 01-07-2016 | - |
| 01-14-2016 | Jason Hennessey | henn | henn@bu.edu ; (617) 358-1089 | n/a | Laura Kamfonik | created 01-14-2016 | - |
| 01-26-2016 | Anuj Thakur | hpc | thakur.an@husky.neu.edu ; (617) 230-2879 | n/a | Rahul Sharma | created 01-26-2016 | - |
| 01-29-2016 | Bhupendra Singh | cloud class project(flink) | singh.bhup@husky.neu.edu; (857) 313-8828 | Peter Desnoyers | Rahul Sharma | Created 01-29-2016
| - | Harshita Agrawal | - |agrawal.h@husky.neu.edu; (857) 317-0715| - | - | - |
| - | Deep Chheda | - |chheda.de@husky.neu.edu; (617) 543-6575| - | - | - |
| 02-03-2016 | ates | cloud class project(MOC Monitoring (MOCMon) Platform v2.0 ) | ates@bu.edu; 857 540 8435 | Ata | Ravi.G | Created 02-03-2016 | - |
| - | timbagby | - | timbagby@bu.edu; (508)-932-2625| - | - | - |
| 02-03-2016 | Leonid | Dataverse | isleonid@hmdc.harvard.edu; 617 496 9966 | Ata | Rahul Sharma | Created 02-03-2016 | - |
| 02-04-2016 | Jingyi Zhang | Cloud class project(Network traffic collection in the MOC) | jyzhangr@bu.edu; 617-283-9403 | Orran | Ravi.G | Created 02-04-2016 | - |
| 02-04-2016 | Naomi Joshi | Cloud class project(Lambda on OpenStack) | joshi.nao@husky.neu.edu; 848-667-3065 | Peter Desnoyers | Rahul Sharma | Created 02-04-2016 | - |
| - | Sushant Mimani | - | mimane.s@husky.neu.edu; 617-208-9552 | - | - | - |
| 02-05-2016 | Jaison Babu | Hybrid Cloud | jbabu@ccs.neu.edu; 610-615-7184 | Peter Desnoyers | Rahul Sharma | Created 02-05-2016 | - |
| - | Akash Singh | - | singh.aka@husky.neu.edu; 617-372-7259 | - | - | - |

### Limitations
1. Images : only Ubuntu and CentOS 
2. Floating IPs : default 3 per users - how can we enforce this ?
3. Only non-admin access is allowed for general users.

