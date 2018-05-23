# 실습시간
## Prerequisite
1. Ubuntu 설치
2. NAT 네트워크를 VBOX에서 생성
3. 각 box 이미지의 네트워크 어댑터에서 NAT 네트워크를 선택하고 생성한 NAT Network 이름을 선택
4. apt-get install docker.io 


## Overlay
> For communication among containers running on different Docker daemon hosts, you can either manage routing at the OS level, or you can use an overlay network.

> an overlay network called `ingress`, which handles control and data traffic related to swarm services. When you create a swarm service and do not connect it to a user-defined overlay network, it connects to the ingress network by default.
> a bridge network called `docker_gwbridge`, which connects the individual Docker daemon to the other daemons participating in the swarm.

* Swarm 에 연결하기 위해서는 무조건  ingress 가 만들어짐 (사용하지 않더라도)

* 왜?
	* 서버가 물리적으로 2대 있다고 가정.
	* 서비스 discovery 같은 것이 돌고 있다고 가정.
	* 서버 A 에 서비스 a 서버 B에 서비스  b 를 docker 로 띄운다고 하면, 
		* bridged mode일 경우 container의 host ip를 알 수 있는 방법이 있지 않는 한 a에서 b로 호출하기 위한 주소를 resolving 할 수 없음.
		* host mode일 경우라면 해결 가능 
		* 하지만  각 A서버에 c서비스를 추가하고 B서버에 d서비스를 추가한다면?
			* 같은 포트를 사용하는 경우 host mode사용 불가
			* 전체 네트워크를 묶은 overlay를 만들어서 서로 찾을 수 있게 한다.
### User-Defined Overlay

* 생성
```
docker network create -d(or--device) overlay test-net
```
* 생성된 docker 에 서 —network 로 `my-overlqy`와 같이 생성된 user-defined overlay 이름을 입력하고 stand alone docker 늘 돌리면 해당 docker 끼리 연결 됨.
```
docker run -it --name alpine1 --network test-net alpine
```

```
docker run -it --name alpine2 --network test-net alpine
```


