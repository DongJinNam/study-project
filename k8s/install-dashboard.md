# Kubernetes Dashboard 설치

**기본환경**

* docker
* docker-compose
* kubernetes



**참고**

* [k8s github dashboard](https://github.com/kubernetes/dashboard)
* [k8s-create-sample-user](https://github.com/kubernetes/dashboard/blob/master/docs/user/access-control/creating-sample-user.md)



**Dashboard 설치**는 아래와 같습니다.

```sh
> kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0-rc5/aio/deploy/recommended.yaml
```

**Dashboard 기동**

```sh
> kubectl proxy
```



Dashboard 접근을 위한 **Admin User, Cluster Role Binding 생성**

* dashboard-adminuser.yaml

* ```yaml
  apiVersion: v1
  kind: ServiceAccount
  metadata:
    name: admin-user
    namespace: kubernetes-dashboard
  ```

* dashboard-clusterrolebinding.yaml

* ```yaml
  apiVersion: rbac.authorization.k8s.io/v1
  kind: ClusterRoleBinding
  metadata:
    name: admin-user
  roleRef:
    apiGroup: rbac.authorization.k8s.io
    kind: ClusterRole
    name: cluster-admin
  subjects:
  - kind: ServiceAccount
    name: admin-user
    namespace: kubernetes-dashboard
  ```

* 위 파일 생성 후, kubectl 적용

* ```sh
  > kubectl create -f ./dashboard-adminuser.yaml
  > kubectl create -f ./dashboard-clusterrolebinding.yaml
  ```



Dashboard 접속을 위한 **토큰 생성**(Windows 10 Powershell)

```sh
> kubectl -n kubernetes-dashboard describe secret $(kubectl -n kubernetes-dashboard get secret | sls admin-user | ForEach-Object { $_ -Split '\s+' } | Select -First 1)
```

-> Token 복사 후 http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/#/login 에서 Token 선택 후, 붙여넣기 후 `Sign-in In Click`



로그인 성공 시. 기본적으로 아래와 같이 뜨게 된다.

![k8s-dashboard-overview](./study-project/k8s/k8s-dashboard-overview.PNG)
