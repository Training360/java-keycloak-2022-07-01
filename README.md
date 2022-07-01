# Keycloak képzés

## Docker indítása

```
net localgroup docker-users %USERDOMAIN%\%USERNAME% /add
docker ps
docker run hello-world
```

## Alkalmazás indítása

```
git clone https://github.com/Training360/java-keycloak-2022-07-01
cd java-keycloak-2022-07-01\employees-arch
docker compose up -d
```

A `C:\Windows\System32\drivers\etc\hosts` fájlba írjuk bele:

```
127.0.0.1 keycloak.training
127.0.0.1 employees.training
127.0.0.1 employees-ui.training
```

Akár így:

```shell
echo 127.0.0.1 keycloak.training >> C:\Windows\System32\drivers\etc\hosts
echo 127.0.0.1 employees-ui.training >> C:\Windows\System32\drivers\etc\hosts
echo 127.0.0.1 employees.training >> C:\Windows\System32\drivers\etc\hosts
```

## Realm létrehozása

* Név: `employees`

Client létrehozása:

* Client ID: `employees-ui`
* Root URL: `http://employees-ui.training:8081/`
  * Base URL is legyen beadva, ez jelenik meg pl. az Account console-on

Szerepkör létrehozása

* Létre kell hozni a szerepköröket (`employees_user`)

Felhasználó létrehozása

* Létre kell hozni egy felhasználót (a _Email Verified_ legyen _On_ értéken, hogy be lehessen vele jelentkezni), beállítani a jelszavát (a _Temporary_ értéke legyen _Off_, hogy ne kelljen jelszót módosítani), <br /> valamint hozzáadni a szerepkört (`johndoe`)

## Információk lekérése

```
http://keycloak.training:8080/auth/realms/employees/.well-known/openid-configuration
http://keycloak.training:8080/auth/realms/employees/protocol/openid-connect/certs
```

## Backend tesztelése

```
curl http://employees.training:8082/api/employees
curl --header "Authorization: bearer xxxxx" http://employees.training:8082/api/employees
```

## Backend tesztelése Postmannel

Token name: `john`
Grant type: `Password credentials`
Access token url: `http://keycloak.training:8080/auth/realms/employees/protocol/openid-connect/token`
Client id: `employees-ui`
Username: `johndoe`
Password: `johndoe`
Scope: `openid`

## OpenLDAP

```
docker exec -it a60 ldapsearch -H ldap://localhost:1389 -D "cn=admin,dc=example,dc=org" -w admin -b "ou=users,dc=example,dc=org"
```

Edit mode: writeable
Vendor: other
User object class: inetOrgPerson
Connection url: ldap://openldap:1389
Users DN: ou=users,dc=example,dc=org
Bind DN: cn=admin,dc=example,dc=org
Bind credential: admin
Trust e-mail ON

