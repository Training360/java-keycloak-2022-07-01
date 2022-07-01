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