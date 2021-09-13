# Llamadas y autenticacion de usuarios

## Crear usuario y loguear en aplicacion

Primero crear un usuario en keycloak. Esto se hace buscando el menu "Users" en la parte izquierda inferior del menu principal.

![Menu usuarios](/img/img1.png)

Seguidamente, crear el usuario correspondiente.

Una vez creado el usuario podemos interactuar con el API de KeyCloak en

## Autenticarse contra keycloak
```
curl   -d "client_id=admin-cli"   -d "username=franz"   -d "password=123456"   -d "grant_type=password"   "https://165.232.128.25:8443/auth/realms/master/protocol/openid-connect/token" -k
```
Con esto tenemos el access-token

El subfijo "-k" realiza la peticion ignorando el certificado, en produccion se recomienda realizar el consumo con SSL.

## Refrescar el access-token
```
curl \
  -d "client_id=admin-cli" \
  -d "refresh_token=eyJhbGciOiJIUzI1NiIsInR5cCIgOiAiSldUIiwia2lkIiA6ICI3Y2Y4NmUwYy02YTBmLTQ3NGQtYTg4Yy0wOGU0YThkNjkwMmEifQ.eyJleHAiOjE2MzE1MTE2NDIsImlhdCI6MTYzMTUwOTg0MiwianRpIjoiMzcyMjQyZWUtMjM0My00NTRiLTk2YWEtNTA5OGFjMzQyZjkyIiwiaXNzIjoiaHR0cHM6Ly8xNjUuMjMyLjEyOC4yNTo4NDQzL2F1dGgvcmVhbG1zL21hc3RlciIsImF1ZCI6Imh0dHBzOi8vMTY1LjIzMi4xMjguMjU6ODQ0My9hdXRoL3JlYWxtcy9tYXN0ZXIiLCJzdWIiOiI5ZWM0NDkwZS03MmUxLTQyNDEtYjc5NS1jNmMyMTg3ZjVlZTAiLCJ0eXAiOiJSZWZyZXNoIiwiYXpwIjoiYWRtaW4tY2xpIiwic2Vzc2lvbl9zdGF0ZSI6IjgyODc5MTkyLWMzZDUtNGRhNy05MmNhLWQ4YjgxMDI5NTYzMiIsInNjb3BlIjoicHJvZmlsZSBlbWFpbCIsInNpZCI6IjgyODc5MTkyLWMzZDUtNGRhNy05MmNhLWQ4YjgxMDI5NTYzMiJ9.fY11h7HoI10E-PMbqI9hKdGQNM9-AqvNXaJDFnCaSFc" \
  -d "grant_type=refresh_token" \
  "https://165.232.128.25:8443/auth/realms/master/protocol/openid-connect/token" -k
  ```
## Obtener datos del Realm

Para obtener los datos, ejecutar:
```
  curl \
  -H "Authorization: bearer <access-token>" \
  "http://localhost:8080/auth/admin/realms/master"

```
Ejemplo:
```
  curl \
  -H "Authorization: bearer eyJhbGciOiJSUzI1NiIsInR5cCIgOiAiSldUIiwia2lkIiA6ICJQUnpSckpzd21yUHdpUmt3cUJVSWVhY2sxWFhXRG9Ia2xHUDVMSDk2cTcwIn0.eyJleHAiOjE2MzE1MTAyOTksImlhdCI6MTYzMTUxMDIzOSwianRpIjoiNjNhZGUwMTgtYjJhNC00OWE0LWJjNjctMmNiNmVhZjBlNTFiIiwiaXNzIjoiaHR0cHM6Ly8xNjUuMjMyLjEyOC4yNTo4NDQzL2F1dGgvcmVhbG1zL21hc3RlciIsInN1YiI6IjllYzQ0OTBlLTcyZTEtNDI0MS1iNzk1LWM2YzIxODdmNWVlMCIsInR5cCI6IkJlYXJlciIsImF6cCI6ImFkbWluLWNsaSIsInNlc3Npb25fc3RhdGUiOiIwNDYyNWQ0Ny02YWIwLTRmNGMtOGJlYi1lNWU2ODgxYTUxNzYiLCJhY3IiOiIxIiwic2NvcGUiOiJwcm9maWxlIGVtYWlsIiwic2lkIjoiMDQ2MjVkNDctNmFiMC00ZjRjLThiZWItZTVlNjg4MWE1MTc2IiwiZW1haWxfdmVyaWZpZWQiOmZhbHNlLCJuYW1lIjoiRnJhbnogSHVheXRhIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiZnJhbnoiLCJnaXZlbl9uYW1lIjoiRnJhbnoiLCJmYW1pbHlfbmFtZSI6Ikh1YXl0YSIsImVtYWlsIjoiZnJhbnpAZ21haWwuY29tIn0.TXM4A9kW1GBi_JXgxg5g8aUhgC-OUUTH3OvMe7-YaNsKBth1rf2MGQ0vODwMyXMoKY_gGdavdxykL7VGBEW1rp717-JGn9gawoBd8mJ4YGTT9ABP2S8WZbavGHickwTW9z1_t1tB7jYtVjNZ8Cava5Nngf-nS9QdAaTz7s-Ewzv2Rnjr8lvVZGB6MLx0TlR-XCWq-4UVqQPf4V-7MR9FSVeRdiwUTrRQCZWHFbO9chtqv1eHV8FSm0Wktqr1qYxSd1eeQRjwks9t3FsRpa26n9C2o_InEu64IELldSn2FiXMM7TxGspgxJwcpnzaVBPbk3zPayBPaNLfjc_wMt4lXw" \
  "https://165.232.128.25:8443/auth/admin/realms/master" -k

```
