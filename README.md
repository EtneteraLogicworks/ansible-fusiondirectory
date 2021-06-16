# Role fusiondirectory

Role `fusiondirectory`:

- Závisí na rolích `webserver` a `openldap`.
- Nainstaluje webovou aplikaci [FusionDirectory](https://www.fusiondirectory.org).
- Přidá do OpenLDAPu schémata pro podporu FusionDirectory a vytvoří v OpenLDAP databázi
  objekty nutné pro fungování FusionDirectory. Stane se vše, co by jinak provedl webový
  průvodce prvotním nastavením.
- Nasadí Apache virtualhost pro FusionDirectory web s HTTPS.

Závislosti:

- Je nutné mít nainstalovaný balík `python-ldap` na počítači, který spouští Ansible.


## Proměnné

| Proměnná         | Povinná | Výchozí              | Popis                                |
| ---------------- | ------- | -------------------- | ------------------------------------ |
| fusiondirectory  | ano     |                      | Slovník pro konfiguraci              |
|                  |         |                      |                                      |
| .access          | ano     |                      | Slovník konfigurace uživatele pro přístup k OpenLDAP |
| ..cn             | ne      | fd                   | CN uživatele                         |
| ..dn             | ne      | cn=fd,ou=users,{{ openldap.searchbase }} | DN uživatele     |
| ..password       | ano     |                      | Heslo uživatele                      |
|                  |         |                      |                                      |
| .admin           | ano     |                      | Slovník konfigurace fusiondirectory root uživatele |
| ..uid            | ne      | fdadmin              | UID uživatele                         |
| ..dn             | ne      | uid=fdadmin,ou=people,{{ openldap.searchbase }} | DN uživatele |
| ..password       | ano     |                      | Heslo uživatele                      |
|                  |         |                      |                                      |
| .config          | ne      | viz defaults         | Slovník konfigurující FusionDirectory |
| .indices         | ne      |                      | Pole dodatečných indexů pro OpenLDAP |
| .plugins         | ne      | systems, mail, alias | Seznam instalovaných FD pluginů      |
| .schemas         | ne      | systems, mail, alias | Seznam instalovaných FD schémat      |
|                  |         |                      |                                      |
| .vhost           | ano     |                      |  Slovník pro konfiguraci Apache virtualhost |
| ..name           | ne      | fusiondirectory      | Jméno webu                           |
| ..www_root       | ne      | `/usr/share/fusiondirectory/html` |  Root FD webu           |
| ..site_name      | ano     |                      | Doménové jméno webu                  |
| ..aliases        | ne      |                      | Další doménová jména pro web         |
| ..custom_access  | ne      |                      | Nastavení omezení přístupu k webu    |
| ..admin_mail     | ne      | {{ admin_mail }}     | Mail správce serveru                 |
| ..https_enabled  | ne      | false                | Zapnutí *https* vhosta               |
| ..https_letsencrypt | ne   | false                | Zíkání let's encrypt certifikátu     |
| ..https_redirect | ne      | false                | Zapnutí přesměrování na *https*      |
| ..cert           | ne      | `/etc/ssl/certs/ssl-cert-snakeoil.pem` | Cesta k TLS certifikátu |
| ..key            | ne      | `/etc/ssl/private/ssl-cert-snakeoil.key` | Cesta k privátnímu klíči |
| ..chain          | ne      |                      | Cesta k souboru s certifikáty mezilehlých autorit |


## Příklad použití

```yaml
fusiondirectory:
  indices:
    - 'gosaMailAlternateAddress eq'
  vhost:
    name: 'fusiondirectory'
    site_name: 'directory.logicworks.cz'
    https_letsencrypt: true
    custom_access: |
      Require ip 1.2.3.4
```
