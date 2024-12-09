---
title: 'CH4 - MCG'
---

sudo apt-get install dnsutils
sudo apt-get install bind9 bind9utils bind9-doc

nslookup
dig 

sudo apt-get install slapd ldap-utils
sudo apt-get install phpldapadmin

sudo dpkg-reconfigure slapd

ldapadd -x -D cn=admin,dc=proyecto-empresa,dc=local -w ieselcaminas -f profesores.ldif
ldapmodify -x -D cn=admin,dc=proyecto-empresa,dc=local -w ieselcaminas -f cambiar_usuario.ldif 
ldapdelete -x -D cn=admin,dc=proyecto-empresa,dc=local -w ieselcaminas uid=alu02,ou=usuarios,dc=proyecto-empresa,dc=local
