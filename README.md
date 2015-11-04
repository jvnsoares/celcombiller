# Used third-party libraries

* pyst
  Interface with Asterisk using either AGI or Manager interfaces
* sqlalchemy
  Database interface

# Setup developemnt enrironment

```bash
$ virtualenv -p /usr/bin/python2.7 venv
$ source venv/bin/activate
$ pip install --allow-external pyst --allow-unverified pyst -r requirements.txt
```

## Database setup

Each SIP user must be inserted in the database of users with a balance. The python code snippet bellow is an example of a insertion of two users.

```python
from flask import Flask
from flask.ext.sqlalchemy import SQLAlchemy
from apiConfig import db
from models import User

admin = User('admin', 'adm123', '999999999', '9999', True)
guest = User('guest', '123123', '999999999', '0000', False)

db.session.add(admin)
db.session.add(guest)
db.session.commit()
```

please check adduser.py

## Using curl to test api

###USER

login

```bash
curl -c cookiefile -d "username=admin&password=adm123" -X POST -s http://localhost:5000/login
```

add user

```bash
curl -b cookiefile -H "Content-Type: application/json" -X POST -d '{"username":"yourusername","password":"yourpassword","clid":"999999999","balance":"0","admin":'false'}' -s http://localhost:5000/api/users
```

update user

```bash
curl -X PATCH -H "Content-Type: application/json" -d '{"username":"yournewusername","password":"yournewpassowrd"}' -s http://localhost:5000/api/users/youroldusername -b cookiefile
```
remove user

```bash
curl -X DELETE -s http://localhost:5000/api/users/yourusername -b cookiefile
```

###GROUPS

add group

```bash
curl -X POST -H "Content-Type: application/json" -d '{"name":"group_name","day":1, "month":1, "year":3000, "count":10}' -s http://localhost:5000/api/groups
```

update group

```bash
curl -X PATCH -H "Content-Type: application/json" -d '{}' -s http://localost:5000/api/groups/group_name
```


## Asterisk setup

With a working Asterisk server you must have at least two SIP accounts. An example of the corresponding section of `sip.conf` follows.

```
[1000000000]
type=friend
secret=laps
host=dynamic
context=celcom

[2000000000]
type=friend
secret=laps
host=dynamic
context=celcom
```

The referenced context in `sip.conf` must exist in the `extensions.conf` the code bellow is an example.

```
[celcom]
exten => _ZXXXXXXXX,1,AGI(celcombiller)
```

celcombiller is an AGI, implemented in the file [celcombiller.py](celcombiller.py) and it must run in the virtual environment created in the first section, a file with something like the content bellow should be created in `/var/lib/asterisk/agi-bin` with execution permission.

```bash
#!/bin/bash
/home/psb/Dropbox/git-projects/celcombiller/venv/bin/python /home/psb/git-projects/celcombiller/celcombiller.py
```





You dont need to write a constructor, you can either treat the addresses property on a Person instance as a list:

a = Address(email='foo@bar.com')
p = Person(name='foo')
p.addresses.append(a)

Or you can pass a list of addresses to the Person constructor

a = Address(email='foo@bar.com')
p = Person(name='foo', addresses=[a])

In either case you can then access the addresses on your Person instance like so:

db.session.add(p)
db.session.add(a)
db.session.commit()
print p.addresses.count() # 1
print p.addresses[0] # <Address object at 0x10c098ed0>
print p.addresses.filter_by(email='foo@bar.com').count() # 1

