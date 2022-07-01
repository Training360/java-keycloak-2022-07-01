# Tematika

* OAuth 2.0 és OpenID Connect
* JWT tokenek
* Telepítés
* Indítás Docker konténerben
* Spring Boot integráció
* Spring Security integráció
* Bejelentkezés felhasználónévvel vagy e-mail címmel
* Remember me
* Saját regisztráció
* Account console
* Enable delete account
* Password policies
* User locale
* Új attribútum felvétele
* Consent
* Sessions
* Saját téma használata
* Terms and conditions
* E-mail küldés
* GitHub/Facebook/Google integráció
* Authentication flow
* User federation, LDAP
* Events
* Logging
* REST API
* Admin CLI
* Export/import
* HA

# Referenciák

http://www.keycloak.org/
https://www.youtube.com/watch?v=duawSV69LDI

## OAuth 2.0

* Nyílt szabány erőforrás-hozzáférés kezelésére (Open Authorization)
* 2012 óta
* Fő használati területe a web, de kitér az asztali alkalmazásokra, mobil eszközökre, stb.
* Autorizációra koncentrál (hozzáférés erőforráshoz, pl. API-hoz), és nem az autentikációra
* Access Tokent használ arra, hogy leírja, mire van a felhasználónak jogosultsága
  * Formátumát nem specifikálja, de gyakran JSON Web Token (JWT), melybe adat menthető
* Elválik, hogy a felhasználó mit is akar igénybe venni, <br /> és az, hogy hol jelentkezik be
  * Google, GitHub, Facebook vagy saját szerver

## OAuth 2.0 szereplők

Szereplők
  * Resource owner: aki rendelkezik a védett erőforrásokkal, és ezekhez képes hozzáférést adni (felhasználó vagy alkalmazás)
  * Client: a szoftver, ami hozzá akar férni a védett erőforrásokhoz, ehhez Access Tokennel kell rendelkeznie
  * Authorization Server: a felhasználó itt tud hozzájutni az Access Tokenhez sikeres autentikáció után. Két végponttal rendelkezik:
    * Autorizációs végpont: itt autentikál a felhasználó
    * Token végpont: gép-gép kommunikációnál
  * Resource Server: védi és hozzáférést biztosít az erőforrásokhoz, a megfelelő Access Token meglétekor

---

## Tokenek

* Access Token: rövid lejárat
  * Formátumát nem specifikálja
  * Gyakran Bearer Token
    * Struktúrálatlan, csak egy karaktersor
    * Gyakran JSON Web Token (JWT), melybe adat menthető
* Refresh Token: ha lejár az Access Token, segítségével új Access Token kérhető le 
* Identity Token: felhasználó adatai

---

## Authorization Code

* A felhasználó (Resource Owner) elmegy az alkalmazás (Client) oldalára, ami pl. egy weboldal, mobilalkalmazás, stb.
* Az átirányít a Authorization Serverre (pl. Google vagy Facebook), megadva a saját azonosítóját és jelszavát (client id és client secret), hogy Access Tokent vagy Authorization Code-ot kérjen. Átadja a scope-ot is, hogy mire van szüksége, valamint egy endpoint (redirect) URI-t, melyre az Access Tokent vagy Authorization Code-ot várja
* Authorization Server ellenőrzi a client secretet, és a scope-ot
* Az Authorization Serveren a felhasználó bejelentkezik, ad jogot, hogy a Client elérje az erőforrást
* Az Authorization Server visszairányítja a felhasználót <br /> az alkalmazás oldalára, url paraméterként átadva neki <br /> az Access Tokent vagy Authorization Code-ot. (Esetleg a Refresh Tokent.)
* Az Access Tokennel hozzáfér az alkalmazás a Resource Serveren lévő erőforráshoz

---

## OAuth 2.0 forgatókönyvek

