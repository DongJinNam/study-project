# Kong API Gateway

kong api gateway 학습을 위한 Repository 입니다.



* 참고링크
  * https://bit.ly/2wDzK8P
  * https://docs.konghq.com/install/docker/?_ga=2.249049090.487633138.1583712585-339693504.1580147547



* window port check

  ```powershell
  netstat -an | select-string -pattern "listening"
  ```

  

1. create docker net

   ```sh
   $ docker network create kong-net
   ```

2. start your database (shell)

   ```sh
   $ docker run -d --name kong-database \
     --network kong-net \
     -p 5555:5432 \
     -e "POSTGRES_USER=kong" \
     -e "POSTGRES_DB=kong" \
     -e "POSTGRES_PASSWORD=kong" \
     postgres:9.6
   ```

   

   ```powershell
   > docker run -d --name kong-database --network kong-net -p 5432:5432 -e "POSTGRES_USER=kong" -e "POSTGRES_DB=kong" -e "POSTGRES_PASSWORD=kong" postgres:9.6
   ```

   

3. prepare your database

   ```sh
   $ docker run --rm \
     --network=kong-net \
     -e "KONG_DATABASE=postgres" \
     -e "KONG_PG_HOST=kong-database" \
     -e "KONG_PG_PASSWORD=kong" \
     -e "KONG_CASSANDRA_CONTACT_POINTS=kong-database" \
     kong:latest kong migrations bootstrap
   ```

   ```powershell
   docker run --rm --network=kong-net -e "KONG_DATABASE=postgres" -e "KONG_PG_HOST=kong-database" -e "KONG_PG_PASSWORD=kong" -e "KONG_CASSANDRA_CONTACT_POINTS=kong-database" kong:latest kong migrations bootstrap
   ```

4.  Start Kong

   ```sh
   $ docker run -d --name kong \
     --network=kong-net \
     -e "KONG_DATABASE=postgres" \
     -e "KONG_PG_HOST=kong-database" \
     -e "KONG_PG_PASSWORD=kong" \
     -e "KONG_CASSANDRA_CONTACT_POINTS=kong-database" \
     -e "KONG_PROXY_ACCESS_LOG=/dev/stdout" \
     -e "KONG_ADMIN_ACCESS_LOG=/dev/stdout" \
     -e "KONG_PROXY_ERROR_LOG=/dev/stderr" \
     -e "KONG_ADMIN_ERROR_LOG=/dev/stderr" \
     -e "KONG_ADMIN_LISTEN=0.0.0.0:8001, 0.0.0.0:8444 ssl" \
     -p 8000:8000 \
     -p 8443:8443 \
     -p 8001:8001 \
     -p 8444:8444 \
     kong:latest
   ```

   ```powershell
   docker run -d --name kong --network=kong-net -e "KONG_DATABASE=postgres" -e "KONG_PG_HOST=kong-database" -e "KONG_PG_PASSWORD=kong" -e "KONG_CASSANDRA_CONTACT_POINTS=kong-database" -e "KONG_PROXY_ACCESS_LOG=/dev/stdout" -e "KONG_ADMIN_ACCESS_LOG=/dev/stdout" -e "KONG_PROXY_ERROR_LOG=/dev/stderr" -e "KONG_ADMIN_ERROR_LOG=/dev/stderr" -e "KONG_ADMIN_LISTEN=0.0.0.0:8001, 0.0.0.0:8444 ssl" -p 8000:8000 -p 8443:8443 -p 8001:8001 -p 8444:8444 kong:latest
   ```

   

Kong Instance 구축 후, 예시로 사용할 API 서버를 아래와 같이 설정

```sh
$ git clone https://github.com/faren/NodeJS-API-KONG.git
$ cd ./NodeJS-API-KONG
$ docker build -t node_kong .
$ docker run -d --name=node_kong --network=kong-net node_kong
```

node_kong IP 주소를 아래와 같이 확인

```sh
$ docker network inspect node_kong
{
	"Name": "node_kong"
	"IPv4Address": "{IPv4Address}"
}
```

kong sh 접속 후, curl 명령어 테스트

```sh
$ docker exec -it kong sh
# curl -i {IPv4Address}:10000/api/v1/customers
```



### Setup Kong as API-gateway to API server routing

* http://localhost:8000/api/v1/customers -> http://{Ipv4Address}:10000/api/v1/customers



* `Add Service` POST : localhost:8001/services  

  ```sh
  $ curl -i -X POST \
    --url http://localhost:8001/services/ \
    --data 'name=api-v1-customers' \
    --data 'url=http://{IPv4Address}:10000/api/v1/customers'
  ```

* GET : localhost:8001/services

  ```sh
  $ curl -v http://localhost:8001/services
  ```

  

* `Add Route`POST : localhost:8001/services

  ```sh
  $ curl -i -X POST \
    --url http://localhost:8001/services/api-v1-customers/routes/ \
    --data 'hosts=["api.ct.id"]' \
    --data 'paths=["/api/v1/customers"]'
  ```

* `Test Setup`

  ```sh
  $ curl -H "Host: api.ct.id" http://localhost:8000/api/v1/customers
  ```

  