# Bootstrapping to custom ports and/or host names
While implementing the bootstrap to custom ports and host names functionality I wrote this small "guide" on how to use the features and see if it worked.

**Important:** I have the repositories `~/cfengine/masterfiles` and `~/cfengine/core` on my machine. If you want to install from source you will need to adjust these paths accordingly to your folder locations.

## PREREQUISITES

### Add hostname (Optional)
If you want to test with a custom hostname, you can add it to `/etc/hosts`:
```
sudo emacs /etc/hosts
```

`/etc/hosts`:
```
127.0.0.1 localhost
192.168.50.50 myhostname
...
```
(Replace 192.168.50.50 with the IP of the hub & myhostname with a name you want to use). This is probably most useful on the client, but you can do it on both, for simplicity.

## INSTALL
If you are not installing from source, but using prebuilt enterprise(or community) you will install in the regular way and can skip this part.
### Install masterfiles (only on hub)
```
cd ~/cfengine/masterfiles && ./autogen.sh && ./configure.sh && sudo make -j2 install
```

### Install core on hub & client
To install core simply:
```
cd ~/cfengine/core && sudo make -j2 install
```

## CHANGE SERVER PORT (OPTIONAL)
If you want to use a custom port, you will have to edit the `cf-serverd` policy:
```
sudo emacs /var/cfengine/masterfiles/controls/cf_serverd.cf
```
Add `port => "5309";` in `body server control`:
```
body server control

{
      port => "5309";
...
```

## BOOTSTRAP
### Bootstrap both hub & client
There are many ways you can bootstrap now:
```
sudo /var/cfengine/bin/cf-agent --bootstrap 192.168.50.50:5309
sudo /var/cfengine/bin/cf-agent --bootstrap myhostname:5309
sudo /var/cfengine/bin/cf-agent --bootstrap myhostname
```
If you do not specify a port the default port will be used (5308).
**Note:** When bootstrapping on the hub it will not care about what port you specify. It will recognize it's bootstrapping to itself and do a local file copy.

## TESTING

### Make a change on hub
```
sudo emacs /var/cfengine/masterfiles/update.cf
```
Make some change(comment for testing)

### Propagate change on hub
```
sudo /var/cfengine/bin/cf-agent -f update.cf -K
sudo /var/cfengine/bin/cf-agent
```

### Get change on client
```
sudo /var/cfengine/bin/cf-agent -f update.cf -K
```

### See change on client
```
sudo emacs /var/cfengine/inputs/update.cf
```

## EXTRAS
### Reinstall + bootstrap bash script
This bash script shows how to stop running processes, reinstall from source(core, not masterfiles) and bootstrap. Assumes you have already installed masterfiles on the hub.
`testscript.sh`:
```Bash
killall cf-execd
killall cf-serverd
killall cf-hub
killall cf-agent
rm -rf /var/cfengine/inputs/
cd ~/cfengine/core
make -j2 install
/var/cfengine/bin/cf-agent --bootstrap myhostname:5309
```
(This will not undo the change you did to `cf_serverd.cf`)
Must run using sudo:
```
sudo ./testscript.sh
```
**Note** that this only runs bootstrap, and thus doesn't test subsequent runs of cf-agent.