* Grant Type:
  * Authorization Code: klasszikus mód, ahogy egy webes alkalmazásba <br /> lépünk Facebook vagy a Google segítségével,
  Authorization Code kerül vissza, mellyel lekérhető háttérben az Access Token
  * Implicit: mobil alkalmazások, vagy SPA-k használják, Access Tokent kap azonnal (deprecated)
  * Authorization Code Grant with Proof Key for Code Exchange (PKCE): mint az Authorization Code, de egy plusz lépéssel, hogy biztonságosabb legyen mobil/SPA alkalmazásoknál
  * Resource Owner Credentials/Password: ezt olyan megbízható <br /> alkalmazások használják, melyek maguk kérik be a jelszót, nem kell átirányítás (deprecated)
  * Client Credentials: ebben az esetben <br /> nem a felhasználó kerül azonosításra, <br /> hanem az alkalmazás önmaga
  * Device Authorization Flow: korlátozott eszközökön, pl. okostévé
  * Refresh Token: új Access Token lekérése Refresh Tokennel

---

# OpenID Connect

* OpenID Connect 1.0 is a simple identity layer on top of the OAuth 2.0 protocol.
* Identity: Set of attributes related to entity.
* Egy élő emberhez több identity tartozik (attribútumok halmaza, másnak mást mutat, pl. barátoknak, főnöknek)

---

# Keycloak bemutatás

* Open Source Identity and Access Management
* Autorizáció és autentikáció rábízható (pl. felhasználók tárolása, password policy, e-mail küldés)
* SSO, logout
* Identity Brokering
  *  OpenID Connect vagy SAML 2.0 Identity Providers
  *  Social Login
* User federation
  * Active Directory, LDAP, Relational database
* Admin console
* CLI és REST API
* Account management console
* Audit naplózás
* Clusterelhető
* Könnyen integrálható (pl. Spring Security)
* Könnyen kiegészíthető

---

# Keycloak support

* Sponsored by Red Hat
* Red Hat Single Sign-On, based on the Keycloak

---

# Alkalmazás architektúra

---

# Architektúra indítása

```
echo 127.0.0.1 keycloak.training >> C:\Windows\System32\drivers\etc\hosts
echo 127.0.0.1 employees-ui.training >> C:\Windows\System32\drivers\etc\hosts
echo 127.0.0.1 employees.training >> C:\Windows\System32\drivers\etc\hosts

docker compose up
```

A Keycloak elérhető a `http://keycloak.training:8080` címen.

A frontend címe: `http://employees-ui.training:8081`

A backend címe: `http://employees.training:8082`


## Realm

Realm nélkül végtelen átirányítás
Client nélkül: Client not found

* Létre kell hozni egy Realm-et (`employees`)
* Létre kell hozni egy klienst, amihez meg kell adni annak azonosítóját (`employees-ui`), <br /> és hogy milyen url-en érhető el (`http://employees-ui.training:8081`)
  * Base URL is legyen beadva, ez jelenik meg pl. az Account console-on
* Létre kell hozni a szerepköröket (`employees_user`)
* Létre kell hozni egy felhasználót (a _Email Verified_ legyen _On_ értéken, hogy be lehessen vele jelentkezni), beállítani a jelszavát (a _Temporary_ értéke legyen _Off_, hogy ne kelljen jelszót módosítani), <br /> valamint hozzáadni a szerepkört (`johndoe`)

`Invalid parameter: redirect_uri`: ha localhoston keresztül érem el a frontendet

## Bejelentkezés menete

https://jwt.io/

GET http://keycloak.training:8080/auth/realms/employees/protocol/openid-connect/auth?response_type=code&client_id=employees-ui&redirect_uri=http%3A%2F%2Femployees-ui.training%3A8081%2Fsso%2Flogin&state=9ff87acb-bc9c-47fc-ad6a-b7b08a266883&login=true&scope=openid

POST http://keycloak.training:8080/auth/realms/employees/login-actions/authenticate?session_code=9JuYI_b7QtYo3uguXg6cdfX9EcRQE9XV6IVZy2YztEA&execution=a8fb7620-a5b5-4dc2-805c-abde771fd618&client_id=employees-ui&tab_id=Jvy4wO59qAY

GET http://employees-ui.training:8081/sso/login?state=9ff87acb-bc9c-47fc-ad6a-b7b08a266883&session_state=9d0eb37b-38d1-4d3b-a5f2-2d00f9b60567&code=ea8b3e50-c146-4399-8625-1d4271ba30d5.9d0eb37b-38d1-4d3b-a5f2-2d00f9b60567.f6fbdd9b-e0be-4ff1-bcbf-a581b34a44a5

