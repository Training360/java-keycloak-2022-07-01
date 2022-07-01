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