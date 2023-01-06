yum -y install openldap compat-openldap openldap-clients openldap-servers openldap-servers-sql openldap-devel

 cp /usr/share/openldap-servers/DB_CONFIG.example /var/lib/ldap/DB_CONFIG

 systemctl start slapd

 systemctl enable slapd


slappasswd -s 123456

dn: olcDatabase={0}config,cn=config
changetype: modify
add: olcRootPW
olcRootPW: {SSHA}Ryh9ZreWVBmOajU5HkUY8O0uX8Q6Oh3C

ldapadd -Y EXTERNAL -H ldapi:/// -f test.ldif

https://passlib.readthedocs.io/en/stable/lib/passlib.hash.ldap_std.html

https://ldap3.readthedocs.io/en/latest/add.html

https://medium.com/analytics-vidhya/crud-operations-for-openldap-using-python-ldap3-46393e3122af

https://geekflare.com/password-generator-python-code/