## Forráskód

`keycloak.auth-server-url`: server url
`keycloak.realm`: realm neve
`keycloak.resource`: client id
`keycloak.public-client`: nem kell client credentials

# Delegálás hátsó microservice felé

employees

http://employees.training:8082/api/employees
There was an unexpected error (type=Unauthorized, status=401).

```shell
docker compose logs -f employees
```

# Forráskód

`bearer-only`: service esetén `true` értékre kell állítani, nem fog autentikálni felhasználót, hanem csak a bearer tokent ellenőrzi

## Kipróbálása

* Konfiguráció leírása:

http://keycloak.training:8080/auth/realms/employees/.well-known/openid-configuration

* A következő címen lekérhető a tanúsítvány

http://keycloak.training:8080/auth/realms/employees/protocol/openid-connect/certs

 ```
 curl -s --data "grant_type=password&scope=openid&client_id=employees-ui&username=johndoe&password=johndoe" http://keycloak.training:8080/auth/realms/employees/protocol/openid-connect/token


 curl -s --header "Authorization: bearer eyJhbGciOiJSUzI1NiIsInR5cCIgOiAiSldUIiwia2lkIiA6ICJtVk5DcHpFMXNPM2V0QmFmeWJ3WktqMWVUQVROMEU2UTlPN3p2VDU3bGJrIn0.eyJleHAiOjE2NTY2MjAyNjEsImlhdCI6MTY1NjYxOTk2MSwianRpIjoiMzIwMzNjM2UtMTUxZC00ZTMyLWIwNTktMThhMjRmMjk5MGZlIiwiaXNzIjoiaHR0cDovL2tleWNsb2FrLnRyYWluaW5nOjgwODAvYXV0aC9yZWFsbXMvZW1wbG95ZWVzIiwiYXVkIjoiYWNjb3VudCIsInN1YiI6IjI1Mzk5YjU0LWI3NWUtNDkwMi1hNWZhLWI4ZDZiYzA5MmI3NiIsInR5cCI6IkJlYXJlciIsImF6cCI6ImVtcGxveWVlcy11aSIsInNlc3Npb25fc3RhdGUiOiJjNTQ4MDEwNy02NzVlLTQ2YTktYTI3My02YmRiMTBjYWUxYzMiLCJhY3IiOiIxIiwiYWxsb3dlZC1vcmlnaW5zIjpbImh0dHA6Ly9lbXBsb3llZXMtdWkudHJhaW5pbmc6ODA4MSJdLCJyZWFsbV9hY2Nlc3MiOnsicm9sZXMiOlsiZGVmYXVsdC1yb2xlcy1lbXBsb3llZXMiLCJvZmZsaW5lX2FjY2VzcyIsInVtYV9hdXRob3JpemF0aW9uIiwiZW1wbG95ZWVzX3VzZXIiXX0sInJlc291cmNlX2FjY2VzcyI6eyJhY2NvdW50Ijp7InJvbGVzIjpbIm1hbmFnZS1hY2NvdW50IiwibWFuYWdlLWFjY291bnQtbGlua3MiLCJ2aWV3LXByb2ZpbGUiXX19LCJzY29wZSI6Im9wZW5pZCBwcm9maWxlIGVtYWlsIiwic2lkIjoiYzU0ODAxMDctNjc1ZS00NmE5LWEyNzMtNmJkYjEwY2FlMWMzIiwiZW1haWxfdmVyaWZpZWQiOnRydWUsInByZWZlcnJlZF91c2VybmFtZSI6ImpvaG5kb2UifQ.is7qlTacTHxXzP7VkRRKOpw4cNbQDu2Zu9GrGVbfcziY7dtPeRLtPLFk-WwF5qdSBZOT9gnwNeVCa1ogMJsjOChH4PubsZrx4qHo1PZ3xLyRY0foa47V1SFTExMP-6yhCn0Sj76XT_2awYDGzd_OgzUq4CzNMtb2SlE6FiyV9CbHXiTPh-JJ0gbs1UeTkYJhSgEP_L5AEIME8Xuxgp1cyF1xj_n17DIRxZyQEUpvKpxlA9irYVwGuZ8ImQyFW9i7H5fE_QA-zeturh0vDfEvDtaHahSOfUNYDGHKZaeTt8tZzStQUiK2xlwMuML4-x6ucN3_z9Sic1PaRNA1Lo8iKw" http://employees.training:8082/api/employees
 ```

