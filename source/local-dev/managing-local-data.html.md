---
title: Managing Local Data
---

When data components are provisioned inside of a nanobox VM, each is given its own virtual IP. These allow you to connect to your data services from outside of your running VM and manage them with your client of choice.

To view the IPs and connection credentials for your data components, use `dev info` command.

```bash
$ nanobox dev info

Local Domain   : app-name.dev
App Name       : app-name
Nanobox state  : running
Nanobox Files  : ~/.nanobox/apps/nanobox-ruby-test

///////// SERVICES /////////

data.redis :
   image : nanobox/redis
   host : 192.168.99.1
   ports : [6379]
data.db :
   image : nanobox/mysql
   host : 192.168.99.2
   ports : [3306]
   username : nanobox
   password : password
```

### Connecting to a Service
Simply use the credentials provided in the tunnel output to connect to your services. Below is an example of connecting to a Redis service inside of a nanobox VM using `redis-cli`.

#### Example Connection to Redis Service
```bash
$ nanobox dev info

...

data.redis :
   image : nanobox/redis
   host : 192.168.99.1
   ports : [6379]

...

# In a separate terminal session

$ redis-cli -h 192.168.99.1 -p 6379
app-name.dev:6379>
```