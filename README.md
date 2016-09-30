to compile
```bash
make -f /usr/share/selinux/devel/Makefile
```

make sure you're have:
```bash
yum install libselinux-policy-devel       
yum install selinux-policy-devel.noarch
```

Some notes:
unfortunatelly so far there's now way to do portcon from module policy def , so we need to do it from shell or CIL

so after installing the module with
```bash
semodule -i vault.pp
```
we need to do
```bash
semanage port -a -t vault_port_t -p tcp 5200
```

to tag the port with the type we've created in the policy definition.

To remove the module is tricky too ,  as the type is defined in the policy

```bash
semanage port -d -t vault_port_t -p tcp 5200
```

and remove the module
```bash
semodule -r vault
```
and you're clean.

Relabel the vault binary with
```bash
restorecon -r /usr/sbin/vault
```

This goes to uncofined when you remove the module so no issues here.

#Init files
```bash
cp vault  /etc/init.d
mkdir /etc/vault && cp vault.conf /etc/vault
chkconfig --add vault
systemctl daemon-relad
```

Usage:
```bash
systemctl start vault.service
systemctl stop vault.service
systemctl status vault.service
```