# Postman

Authorization/ OAuth 2.0
Grant Type: Password
Client id: employees-ui
Scope: openid


# Bejelentkezés felhasználónévvel vagy e-mail címmel

Be lehet jelentkezni e-mail címmel is

# Remember me

Böngésző bezárása után is megmarad a bejelentkezés

(Kipróbálható másik Chrome profillal)

# Saját regisztráció

Login/User registration

Nem kapja meg a jogosultságot

`default-roles-employees` role-ba beletenni az `employees_user` role-t

# Account console

http://localhost:8085/auth/realms/employees/account

# Enable delete account

https://www.keycloak.org/docs/latest/server_admin/#proc-allow-user-to-delete-account_server_administration_guide

Authentication / Required Actions

Role Mappings / Client Roles / account / delete-account

# Password policies

Authentication / Password policy

# User locale

Realm Settings / Themes / Internationalization Enabled
Default Locale
Átmegy a JWT-ben is
Megjelenik a login formon is

# Új attribútum felvétele

avatar_url
https://avatars.githubusercontent.com/training360

Clients / Mappers
Create Protocol Mapper
User attribute

Name: `avatar_url`
User Attribute: `avatar_url`
Token Claim Name: `avatar_url`
Claim JSON Type: `String`


# Consent

* Client-nél Consent Required

## Beállítani, hogy az attribute-ot jóvá lehessen hagyni

* Ki kell törölni a mappert
* Fel kell venni egy új client scope-ot
* Display On Consent Screen
* Abba egy token mapper - user attribute-ot

# Sessions nyomon követése

Usernél

Manage / Sessions / Logout all

(Felhasználó nem látja a saját session-jeit)

# Saját téma

# Terms and conditions

Authentication/Required actions / Terms and conditions / Enabled / Default Action

# E-mail küldés

Realm settings / Email

Login / 
Verify email
Forgot password


# GitHub integráció

Settings / Developer settings / OAuth Apps

* Először fel kell venni egy appot a GitHubon
* Itt kell a client id
* Kell generálni egy client secret-et
* Homepage URL: `http://keycloak.training:8080/`
* Auth callback URL: `http://keycloak.training:8080/auth/realms/employees`

* Kell Mappers / Attribute importer


# Authentication flow (Magic link)

# User federation, LDAP


kubectl apply -f openldap-secret.yaml
kubectl apply -f openldap-deployment.yaml
kubectl logs -f pod/openldap-5b6b7c667c-sfq5s


ldapsearch -H ldap://localhost:1389 -D "cn=admin,dc=example,dc=org" -w admin -b "ou=users,dc=example,dc=org"


Edit mode: writeable
Vendor: other
User object class: inetOrgPerson
Connection url: ldap://openldap:1389
Users DN: ou=users,dc=example,dc=org
Bind DN: cn=admin,dc=example,dc=org
Bind credential: admin
Trust e-mail ON

# DB (MariaDB)

```shell
docker network create keycloak-network
docker run -d --name keycloak-mariadb --net keycloak-network -p 3307:3306 -e MARIADB_ALLOW_EMPTY_ROOT_PASSWORD=true -e MARIADB_DATABASE=keycloak -e MARIADB_USER=keycloak -e MYSQL_PASSWORD=password mariadb:10.7.1
docker run -d --net keycloak-network -e DB_VENDOR=mariadb -e KEYCLOAK_USER=root -e KEYCLOAK_PASSWORD=root -e DB_ADDR=keycloak-mariadb -e DB_USER=keycloak -e DB_PASSWORD=password -p 8086:8080 --name keycloak-prod jboss/keycloak:16.1.1
```

```
kubectl exec -it pod/keycloak-mariadb-5bf759dc66-j8pzw -- mysql keycloak
show tables;
select * from USER_ENTITY;

select * from EVENT_ENTITY;
```

# Events

Manage / Events / Config / Save Events ON
Hibás bejelentkezés
Login Eventsnél megjelenik
Konzol logon is megjelenik

(Felhasználó nem látja a saját logját)


