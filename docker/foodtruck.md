# FoodTruck

* [Tutorial Link](https://docker-curriculum.com/#multi-container-environments)

* 단일 컨테이너가 아닌 Multi Container 기동 시 활용할 만한 예제입니다.

* Flask 기반의 웹 어플리케이션 서버와 Elasticsearch 6.3.0 버전이 사용됩니다.

* Source Pull

  ```sh
  $ git clone https://github.com/prakhar1989/FoodTrucks
  $ cd FoodTrucks
  ```

* Elasticsearch

* ```sh
  $ docker pull docker.elastic.co/elasticsearch/elasticsearch:6.3.0
  $ docker run -d --name es -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" docker.elastic.co/elasticsearch/elasticsearch:6.3.0
  ```

  

* 로그 확인

  ```sh
  $ docker container logs es
  ```



* Dockerfile 수정

* ```dockerfile
  # start from base
  FROM ubuntu:latest
  
  LABEL maintainer="DongJin Nam <ehdwls6856@gmail.com>"
  
  # install system-wide deps for python and node
  RUN apt-get update
  RUN apt-get -y install python-pip python-dev curl gnupg
  RUN curl -sL https://deb.nodesource.com/setup_10.x | bash
  RUN apt-get -y install nodejs
  
  # copy our application code
  ADD flask-app /opt/flask-app
  WORKDIR /opt/flask-app
  
  # fetch app specific deps
  RUN npm install
  RUN npm run build
  RUN pip install -r requirements.txt
  
  # expose port
  EXPOSE 5000
  
  # start app
  CMD [ "python", "./app.py" ]
  ```

* Docker 이미지 생성 및 Container 기동

* ```sh
  $ cd FoodTrucks
  $ docker build -t ehdwls6856/foodtrucks-web .
  $ docker run -P --rm ehdwls6856/foodtrucks-web
  ```

* 다만 위와 같이 할 경우, 아래와 같은 에러 메시지 발생

  * `Unable to connect to ES. Retrying in 5 secs...`

* Docker network 설정이 잘못되어 있어서 발생(localhost 가 아닌 es가 구축된 IP를 입력해야 함)

  * Docker Network 내 `Bridge` 개념 이해!!
  * `Bridge` : 컨테이너 간 연결되는 네트워크 망(default : bridge)
  * 같은 호스트 간에서 여러 컨테이너 구동 시, 기본값으로는 Bridge 망 내에 컨테이너 간 통신이 가능

* Docker bridge network 확인

  * ```sh
    $ docker network inspect bridge
    ```

* 컨테이너 구동 후, elasticsearch container 와 통신 여부 확인

  * es_ip 는 위 명령어를 통해 얻은 json 파일 내 "Containers" 내 `IPv4Address` 확인

  ```sh
  $ docker run -it --rm ehdwls6856/foodtrucks-web bash
  $ curl {es_ip}:9200
  ```

* 다만, 이런 방법은 기본적으로 모든 컨테이너 간 브리지가 공유되기 때문에, `Not Secure`

* `docker network` command 사용을 통해 내부망 구축 가능

* ```sh
  $ docker network create foodtrucks-net
  $ docker network ls
  ```

* 기존 컨테이너 제거 후, 새로운 net(`foodtrucks-net`)을 통한 기동

* ```sh
  $ docker container stop es
  $ docker container rm es
  $ docker run -d --name es --net foodtrucks-net -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" docker.elastic.co/elasticsearch/elasticsearch:6.3.0
  ```

* Flask App 기동 후, elasticsearch node 와 통신 확인

* ```sh
  $ docker run -it --rm --net foodtrucks-net ehdwls6856/foodtrucks-web bash
  $ curl es:9200
  ```

* 다만, 백그라운드 앱으로 기동할 경우 명령어 수행은 아래와 같다.

* ```sh
  $ docker run -d --net foodtrucks-net -p 5000:5000 --name foodtrucks-web ehdwls6856/foodtrucks-web
  ```

* `Summary`(setup-docker.sh)

* ```sh
  $ docker build -t ehdwls6856/foodtrucks-web .
  $ docker network create foodtrucks-net
  $ docker run -d --name es --net foodtrucks-net -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" docker.elastic.co/elasticsearch/elasticsearch:6.3.0
  $ docker run -d --net foodtrucks-net -p 5000:5000 --name foodtrucks-web ehdwls6856/foodtrucks-web
  ```

* `ToDo` : Docker-Compose, Docker-Swarm, Kubernetes

  
