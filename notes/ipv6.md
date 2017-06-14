## Hub
```
$ sudo cf-agent --bootstrap "fdff:ffff:ffff:ffff:ffff:ffff:ffff:ffff"
R: Bootstrapping from host 'fdff:ffff:ffff:ffff:ffff:ffff:ffff:ffff' via built-in policy '/var/cfengine/inputs/failsafe.cf'
R: This host assumes the role of policy server
R: Updated local policy from policy server
  notice: Bootstrap to 'fdff:ffff:ffff:ffff:ffff:ffff:ffff:ffff' completed successfully!
$ sudo cf-net connect
Connected & authenticated successfully to 'fdff:ffff:ffff:ffff:ffff:ffff:ffff:ffff'
$ sudo cf-net -H "fdff:ffff:ffff:ffff:ffff:ffff:ffff:fff0" connect
Connected & authenticated successfully to 'fdff:ffff:ffff:ffff:ffff:ffff:ffff:fff0'
```

## Client
```
$ sudo cf-agent --bootstrap "fdff:ffff:ffff:ffff:ffff:ffff:ffff:ffff"
  notice: Bootstrap mode: implicitly trusting server, use --trust-server=no if server trust is already established
R: Bootstrapping from host 'fdff:ffff:ffff:ffff:ffff:ffff:ffff:ffff' via built-in policy '/var/cfengine/inputs/failsafe.cf'
R: This autonomous node assumes the role of voluntary client
R: Updated local policy from policy server
  notice: Bootstrap to 'fdff:ffff:ffff:ffff:ffff:ffff:ffff:ffff' completed successfully!
$ sudo cf-net -H "fdff:ffff:ffff:ffff:ffff:ffff:ffff:ffff" connect
Connected & authenticated successfully to 'fdff:ffff:ffff:ffff:ffff:ffff:ffff:ffff'
$ sudo cf-net -H "fdff:ffff:ffff:ffff:ffff:ffff:ffff:fff0" connect
Connected & authenticated successfully to 'fdff:ffff:ffff:ffff:ffff:ffff:ffff:fff0'
```