# X.509 client certificate user authentication

https://www.keycloak.org/docs/16.1/server_admin/index.html#_x509

# Metrics

?

Nincs beépített megoldás

https://www.keycloak.org/docs/latest/server_installation/#_operator

# Logging

# REST API

https://www.keycloak.org/docs-api/15.0/rest-api/

curl -s --data "grant_type=password&scope=openid&client_id=admin-cli&username=root&password=root" http://keycloak.training:30010/auth/realms/master/protocol/openid-connect/token
curl --header "Accept: application/json" --header "Authorization: bearer eyJhbGciOiJSUzI1NiIsInR5cCIgOiAiSldUIiwia2lkIiA6ICJWbXBfVGNPWWctUTBnbHV3VUFPVXlGQW4wekhDTHBlWi13Y2QxNHRfdGpnIn0.eyJleHAiOjE2NDU3MTA5MTMsImlhdCI6MTY0NTcxMDg1MywianRpIjoiMjE3NjkzYWQtNzQzYi00OWUxLTk3YzctMmUzOGIwZmZhYWRjIiwiaXNzIjoiaHR0cDovL2tleWNsb2FrLnRyYWluaW5nOjMwMDEwL2F1dGgvcmVhbG1zL21hc3RlciIsInN1YiI6IjZmNTQ0YzAwLTVkMDMtNDRlOC04MTQzLTAyY2Q0NzA5MTFkOCIsInR5cCI6IkJlYXJlciIsImF6cCI6ImFkbWluLWNsaSIsInNlc3Npb25fc3RhdGUiOiIzNTAyODRkMy0wMmM5LTQ5ZGMtYWU2Ny03NTEwYmU1OWM4NmEiLCJhY3IiOiIxIiwic2NvcGUiOiJvcGVuaWQgZW1haWwgcHJvZmlsZSIsInNpZCI6IjM1MDI4NGQzLTAyYzktNDlkYy1hZTY3LTc1MTBiZTU5Yzg2YSIsImVtYWlsX3ZlcmlmaWVkIjpmYWxzZSwicHJlZmVycmVkX3VzZXJuYW1lIjoicm9vdCIsImVtYWlsIjoia2V5Y2xvYWtAbG9jYWxob3N0In0.CtipTgWP_RzhsYdVD2IHKZ4OyDaPu_YTtFVl5hhk6QBIGWil_6oQ0suSsLifVBjfcqd5euO5zukGnUm_z00MpsdLndu6ak84GBL_WSeWT6beGiDxH6OKqhZjOAo7tY4c2hblOJ5aOYb5zUfdMD7mVqHQslL4wJVBaJnuSeSLc2-V4H3ecUszFGSCbHMdf5Ejo3ovxNd-nxbaNKwAKAYwVuwvLlY9St9byesfPVTelnzXtdxuCgEmB9p2NFsZEX7iumfQ_wWvU2rFQcF_ekme2eCg5ClwYQs219uDRDTTN1sww0CkJ7N800XqiIl8mXobSWIBDO9oZbTMfUtxDLm9Pg" http://keycloak.training:30010/auth/admin/realms/employees/users

Postmannel

# Admin CLI

kubectl exec -it pod/keycloak-7b5fb99f-zpbj6 -- /opt/jboss/keycloak/bin/kcadm.sh config credentials --server http://127.0.0.1:8080/auth --realm master get users --user root

kubectl exec -it pod/keycloak-7b5fb99f-zpbj6 -- /opt/jboss/keycloak/bin/kcadm.sh get users -r employees
kubectl exec -it pod/keycloak-7b5fb99f-zpbj6 -- /opt/jboss/keycloak/bin/kcadm.sh get events -r employees

kubectl exec -it pod/keycloak-7b5fb99f-zpbj6 -- /opt/jboss/keycloak/bin/kcadm.sh create users -s username=johnsmith -s enabled=true -r employees
kubectl exec -it pod/keycloak-7b5fb99f-zpbj6 -- /opt/jboss/keycloak/bin/kcadm.sh set-password --username johnsmith --new-password johnsmith  -r employees

# Export/import

# HA

JGroups and Infinispan
https://www.keycloak.org/server/caching

# Reverse proxy mögött ? 