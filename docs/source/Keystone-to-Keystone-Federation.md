# Keystone to Keystone Federation
[Trello board](https://trello.com/b/BQQFdyLx/os-mix-match-federation)
* To spin up vm with devstack in one command see [vagrant recipe](https://github.com/CCI-MOC/k2k-fed/tree/master/devstack-k2k)
* [Tutorial to set up K2K with Kilo](http://blog.rodrigods.com/it-is-time-to-play-with-keystone-to-keystone-federation-in-kilo/)
  * small modification needed will update later see trello card for detail for now
  * [python script and README to explain details](https://github.com/minggLu/MOC/tree/master/os-federation)

Will update more information soon 

### Mapping 
(Minying 07/23/15)

Due to the complexity of Keystone v3 and in order for the team to understand this better, I'm gonna start from the very basic and then get to our multi-directional keystone federation design. 

### The concepts of user, group, domain, roles and projects in keystone

**Groups**

* a group of users
* role can be grant to a group, applying to all the users in the group

**Domains**

* may contain users, projects and groups
* the important part of this to K2K is that. There's a domain called "Federated", which will contain all the ephemeral users for K2K federation 
* a domein can only be accessed by a user/group who has a role of the domain.

**Projects**

* projects belong to domain
* project can only be accessed by a user/group who has a role of the project. 

**Roles**

* roles define the permission a user has to a domain or project 
* if a user has no rule to a project/domain that means the user has no permission, can't even see it
* roles can be granted to a user or a group 

### Scoped token and unscoped token

**Unscoped token** means that a user/group has permission to look at what are the domains/projects that they can scope to (meaning they have roles of those projects/domains). But can't do anything else except ask for list or request a scoped token

**Scoped token** means that a user/group has certain privilege (depends on the role the user/group has of the project/domain) of a specific project/domain that they scoped to. And can actually perform actions like volume-attach 

### How does K2K federation works
1. User in IdP generates a saml assertion
2. Exchange the assertion with SP
3. User in IdP gets an unscoped token
4. Use the unscoped token to get project/domain ID to scoped
5. Get a scoped token
6. Profit

There're also preparation work in SP and IdP for example registering IdP endpoint in SP and SP endpoints in IdP, building mapping rules, protocols, set up shibboleth, configure keystone.conf, and generate idp metadata
* protocols indicates which mapping rules to use
* Mapping is a list of rules that map federation protocols to attributes to Identity API objects (groups/users)
  * Mapping is set up in SP, is use to map a remote object to local attributes
  * **"local"** reference to the local attributes in SP (group/user)
    * "remote" users will be map to local attributes according to the rules, ephemeral or not. (more detail in next section)
  * **"remote"** is a list of objects. 
  * The local attributes will only apply (in assertion exchange) when all the remote attributes are match in the assertion. 
  * `type`: is an assertion type keyword
  * `any_one_of`: the rule is matched only if any one of the string appears under `type`
  * `not_any_of`: mutually exclusive with `any_one_of`
  * `regex`: we don't care about this for now. if set to true, the string will be evaluated as regular expression
  
An example: **Explanation is beneath the code** 

```
    "remote": [
        {
            "type": "openstack_user",
            "any_one_of": [
                "user1",
                "admin"
            ]
        }
    ]
```
  
In the above example, the remote attribute type is `openstack_user`, during assertion exchange, keystone will look for the value corresponding to key `openstack_user` and if the key value matches with any one of `admin` or `user1`, then attribute matches, rule matches, local attributes applies and user mapped. 

### How keystone handles ephemeral users in K2K
When a federated user is ephemeral, means that it doesn't have a local user in SP. A user is default ephemeral when there's no domain provided under domain (example as below)

```
   "local": [
       {
            "user": {
                "name": "username"
                "domain": {
                    "name": "domain_name"
                }
            }
       }
   ]
```

If a domain is specified under user, then the user will be treated as existing in the backend, and behave in behalf of the user in backend. 

If the user doesn't actually exist in the backend, the server will throw HTTP error code (probably 401 or 404)

see [user mapping for federated authentication](http://specs.openstack.org/openstack/keystone-specs/specs/kilo/federated-direct-user-mapping.html) for more detail on this matter

### Bi-directional K2K
A SP will also be an IdP and an IdP can also be a SP (this is a fun sentence to say)

The way it is set up right now in 216/234 pair, is like two one way road that's right next to each other, that the destination of one is the starting point of the other and vise versa. This is because we are using ephemeral users. When 234(IdP) is getting service from 216(SP) it works like it casts a shadow onto the "Federated" domain in 216 and the shadow scopes a project/domain and does stuff. But if 216 wants to work as an IdP and do federation to 234(SP), it won't be able to use the shadow of 234(IdP)'s user, because it doesn't exist on 216. 

The way it's set up now, is that 216 and 234 can communicate in 2 ways, but they create their own ephemeral users in the communication. 

```
_____216_______          ______234______ 
| (default)   | -------> | (federated) |  
| (federated) | <------- | (default)   | 
---------------          --------------- 

```
....I tried on the graph....

### Multiple mappings and protocols 
One mapping can contain a list of rules, each rule has `remote` and `local` objects. One protocol can  correspond to **only one** mapping and one IdP but one mapping can correspond to **multiple** protocols or IdP. 

**Multiple rules in a mapping**

* all rules that matched with the saml assertion will be apply together and users will be mapped correspondingly

**Multiple mappings** 

* when there are multiple mappings, it's the `protocol` in `auth_url` to decide which mapping to you
* because one protocol only correspond to one mapping and one IdP
* `auth_url` is stored in the SP entry in IdP, it's determined when we register the SP in IdP with `client.federation.service_providers.create`

**Multiple protocols**

* As I mentioned above we need to have multiple protocols to have multiple mappings
* To add a new protocol we need to change the following
  * This will provide a protocol that works the same way as `saml2` but with different name
  * `keystone.conf` file `[auth]` section
      * method and protocol path (py)
      * [keystone.auth.plugin.mapped.Mapped](https://github.com/openstack/keystone/tree/master/keystone/auth/plugins)
    * `/etc/apache2/site-available/keystone.conf`
      * Add a `LocationMatch` section for the new protocol
    * register new protocol with new mapping in SP
    * register new sp entry with the new protocol and `auth url` in IdP

### Security Concerns? 
* I think we are definitely more secure with ephemeral users than with user exists in the backend, but this is probably gonna result in chatty authentication for each request we send between 2 clouds? 
* What projects/domains a ephemeral can scoped to is really depend on SP when it assign roles of projects/domains to the group/user in mapping. So if the SP is compromised and assign admin roles of admin project or cloud admin domain to a malicious IdP user then it would be bad
* If SP trusted a malicious IdP, then all the project the IdP user has permission to will be in danger, but not the others if we are using a ephemeral user, because it's a "non-exist" user in SP that only have meaning to the projects that's the ephemeral user scoped to. 
* I can't come up with more specific things without knowing more about use cases and our thread model...
 
(to be continue on thinking....)

