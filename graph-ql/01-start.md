# GraphQL

### 배경

* REST 가 서버로부터 데이터를 가져오는 가장 인기있는 API 표준
* 그러나 아래와 같은 문제점이 존재
  * 모바일 효율적 데이터 로딩
    * REST와 비교하여 GraphQL은 네트워크 상에서 데이터 전달량을 최소화
    * 모든 Requirement들을 담은 Single Point 제공의 필요성
    * CI/CD 가 여러 회사들의 표준이 되어감에 따라, 클라이언트 요구사항을 빠르게 반영하기 위한 수단이 필요
    
### 개념

* Facebook이 만든 오픈소스 API 표준
* declartive data fetching
* single end points, queries
  
### Example(REST vs GraphQL)

* REST
  * /users/<id>
  * /users/<post>
  * /users/<followers>
  * 3개의 요청에 대한 API를 각각 개발해야 함.
* GraphQL
  * 3개의 요청을 한번의 API로 제공 가능
  * 아래의 예시를 보면, REST 방식이 요구사항을 모두 담을 수 있는 single point 역할을 하기 어렵다.
  ```json
  query {
    User(id : "dongjinnam") {
      name
      Posts {
        title
      }
      followers(last: 3) {
        name
      }
    }
  }
  ```
  * 지속적으로 빠르게 전환하는 frontend 개발 환경과 통계에 대한 인사이트가 필요한 backend 개발환경에서 효율적임.
  * schema가 정의되면, frontend와 backend는 각기 독립된 환경에서 개발 가능.
