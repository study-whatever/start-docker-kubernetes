**[목차]**
- [01 도커 이미지와 컨테이너](#01-------------)
  * [도커 이미지](#------)
  * [도커 컨테이너](#-------)
- [02 도커 컨테이너 다루기](#02------------)
  * [컨테이너 생성](#-------)
  * [컨테이너 목록 확인](#----------)
  * [컨테이너 삭제](#-------)
  * [컨테이너를 외부에 노출](#------------)
  * [컨테이너 애플리케이션 구축](#--------------)
  * [도커 볼륨](#-----)
  * [도커 네트워크](#-------)
  * [컨테이너 로깅](#-------)
  * [컨테이너 자원 할당 제한](#-------------)
- [03 도커 이미지](#03-------)

---

## 01 도커 이미지와 컨테이너

도커 엔진이 사용하는 기본 단위: **이미지, 컨테이너**

### 도커 이미지

- 컨테이너를 생성할 때 필요한 요소
- 가상 머신 생성할 때 사용하는 **iso 파일**과 비슷한 개념
- 여러 개의 계층으로 된 바이너리 파일로 존재하고, 컨테이너를 생성하고 실행할 때 읽기 전용으로 사용됨
- 도커 명령어로 내려받을 수 있어 별도 설치할 필요는 없음

![Image](https://github.com/user-attachments/assets/290c0e68-c56e-4892-8d94-00587be5c1ef)

- 이미지의 이름은 기본적으로 `[저장소이름]/[이미지이름]:[태그]` 형태로 구성

> **저장소(Repository)**: 이미지가 저장된 장소. 명시되지 않은 이미지는 도커에서 기본적으로 제공하는 이미지 저장소인 도커 허브의 공식 이미지를 뜻함. 생략하는 경우 있음
> 
> 
> **이미지 이름**: 해당 이미지가 하는 역할. 예시에서는 우분투 컨테이너 생성을 위한 이미지라는 것을 알 수 있음. 필수 값
> 
>  **태그**: 이미지 버전 관리, 리비전 관리에 사용. 일반적으로는 14.04 같이 버전을 명시하지만 태그 생략하면 도커 엔진은 latest로 인식함 
> 

### 도커 컨테이너

도커 이미지는 리눅스 운영체제, 데이터베이스 등의 각종 애플리케이션, 빅데이터 분석 도구까지 갖가지 종류가 있음 

이러한 이미지로 컨테이너를 생성하면 해당 이미지 목적에 맞는 파일이 들어 있는 파일시스템과 격리된 시스템 자원 및 네트워크를 사용할 수 있는 독립된 공간이 생성됨 ⇒ **도커 컨테이너**

![Image](https://github.com/user-attachments/assets/8010c877-e8c2-42f5-ac4d-ae99e2bcb293)

컨테이너는 이미지를 읽기 전용으로 사용하되 이미지에서 변경된 사항만 컨테이너 계층에 저장하므로 컨테이너에서 무엇을 하든지 원래 이미지는 영향을 받지 않음 

생성된 각 컨테이너는 독립된 파일 시스템을 제공받고 호스트와 분리되어 있어서 특정 컨테이너에서 어떤 애플리케이션을 설치하거나 삭제해도 다른 컨테이너와 호스트는 변화 없음 

## 02 도커 컨테이너 다루기

도커 CLI 를 기준으로 설명

### 컨테이너 생성

- 도커 버전 확인 `docker -v`
- 컨테이너 생성 `run`
    
    `docker run -i -t ubuntu:14.04` 
    
    - ubuntu:14.04: 컨테이너 생성하기 위한 이미지 이름
    - -i: 컨테이너와 상호(interactive) 입출력 가능하게 해줌
    - -t: tty 활성화 → 배시 셸 사용하도록 설정
    
    ![Image](https://github.com/user-attachments/assets/7e309321-8e4c-4db5-9ba9-d3caaeb4ce4f)
    
    - 도커 허브에서 자동으로 이미지를 내려받음
    - 실행하면 도커 컨테이너 내부에 들어오게 됨 → 사용자, 호스트 이름 변경됨
    - 기본 사용자는 root, 호스트 이름은 무작위 16진수 해시값(컨테이너 고유 ID 앞 일부분)

![Image](https://github.com/user-attachments/assets/0ac7faae-1f13-4d58-93ab-a5571955159a)

- 아무것도 설치되지 않음
- 컨테이너 내부 빠져나오는 방법
    - `exit` , Crtl+D → 컨테이너를 정지시킴
    - Ctrl+P, Q → 정지하지 않고 빠져나옴

- `docker pull centos:7`
    - CentOS 이미지를 사용하기 위해서 도커 공식 이미지 저장소에서 내려받음
- `docker images`
    
    ![Image](https://github.com/user-attachments/assets/e5cc41e7-932e-4603-8e9c-9e3155f84444)
    
    - 도커 엔진에 존재하는 이미지 목록 출력함
    - 이전에 내려받은 ubuntu와 방금전 받은 centos 확인
- 컨테이너 생성 시 `create` 명령어 사용 가능
    - `docker create -i -t --name mycentos centos:7`
        
        ![Image](https://github.com/user-attachments/assets/a9b766cc-4917-40f1-b434-c11610b3b6ba)
        
        —name: 컨테이너 이름 설정 가능 
        
    - run 과는 달리 컨테이너 내부로 들어가지지 않음
- `docker start` , `docker attach` 컨테이너 시작하고 내부로 들어감

![Image](https://github.com/user-attachments/assets/c17e6242-0e28-40b6-82c0-ca6da945a805)

보통은 컨테이너 생성과 동시에 시작하기 때문에 run을 더 많이 사용함 

### 컨테이너 목록 확인

`docker ps` 

![Image](https://github.com/user-attachments/assets/10287a01-4c64-4dee-8bda-500b26577ba5)

- 정지되지 않은 컨테이너만 출력
- 옵션 -a: 정지된 컨테이너 포함한 모든 컨테이너 출력함
- CONTAINER ID: 컨테이너에게 자동으로 할당되는 고유 ID, 일부분만 출력
- IMAGE: 컨테이너 생성할 때 사용된 이미지 이름
- COMMAND: 컨테이너 시작될 때 실행될 명령어. 대부분의 이미지에 미리 내장돼 있어 별도 설정 필요 없음
- CREATED: 컨테이너 생성되고 난 후 흐른 시간
- STATUS: 컨테이너 상태, Up은 실행중, Exited는 종료, Pause 는 일시 중지
- PORTS: 컨테이너가 개방한 포트와 호스트에 연결한 포트를 나열함, 위에서는 설정 안했기 때문에 출력 없음
- NAMES: 컨테이너 고유 이름, 이름 설정하지 않았다면 임의 형용사와 명사를 무작위로 조합해서 이름 설정함
    - `docker rename` 으로 이름 변경 가능

### 컨테이너 삭제

`docker rm [name]`

- 한 번 삭제된 경우 복구 불가능
- 실행 중인 컨테이너는 삭제 불가능 → 정지 또는 강제 삭제 옵션 추가해야 함
    
    ```bash
    # 중지 후 삭제 
    docker stop mycentos
    docker rm mycentos
    
    # 강제 삭제
    docker rm -f mycentos
    ```
    

`docker container prune` 

- 모든 컨테이너 삭제

`docker ps -a -q` 를 사용해서 삭제

```bash
docker stop $(docker ps -a -q)
docker rm $(docker ps -a -q)
```

- -a: 컨테이너 상태와 관계 없는 모든 컨테이너
- -q: 컨테이너 ID만 출력
- 컨테이너 리스트를 변수로 해서 컨테이너 삭제

### 컨테이너를 외부에 노출

컨테이너는 가상 머신과 마찬가지로 가상 IP 주소를 할당받음 

기본적으로 도커는 컨테이너에 172.17.0.x 의 IP를 순차적으로 할당함

`ifconfig` : 컨테이너 네트워크 인터페이스 확인 

![Image](https://github.com/user-attachments/assets/dad33194-1eae-4be4-b20c-e2000c1822da)

- NAT IP인 172.17.0.2 를 할당받은 eth0 인터페이스와 로컬 호스트인 lo 인터페이스가 있음
- 아무런 설정 하지 않았다면 컨테이너는 외부에서 접근할 수 없고 도커가 설치된 호스트에서만 접근 가능
- 외부에 노출하기 위해서는 eth0 의 IP와 포트를 호스트의 IP와 포트에 바인딩해야 함

`docker run -i -t --name mywebserver **-p 80:80** ubuntu:14.04` 

- -p 옵션: 컨테이너 포트를 호스트의 포트와 바인딩해 연결할 수 있게 설정함
    - [호스트 포트]:[컨테이너 포트]
    - 여러 개의 포트를 외부에 개방하려면 -p 옵션을 여러 번 써서 설정

```bash
# 아파치 웹 서버 설치
apt-get update
apt-get install apache2 -y
service apache2 start
```

설치 하고 실행하고 나면, [도커 엔진 호스트의 IP]:80 으로 접근 가능!

![Image](https://github.com/user-attachments/assets/14cd6a6b-780a-499e-ad1e-c0a421333c9e)

호스트 IP의 80번 포트로 접근 → 80번 포트는 컨테이너의 80번 포트로 포워딩 → 웹 서버 접근 

호스트의 IP와 포트를 컨테이너의 IP와 포트로 연결한다는 개념은 매우 중요함

- 아파치 웹 서버는 172 대역을 가진 컨테이너의 NAT IP와 80번 포트로 서비스하므로 여기에 접근하려면 172.17.0.x:80 의 주소로 접근해야 함
- 그러나 도커의 포트 포워딩(-p)를 써서 호스트와 컨테이너를 연결했으므로 호스트의 IP와 포트를 통해 172.17.0.x:80으로 접근할 수 있음

![Image](https://github.com/user-attachments/assets/b4cc8fed-4710-40f5-9ce8-a89625f6a4a6)

-p 옵션 값으로 80:81 같이 입력했다면 외부에서 웹 서버에 접근 못함

호스트의 80번 포트와 연결된 컨테이너 포트는 81이 될 것이고 81번 포트는 어떤 서비스도 제공하도록 설정돼 있지 않기 때문 

### 컨테이너 애플리케이션 구축

- 대부분의 서비스는 단일 프로그램으로 동작하지 않음
- 여러 에이전트나 데이터베이스 등과 연결되어 완전한 서비스로써 동작함
- 이런 서비스를 컨테이너화(Containerize)할 때 **여러 개의 애플리케이션을 한 컨테이너에 설치**할 수도 있음
- 그러나 컨테이너에 애플리케이션을 하나만 동작시키면 컨테이너 간 독립성 보장, 애플리케이션 버전 관리, 소스코드 모듈화 등이 쉬워짐

![Image](https://github.com/user-attachments/assets/842dfada-1ab4-4437-b1a7-03b3f3e25429)

**→ 하나의 컨테이너에 프로세스 하나만 실행하는 것이 권장**

```bash
# mariadb 이미지를 사용해 데이터베이스 컨테이너 생성
docker run -d \
--name wordpressdb \
-e MYSQL_ROOT_PASSWORD=password \
-e MYSQL_DATABASE=wordpress mariadb:10.5 # mac에서 mysql 안되서 mariadb로 
```

```bash
# 미리 준비된 워드프레스 이미지를 이용해 워드프레스 웹 서버 컨테이너 생성 
docker run -d \
-e WORDPRESS_DB_HOST=mysql \
-e WORDPRESS_DB_USER=root \
-e WORDPRESS_DB_PASSWORD=password \
--name wordpress \
--link wordpressdb:mysql \
-p 80 \
wordpress
```

![Image](https://github.com/user-attachments/assets/0a4bcb76-2afb-4498-b368-58a81b5ccbc6)

워드프레스 웹 서버 컨테이너의 -p 옵션에서 80 을 입력하여 호스트 포트 중 하나와 컨테이너의 80번 포트가 연결됨 

> `0.0.0.0:63593 → 80/tcp` 에서 0.0.0.0은 호스트의 활용 가능한 모든 네트워크 인터페이스에 바인딩함
> 

![Image](https://github.com/user-attachments/assets/bacd6ec7-8c3e-4d1d-b9b4-f1dbfe6784eb)

`run` 옵션

- -d: Detached 모드로 컨테이너 실행. 컨테이너를 **백그라운드에서 동작하는 애플리케이션**으로써 실행하도록 설정
    - 입출력이 없는 상태로 컨테이너를 실행하게 됨. 컨테이너 내부에서 프로그램이 터미널을 차지하는 포그라운드로 실행돼 사용자의 입력을 받지 않음
    - Detached 모드인 컨테이너는 반드시 컨테이너에서 프로그램이 실행돼야 하며, 포그라운드 프로그램이 실행되지 않으면 컨테이너는 종료됨
        
        ![Image](https://github.com/user-attachments/assets/9f58efdf-bfc3-43b8-bd83-61b9bc4f884e)
        
    
    ```bash
    # 컨테이너 생성돼도 바로 종료되므로 docker ps 로 확인 X
    docker run -d --name detach_test ubuntu:14.04
    ```
    
    - docker start 로 시작해도 컨테이너 내부 터미널을 차지하는 포그라운드로써 동작하는 프로그램이 없어 컨테이너는 시작되지 않음
    
    ```bash
    # mariadb 컨테이너를 -i -t 옵션으로 생성 
    docker run -i -t \
    --name mysql_attach_test \
    -e MYSQL_ROOT_PASSWORD=password \
    -e MYSQL_DATABASE=wordpress \
    mariadb:10.5
    ```
    
    - 프로그램이 포그라운드로 실행되는 거 확인 가능
    - 이 상태에서는 상호 입출력이 불가능하고 단순히 프로그램이 포그라운드 모드로 동작하는 것만 지켜볼 수 있음
    - 따라서 -d 옵션을 설정해서 컨테이너가 백그라운드에서 동작하게 하는 것!
- -e: 컨테이너 내부 환경변수를 설정
    - `MYSQL_ROOT_PASSWORD=password` 해당 환경변수 값을 password로 설정한다는 의미
    - echo 로 확인 가능
    - 그러나 echo 명령어를 사용하려면 상호 입출력이 가능한 배시 셸을 사용할 수 있는 환경이 필요함 → docker attach 명령어로 컨테이너 내부로 들어가야 하지만 위에서 생성한 mariadb 컨테이너는 -d 옵션으로 생성했으므로 attach 명령어가 의미 없음.. 컨테이너 실행 중인 프로그램의 로그 출력을 볼 뿐
    - **exec** 명령어를 이용하면 컨테이너 내부 셸을 사용할 수 있음 !
        - exec 로 들어왔을 때는 exit 을 써도 종료되지 않음 .. mysqld 프로세스가 컨테이너 안에서 여전히 포그라운드 모드로 동작하고 있기 때문
- --link: A 컨테이너에서 B 컨테이너(172.17.0.3)로 접근하는 방법 중 가장 간단한 것은 NAT로 할당받은 내부 IP를 쓰는 것!
    - A컨테이너는 B 컨테이너의 IP를 써서 접근 가능!
    - 도커 엔진은 컨테이너에게 내부 IP 를 172.17.0.2, 3, 4 .. 와 같이 순차적으로 할당함 → 시작할 때마다 재할당하는 것이므로 매번 변경되는 컨테이너 IP로 접근하기 어려움
    - —link 옵션은 내부 IP를 알 필요 없이 항상 컨테이너에 별명으로 접근하도록 설정함!
    - —link 에 입력된 컨테이너가 실행 중이지 않거나 존재하지 않으면 —link 적용한 컨테이너도 실행할 수 없음 → 확인 위해서 `docker stop wordpress wordpressdb` 실행
    - —link 옵션은 현재 deprecated 된 옵션으로 추후 삭제될 수 있음
    - 도커 브리지 네트워크를 사용하면 동일한 기능을 더욱 손쉽게 사용할 수 있어서 브리지 네트워크를 권장함!

### 도커 볼륨

도커 이미지로 컨테이너를 생성하면 이미지는 읽기 전용이 되어 컨테이너 변경 사항만 별도로 저장해 각 컨테이너의 정보를 보존함 

![Image](https://github.com/user-attachments/assets/3db16273-6e25-438f-b982-bdcb068a9c82)

이미 생성된 이미지는 절대 변경되지 않음 

컨테이너 계층에 원래 이미지에서 변경된 파일시스템 등을 저장함 

→ 이미지는 mysql 실행하는 데 필요한 애플리케이션 파일이 들어있고, 컨테이너 계층은 데이터베이스를 운용하면서 쌓이는 데이터가 저장됨

문제는 mysql 컨테이너를 삭제하면 컨테이너 계층에 저장돼 있던 데이터베이스의 정보도 삭제됨 

→ 컨테이너 데이터를 영속적(Persistent) 데이터로 활용할 수 있는 방법이 있음

- 그 중 가장 쉬운 방법은 **볼륨 활용**

**볼륨 활용하는 방법**

- 호스트와 볼륨 공유
- 볼륨 컨테이너 활용
- 도커가 관리하는 볼륨 생성

1. **호스트 볼륨 공유**

```bash
# mysql 데이터베이스 컨테이너 생성
docker run -d \
--name **wordpressdb_hostvolume** \
-e MYSQL_ROOT_PASSWORD=password \
-e MYSQL_DATABASE=wordpress \
-v /home/wordpress_db:/var/lib/mysql \
mysql:5.7

# 워드프레스 웹 서버 컨테이너 생성
docker run -d \
-e WORDPRESS_DB_PASSWORD=password \
--name **wordpress_hostvolume** \
--link wordpressdb_hostvolume:mysql \
-p 80 \
wordpress
```

- -p 옵션으로 컨테이너 80 포트를 외부에 노출함 → wordpress_hostvolume 컨테이너의 호스트 포트로 워드프레스 컨테이너에 접속 가능
- -v 옵션으로 `/home/wordpress_db:/var/lib/mysql` 설정함
    - 호스트의 /home/wordpress_db 디렉터리와 컨테이너의 /var/lib/mysql 디렉터리를 공유한다는 뜻 → **[호스트 공유 디렉터리]:[컨테이너 공유 디렉터리] 형태**
    
    <aside>
    💡
    
    컨테이너의 /var/lib/mysql 디렉터리: MYSQL 이 데이터베이스 데이터를 저장하는 기본 디렉터리
    
    </aside>
    
    - 실제로 /home/wordpress_db 디렉터리를 확인해 보면 mysql 구동하는데 필요한 각종 파일이 공유됨
        - mysql, performance_schema, sys, wordpress

```bash
# 컨테이너 삭제해서 데이터베이스 데이터 보존되는지 확인 
docker stop wordpress_hostvolume worpressdfb_hostvolume
docker rm wordpress_hostvolume worpressdfb_hostvolume

ls /home/wordpress_db -> 데이터 그대로 남아있음 
```

![Image](https://github.com/user-attachments/assets/66831d68-4a66-483a-94b0-d02c6a911df9)

/var/lib/mysql 디렉터리는 호스트의 /home/wordpress_db 디렉터리와 동기화가 아니라 완전히 같은 디렉터리 

- 원래 호스트에는 /home/wordpress_db 디렉터리가 존재하지 않았는데, -v 옵션때문에 디렉터리 생성되었고 파일이 공유됨! 컨테이너 파일이 호스트로 복사된 것

![Image](https://github.com/user-attachments/assets/fd0d9316-8c90-4a65-ac3d-20d9ff047b55)

<aside>
💡

디렉터리 단위뿐 아니라 단일 파일 단위의 공유도 가능함

동시에 여러 개의 -v 옵션 사용 가능

```bash
docker run -i -t \
--name file_volume \
-v /home/hello:hello \
-v /home/hello2:hello2 \
ubuntu:14.04
```

</aside>

- 호스트에 이미 디렉터리와 파일 존재하고 컨테이너에도 존재할 때 공유하면?
    
    ```bash
    # 이미 준비된 이미지를 사용 -> /home 디렉터리에 testdir_2 디렉터리 존재하고 test 라는 파일 존재
    docker run -i -t --name volume_dummy alicek106/volume_test
    
    # 다시 -v 옵션으로 컨테이너 생성
    # /home/testdir_2 확인하면 원래 존재했던 test 가 없어지고 호스트에서 공유된 파일이 존재함
    docker run -i -t \
    --name volume_overide \
    -v /home/wordpress_db:/home/testdir_2 \
    alicek106/volume_test
    ```
    
    - 이미지에 원래 존재하던 디렉터리에 호스트 볼륨을 공유하면 커넽이너의 디렉터리 자체가 덮어씌워짐
    - -v 옵션을 통한 호스트 볼륨 공유는 호스트의 디렉터리를 컨테이너의 디렉터리에 마운트함

1. **볼륨 컨테이너**
- -v 옵션으로 볼륨을 사용하는 컨테이너를 다른 컨테이너와 공유하는 것
- 컨테이너 생성할 때 `--volumes-from` 옵션을 설정하면 -v 옵션 적용한 컨테이너의 볼륨 디렉터리를 공유할 수 있음
- 직접 볼륨을 공유한 것이 아닌 -v 옵션을 적용해서 컨테이너를 통해 공유하는 것

```bash
# volume_override 컨테이너에서 볼륨 공유받음 
# volume_overide 컨테이너는 /home/testdir_2 디렉터리를 호스트와 공유 중
# 이 컨테이너를 볼륨 컨테이너로서 volumes_from_container 컨테이너에 다시 공유하는 것
docker run -i -t \
**--name volues_from_container \
--volumes-from** **volume_overide** \
ubuntu:14.04
```

![Image](https://github.com/user-attachments/assets/ef33267a-b8ec-44e0-a571-1ad3218b4387)

- 여러 개의 컨테이너가 동일한 컨테이너의 볼륨을 공유해서 사용할 수도 있음
- 이러한 구조로 호스트에서 볼륨만 공유하고 별도의 역할을 담당하지 않는 일명 **‘볼륨 컨테이너’**로서 활용하는 것이 가능함

1. **도커 볼륨**
- `docker volume` 명령어 사용
- 도커 자체에서 제공하는 볼륨 기능을 활용해 데이터 보존할 수 있음
- `docker volume create` 으로 볼륨 생성

```bash
# 볼륨 생성 
docker volume create --name myvolume 

# 생성된 볼륨 확인
docker volume ls 
```

- 생성할 때 플러그인 드라이버를 설정해 여러 종류의 스토리지 백엔드를 쓸 수 있음! 여기서는 기본 제공하는 드라이버인 **local** 사용
    - 이 볼륨은 로컬 호스트에 저장되고 도커 엔진에 의해 생성되고 삭제됨

```bash
# myvolume 이라는 볼륨 사용하는 컨테이너 생성 
docker run -i -t --name myvolume_1 \
**-v myvolume:/root/ \**
ubuntu:14.04
```

- [볼륨 이름]:[컨테이너 공유 디렉터리] 형식으로 작성
    - /root/ 디렉터리에 마운드해서 /root 디렉터리에 파일을 쓰면 해당 파일이 볼륨에 저장됨

![Image](https://github.com/user-attachments/assets/91500d24-36ca-40ad-b51a-a220a9e20656)

- 볼륨은 디렉터리 하나에 상응하는 단위로서 도커 엔진에서 관리함
- 도커 볼륨도 호스트 볼륨 공유와 마찬가지로 호스트에 저장하는데, 실제로 어디에 저장되는지 사용자는 알 필요 없음
    - `docker inspect` 로 실제로 어디에 저장되는지 확인 가능
- docker volume create 명령을 별도로 입력하지 않아도 -v 입력할 때 수행하도록 설정할 수 있음 → 공유할 디렉터리 위치를 입력하면 볼륨을 자동으로 생성함

```bash
docker run -i -t --name volume_auto \
-v **/root \**
ubuntu:14.04
```

도커 볼륨을 생성하고 삭제하다 보면 불필요한 볼륨들이 남아있을 때가 있음 

- 볼륨은 자동으로 삭제 X

사용되지 않는 볼륨을 한꺼번에 삭제하려면 `docker volume prune` 명령어 사용

컨테이너가 아닌 외부에 데이터 저장하고, 컨테이너는 그 데이터로 동작하도록 설계하는 것 = stateless

- 컨테이너 자체는 상태가 없고 데이터는 외부로부터 제공받음
- stateless한 컨테이너 설계는 도커 사용할 때 매우 바람직함

<aside>
💡

-v 옵션 대신 —mount 옵션 사용할 수 있음

기능은 같지만 볼륨 정보를 나타내는 방법이 다르기 때문에 편한 옵션을 사용하면 됨

```bash
docker run -i -t --name mount_option_1 \
--mount type=volume,source=myvolume,target=/root \
ubuntu:14.04
```

</aside>

### 도커 네트워크

**구조**

ifconfig 명령어를 입력해서 컨테이너 네트워크 인터페이스에 **eth0과 lo** 확인함

![Image](https://github.com/user-attachments/assets/f0431f94-5667-4cb2-b654-470f431267c7)

도커는 컨테이너 내부 IP를 순차적으로 할당하고, IP는 컨테이너 재시작할 때마다 변경될 수 있음

- 내부IP는 도커가 설치된 호스트, 즉 내부 망에서만 쓸 수 있어 외부와 연결이 필요함
- 컨테이너 시작할 때마다 호스트에 **veth(virtual eth)**라는 인터페이스를 생성해서 이뤄짐
- 도커는 각 컨테이너에 외부와의 네트워크를 제공하기 위해 컨테이너마다 가상 네트워크 인터페이스를 호스트에 생성함. 인터페이스 이름이 veth로 시작
- 사용자가 직접 생성할 필요 없고 컨테이너 생성될 때 도커 엔진이 자동으로 생성함

![Image](https://github.com/user-attachments/assets/0e07a5d9-3000-425d-b598-ab6d34ee3962)

- 실행 중인 컨테이너 수만큼 veth 로 시작하는 인터페이스 생성됨

**eth0**

- 공인 IP 또는 내부 IP가 할당되어 실제로 외부와 통신할 수 있는 호스트 네트워크 인터페이스

**vethXXX**

- 컨테이너 시작할 때 생성되고 각 컨테이너의 eth0과 연결됨

**docker0 브리지**

- 각 veth 인터페이스와 바인딩돼 호스트의 eth0 인터페이스와 이어주는 역할을 함

![Image](https://github.com/user-attachments/assets/1bb008c4-bcd2-4efc-ae99-5d80f71752da)

💡컨테이너의 eth0 인터페이스는 호스트의 veth 인터페이스와 연결되고, veth 인터페이스는 docker0 브리지와 바인딩돼 외부와 통신할 수 있음 

- `brctl` 명령어로 docker0 브리지에 veth 바인딩 확인 가능

**도커 네트워크 기능**

사용자 선택에 따라 여러 네트워크 드라이버를 쓸 수 있음 

- 도커 자체 제공: 브리지(bridge), 호스트(host), 논(none), 컨테이너(container), 오버레이(overlay)
- 서드 파티: weave, flannel, openvswitch → 더 확장된 네트워크 구성을 위해 활용됨

`docker network ls` : 네트워크 목록 확인 

![Image](https://github.com/user-attachments/assets/f047a324-afca-43ba-a8c9-7342fb8a373a)

- **브리지**
    - 컨테이너 생성할 때 자동으로 연결되는 docker0 브리지를 활용하도록 설정돼 있음
    - 172.17.0.x IP 대역을 컨테이너에 순차적으로 할당함
    - `docker network inspect` 로 네트워크의 자세한 정보 확인 가능
        
        ![Image](https://github.com/user-attachments/assets/9fb29cc4-5995-4b7c-9317-ea2afec9123c)
        
        - Config 항목의 서브넷과 게이트웨이 IP 확인!
        - 브리지 네트워크를 사용 중인 컨테이너 목록을 Containers 에서 확인 가능
    - 아무 설정 없이 컨테이너 생성하면 자동으로 docker0 브리지를 사용함
- **브리지 네트워크**
    - docker0이 아닌 사용자 정의 브리지를 새로 생성해 각 컨테이너에 연결하는 네트워크 구조
    - 컨테이너는 연결된 브리지를 통해 외부와 통신할 수 있음
    - `docker network create --driver bridge **mybridge**` : 새로운 브리지 타입의 네트워크 생성
    - docker run, create 명령어에 `--net` 값을 설정하면 컨테이너가 이 네트워크를 사용하도록 설정할 수 있음
    
    ![Image](https://github.com/user-attachments/assets/97c97d82-38df-4cf2-9785-8cacd3e42c49)
    
    - 새로운 IP 대역 할당을 확인할 수 있음 ! 172.18 대역의 내부 IP가 할당됨
    - 사용자 정의 네트워크는 `docker network disconnect` , `connect` 를 통해 컨테이너에 유동적으로 붙이고 뗄 수 있음
        - 단, 논 네트워크나 호스트 네트워크 등 특별한 네트워크 모드에서는 connect, disconnect 명령어를 사용할 수 없음!
        - **특정 IP 대역을 갖는 네트워크 모드에만** 명령어 사용 가능
    - 네트워크의 서브넷, 게이트웨이, IP 할당 범위 등을 임의로  설정하려면 네트워크 생성할 때 —subnet, —ip-range, —gateway 옵션 추가
        - 단, —subnet 과 —ip-range는 같은 대역이어야 함
        
        ![Image](https://github.com/user-attachments/assets/f99cf0ce-44ff-4030-b455-f97aa082dbc3)
        
- **호스트 네트워크**
    - 네트워크를 호스트로 설정하면 호스트의 네트워크 환경을 그대로 쓸 수 있음
    - 별도로 생성할 필요 없이 기존 host 라는 이름의 네트워크 사용
        
        ```bash
        docker run -i -t --name network_host \
        **--net host \**
        ubuntu:14.04
        ```
        
    - 컨테이너 내부 네트워크 환경 확인하면 호스트와 같음 ! 호스트 이름도 무작위가 아닌 도커 엔진이 설치된 호스트 머신의 호스트 이름으로 설정됨
        
        ![Image](https://github.com/user-attachments/assets/802409bb-f2ec-4297-98dc-a66c9ca13858)
        
    - 컨테이너 내부 애플리케이션을 별도의 포트 포워딩 없이 바로 서비스할 수 있음
    - 마치 실제 호스트에서 애플리케이션을 외부에 노출하는 것과 같음
- **논 네트워크**
    - 아무런 네트워크를 쓰지 않는 것! 외부와 연결 단절
    
    ```bash
    docker run -i -t --name network_none \
    **--net none \**
    ubuntu:14.04
    ```
    
    - 내부에서 네트워크 인터페이스를 확인하면 로컬호스트를 나타내는 lo 외에는 존재하지 않음
- **컨테이너 네트워크**
    - `--net container:[다른 컨테이너 ID]`  옵션으로 container 를 입력하면 다른 컨테이너의 네트워크 네임스페이스 환경을 공유할 수 있음
    - 공유 속성: 내부IP, 네트워크 인터페이스의 맥(MAC) 주소 등
    - 컨테이너 내부 네트워크 환경을 공유하면 내부 IP 를 새로 할당받지 않고 호스트에 veth 로 시작하는 가상 네트워크 인터페이스도 생성되지 않음
    
    ![Image](https://github.com/user-attachments/assets/060e3cbf-40f9-4dde-858e-64cca49e7d4c)
    
- **브리지 네트워크와 --net-alias**
    - 브리지 타입의 네트워크와 run 명령어의 --net-alias 를 같이 사용하면 특정 호스트 이름으로 컨테이너 여러 개에 접근 가능
    
    ```bash
    # alicek106라는 호스트 이름으로 3개의 컨테이너 생성 
    docker run -i -t -d --name network_alias_container1 \
    --net mybridge \
    **--net-alias alicek106** \
    ubuntu:14.04
    
    docker run -i -t -d --name network_alias_container2 \
    --net mybridge \
    **--net-alias alicek106** \
    ubuntu:14.04
    
    docker run -i -t -d --name network_alias_container3 \
    --net mybridge \
    **--net-alias alicek106** \
    ubuntu:14.04
    
    # 각 컨테이너의 IP 확인 
    docker inspect network_alias_container1 | grep IPAddress # 172.18.0.2
    docker inspect network_alias_container2 | grep IPAddress # 172.18.0.3
    docker inspect network_alias_container3 | grep IPAddress # 172.18.0.4
    
    # 세 개 컨테이너에 접근할 컨테이너 생성 후 alicek106 호스트 이름으로 ping 요청 전송
    docker run -i -t --name network_alias_ping \
    --net mybridge \
    ubuntu:14.04
    
    ping -c 1 **alicek106**
    ```
    
    ![Image](https://github.com/user-attachments/assets/c4ec8ee7-1a7f-42fb-8330-2950b53aff41)
    
    - 컨테이너 3개의 IP로 각 ping 이 전송됨!
    - 매번 달라지는 IP를 결정하는 것은 **라운드로빈** 방식
    - 도커 엔진에 내장된 DNS 가 alicek106이라는 호스트 이름을 —net-alias 옵션으로 alicek106 을 설정한 컨테이너로 변환(resolve)하기 때문
    
    ![Image](https://github.com/user-attachments/assets/70012c27-7b3c-43bb-9fe0-59b677b27b60)
    
    - 도커 DNS는 호스트 이름으로 유동적인 컨테이너를 찾을 때 주로 사용
        - 대표적인 예 `—link` : 컨테이너 IP 변경돼도 별명으로 컨테이너 찾을 수 있게 DNS에 의해 자동으로 관리됨
        - —net-alias 옵션이 있으면 도커는 사용자가 정의한 브리지 네트워크에 사용되는 내장 DNS 서버를 가지고, DNS의 IP는 127.0.0.11 임
        - mybrdige 라는 이름의 네트워크에 속한 3개의 컨테이너는 해당 옵션으로 alicek106을 등록했고, 컨테이너의 Ip는 DNS 서버에 alicek106이라는 호스트 이름으로 등록됨
        - mybridge 네트워크에 속한 컨테이너에서 alicek106 호스트 이름으로 접근하면 DNS 서버는 라운드 로빈 방식을 이용해 컨테이너의 IP 리스트를 반환함
    - `dig` 는 DNS 로 도메인 이름에 대응하는 IP 조회할 때 쓰는 도구
        
        ![Image](https://github.com/user-attachments/assets/61002907-d9e3-4eae-bee2-44b94f9de2b9)
        
- **MacVLAN 네트워크**
    - 호스트의 네트워크 인터페이스 카드를 가상화해 **물리 네트워크 환경을 컨테이너에게 동일하게 제공함**
    - MacVNAN을 사용하면 물리 네트워크상에서 가상의 맥 주소를 가지고 해당 네트워크에 연결된 다른 장치와의 통신이 가능해짐
    - MacVNAN에 연결된 컨테이너는 기본적으로 할당되는 IP 대역 대신 네트워크 장비의 IP를 할당받기 때문
    
    ![Image](https://github.com/user-attachments/assets/f8f65c33-a2a4-4bbd-b5e5-986867d5d57e)
    
    - 공유기, 라우터, 스위치 같은 네트워크 장비에 두 대의 서버가 연결되어 있음
    - 각 서버는 192.168.0.0/24 대역에서 IP를 동적으로 할당받는다고 가정
    - MacVLAN을 사용하면 각 컨테이너에 192.168.0.0/24 대역의 IP를 할당받을 수 있음
    - 따라서 MacVLAN응ㄹ 사용하는 컨테이너와 동일한 IP 대역응ㄹ 사용하는 서버 및 컨테이너는 서로 통신 가능!
    - MacVLAN 네트워크를 사용하는 컨테이너는 기본적으로 호스트와는 통신 불가능함
    - 사용하려면 적어도 1개의 네트워크 장비와 서버가 필요함 → 대부분 환경에서 사용 방법이 거의 동일하기 때문에 고가의 스위치, 서버 대신 공유기와 라즈베리 파이 사용 가능
        - 버추얼박스 가상 머신과 호스트 전용 어댑터로 테스트도 가능
    
    ```bash
    # MacVLAN 네트워크 생성
    docker network create -d macvlan --subnet=192.0.0/24 \
    --ip-range=192.168.0.64/28 --gateway=192.168.0.1 \
    -o macvlan_mode=brdige -o parent=eth0 my_macvlan
    ```
    
    - -d: 네트워크 드라이버로 macvlan 사용한다는 것을 명시
    - —subnet: 컨테이너가 사용할 네트워크 정보를 입력
    - —ip-range: MacVLAN 생성하는 호스트에서 사용할 컨테이너 IP 범위 입력. 겹치지 않게 설정해야 함
    - —gateway: 네트워크에 설정된 게이트웨이 입력
    - -o: 네트워크 추가적인 옵션 설정
    
    ```bash
    # MacVLAN 네트워크를 사용하는 컨테이너 생성 
    docker run -it --name c1 --hostname c1 \
    **--network my_macvlan** ubnutu:14.04
    ```
    

### 컨테이너 로깅

**json-file 로그 사용**

도커는 컨테이너 표준 출력(StdOut)과 에러(StdErr) 로그를 별도의 메타데이터 파일로 저장하고 확인하는 명령어를 제공함 

```bash
# 컨테이너 생성 
docker run -d --name mysql \
-e MYSQL_ROOT_PASSWORD=1234 \
mysql:5.7

# 컨테이너 표준 출력 확인 -> -d 로 백그라운드 모드의 컨테이너인 경우 애플리케이션 상태 확인 
docker logs mysql 
```

```bash
# 다른 방법으로 컨테이너 생성 
# 컨테이너 생성은 됐으나 실행되지 않음 
# docker start 명령어로 다시 시작해도 시작되지 않음 
docker run -d --name no_passwd_mysql mysql:5.7

# 이때 docker logs 로 애플리케이션의 문제 확인 가능 
# 필요한 환경변수 지정하지 않아서 컨테이너 시작되지 않은 것
docker logs no_passwd_mysql
```

백그라운드 모드의 컨테이너인 경우 잘 구동중인지 확인하기 위한 방법으로 로그 사용

컨테이너가 정상적으로 실행 및 동작하지 않고 docker attach 명령어도 사용하지 못하는 개발 환경에서 로그를 통해 간단하고 빠르게 에러 확인 가능 

- `--tail` : 출력할 로그 수 설정 가능
- `--since` : 특정 시간 이후의 로그 확인 가능
- `-t` : 타임스탬프 표시
- `-f` : 실시간으로 출력되는 내용 확인, 애플리케이션 개발할 때 유용함

기본적으로 컨테이너 로그는 JSON 형태로 도커 내부에 저장됨

- `/var/lib/docker/containers/${CONTAINER_ID}/${CONTAINER_ID}-json.log` 에 저장

컨테이너 내부 출력이 너무 많은 상태면 json 파일 크기가 커져 호스트의 남은 저장 공간을 전부 사용할 수도 있음 → `--log-opt` 로 컨테이너 json 로그 파일 최대 크기 지정 가능

- max-size: 로그 파일 최대 크기
- max-file: 로크 파일 개수

도커는 컨테이너 로그를 JSON 파일로 저장하지만 그 외 각종 로깅 드라이버를 사용해서 설정할 수도 있음

- syslog, journald, fluentd, awslogs ..

**syslog 로그**

컨테이너 로그를 syslog로 보내 저장하도록 설정할 수 있음 

유닉스 계열 운영체제에서 로그를 수집하는 오래된 표준

커널, 보안 등 시스템 관련 로그, 애플리케이션 로그 등 다양한 종류의 로그 수집함

유닉스 계열에서는 syslog 사용하는 인터페이스가 동일하기 때문에 체계적으로 로그 수집하고 분석할 수 있음 

```bash
# syslog 에 로그 저장하는 컨테이너 생성 
docker run -d --name syslog_container \
**--log-driver=syslog \**
ubuntu:14.04 \
echo **syslogtest** # 컨테이너 커맨드가 echo syslogteset 로 설정돼서 문구 출력 후 컨테이너 종료
```

 syslog 로깅 드라이버는 로컬호스트의 syslog 에 저장하므로 운영체제 및 배포판에 따라 해당 파일 위치를 알아야 함

- 우분투 14.04: `/var/log/syslog.CentOS`
- RHEL: `/var/log/messages`
- 우분투 16.04, CoreOS: `journalctl -u docker.service` 명령어로 확인

syslog 를 원격 서버에 설치하면 로그 옵션을 추가해 로그 정보를 원격 서버로 보낼 수 있음 

- rsylog: 원격에 저장하는 방법 중 하나

![Image](https://github.com/user-attachments/assets/06b58299-600f-4395-bdf4-deccbcf1738e)

```bash
# 서버 호스트에서 rsyslog 컨테이너 생성 
docker run -i -t \
-h rsyslog \
--name rsyslog_server \
**-p 514:514 -p 514:514/udp \**
ubuntu:14.04

# 컨네이너 내부의 rsyslog.conf 열어 syslog 서버 구동시키는 항목의 주석 해제 후 저장 
$ModLoad imudp
$UDPSererRun 514

$ModLoad imtcp
$InputTCPServerRun 514

# 클라이언트 호스트에서 컨테이너 생성해 서버 rsyslog 컨테이너에 로그 저장 
docker run -i -t \
--log-driver=syslog \
--log-opt syslog-address=tcp://192.168.0.100:514 \
--log-opt tag="mylog" \
ubuntu:14.04
```

- `--log-opt` 로깅 드라이버에 추가할 옵션
    - `syslog-address` 에 rsyslog 컨테이너에 접근할 수 있는 주소 입력
- 서버의 rsyslog 컨테이너로 돌아와 내부 syslog 파일 확인하면 로그가 전송된거 확인 가능

**fluentd 로깅**

각종 로그 수집하고 저장할 수 있는 기능 제공하는 오픈소스 도구

도커에서 fluentd 플러그인 공식적으로 제공함

데이터 포맷으로 JSON 사용

데이터를 AWS S3, HDFS(Dadoop Distributed File System), MongoDB 등 다양한 저장소에 저장 가능 

[예시] fluentd 와 몽고DB 연동해서 데이터 저장 

![Image](https://github.com/user-attachments/assets/227f18eb-178b-4b38-ace4-31da575be9c8)

- 특정 호스트에 생성되는 컨테이너는 하나의 fluentd 에 접근하고, fluentd 는 몽고 DB에 데이터 저장

![Image](https://github.com/user-attachments/assets/3cebd623-a15c-41cc-bf30-0a3d2a936f60)

- 몽고 디비 컨테이너 생성
    - 호스트의 27017 포트 개방돼 있어야 함
    
    ```bash
    docker run --name mongoDB -d -p 27017:27017 mongo
    ```
    
- fluentd 서버 호스트에서는 fluent.conf 파일을 사용
    - 들어오는 로그 데이터를 몽고 DB에 저장하고, access 라는 이름의 컬렉션에 로그를 저장하고, 몽고 DB 컨테이너의 호스트 주소와 포트를 지정한 내용이 있음
- fluentd.conf 파일이 저장된 디렉터리에서 fluentd 컨테이너 생성
    - 파일을 -v 옵션으로 fluentd 컨테이너에 공유하고 설정 파일로 사용함
    
    ```bash
    docker run -d --name fluentd -p 24224:24224 \
    -v $(pwd)/fluent.conf:/fluentd/etc/fluent.conf \
    -e FLUENTD_CONF=fluent.conf \
    alicek106/fluentd:mongh
    ```
    
- 도커 서버에서 로그 수집할 컨테이너 생성
    - `--log-opt` tag 명시해서 몽고 DB에 로그로서 저장됨
    - 호스트의 80번 포트로 nginx 웹 서버에 접근 가능
    
    ```bash
    docker run -p 80:80 -d \
    **--log-driver=fluentd \ 
    --log-opt fluentd-address=192.168.0.101:24224 \
    --log-opt tag=docker.nginx.webserver \** 
    nginx
    ```
    
- nginx 서버 접근 뒤 몽고DB 컨테이너에서 데이터 확인하면 nginx 웹 서버 접근 기록 저장된거 확인 가능

‼️ 기억해야 할 것은 도커 엔진은 fluentd 서버에 컨테이너 로그를 전송했고, 이 로그는 다시 몽고 DB 서버로 전송되어 저장됨

**아마존 클라우드워치 로그**

AWS에서 로그 및 이벤트 수집하고 저장해 시각적으로 보여주는 도구

도커를 EC2 에서 사용하고 있다면 다른 도구를 별도로 설치할 필요 없이 드라이버 옵션을 설정하는 것만으로도 클라우드워치 로깅 드라이버 사용 가능 

단계

- 클라우드워치에 해당하는 IAM 권한 생성
    - CloudWatchFullAccess
- 로그 그룹 생성
- 로그 그룹에 로그 스트림 생성
- 클라우드워치의 IAM 권한 사용할 수 있는 EC2 인스턴스 생성과 로그 전송
    - 인스턴스에서 mycloudwatch라는 클라우드워치를 사용하도록 권한 추가해야 함

### 컨테이너 자원 할당 제한

컨테이너 생성하는 run, create 명령어에서 컨테이너 자원 할당량을 조정하도록 옵션 설정할 수 있음 

아무런 옵션이 없다면 호스트의 자원을 제한 없이 쓸 수 있게 됨 😠 → 호스트 자원을 전부 점유해 다른 컨테이너들뿐 아니라 호스트 자체의 동작이 멈출 수도 있음 

설정된 자원 제한 확인하는 방법 `docker inspect` 

![Image](https://github.com/user-attachments/assets/a8d1926f-93c4-492e-ac6a-b8297dadeafe)

**컨테이너 메모리 제한** `--memory` 

- 입력할 수 있는 단위: m(megabyte), g(gigabyte)
- 제한할 수 있는 최소 메모리는 4MB
- ⚠️ 프로세스가 할당된 메모리를 초과하면 컨테이너가 자동 종료되므로 적절하게 할당하는 것이 좋음
- `—-memory-swap` 기본적으로 컨테이너의 Swap 메모리는 메모리의 2배로 설정되는데, 별도로 지정할 수 있음

**컨테이너 CPU 제한** 

`--cpu-shares` 

- 컨테이너에 가중치를 설정해 해당 컨테이너가 CPU를 상대적으로 얼마나 사용할 수 있는지를 나타냄
- 시스템에 존재하는 CPU를 어느 비중만큼 나눠 쓸 것인지 ⇒ share
- 아무런 설정하지 않았을 때 컨테이너가 가지는 값은 1024로, 1의 비중을 의미함
- [예시] 1번 컨테이너 1024, 2번 컨테이너 512 로 설정하면 2:1 비율로 사용하게 됨

`--cpuset-cpu` 

- 호스트에 CPU가 여러 개 있을 때, 특정 CPU만 사용하도록 설정
- 0번부터 시작
- CPU별 사용량 확인할 수 있는 도구: htop

`--cpu-period` , `--cpu-quota` 

- 컨테이너의 CFS(Completely Fair Scheduler) 주기는 기본적으로 100ms 로 설정되지만 run 명령어의 옵션으로 위를 사용하면 주기를 변경할 수 있음
- period의 값은 기본적으로 100000 = 100ms
- quota 는 period 에 설정된 시간 중 CPU 스케줄링에 얼마나 할당할 것인지를 설정함
- [예시] 100000(period), 25000(quota) → CPU 주기가 1/4 로 줄어 성능이 1/4 로 감소함

`--cpus` 

- 위와 동일한 기능을 하지만 좀 더 직관적으로 CPU 개수를 지정함
- 0.5라고 설정하면 period = 100000 또는 quota=50000과 동일하게 CPU 제한

**Block I/O 제한**

옵션이 없다면 컨테이너 내부에서 파일 읽고 쓰는 대역폭에 제한이 설정되지 않음 

→ 블록 입출력을 과도하게 사용하지 않게 설정하려면 

`--device-write-bps` , `--device-read-bps` , `--device-write-iops` , `--device-read-iops` 옵션으로 블록 입출력 제한 가능

Direct I/O 의 경우에만 블록 입출력이 제한되고 Buffered I/O는 제한 X

`--device-write-bps` , `--device-read-bps`

- 각기 쓰고 읽는 작업의 초당 제한 설정
- kb, mb, gb 단위로 제한 가능

`--device-write-iops` , `--device-read-iops`

- 상대적인 값 입력
    
    ![Image](https://github.com/user-attachments/assets/62be0c18-2a3b-4cec-acf5-622720a784c6)
    
    - 두 배 차이나는 컨테이너로 쓰기 작업 수행하면 수행 시간이 2배 가량 차이가 남

**스토리지 드라이버와 컨테이너 저장 공간 제한**

도커 엔진은 컨테이너 내부 저장 공간을 제한하는 기능을 보편적으로 제공하지 않지만, 도커 스토리지 드라이버나 파일 시스템 등 특정 조건을 만족하는 경우 제한적으로 사용 가능 

스토리지 드라이버 개념을 설명하지 않았기 때문에 생략

단, 모든 스토리지 드라이버에서 컨테이너 저장 공간을 제한할 수 있지 않음 

따라서 컨테이너 애플리케이션이 해당 스토리지 드라이버에 적합하지 않다면 기능을 사용하지 않는 것이 좋을 수 있음 
