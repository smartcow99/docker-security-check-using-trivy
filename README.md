# Docker-security-check-using-Trivy🔎

## 개요📑
> 클라우드 환경에서 보안은 항상 중요한 문제로 자리잡고 있습니다.
> 
> 특히, Docker의 Container Image는 널리 사용되는 만큼 중요하다고 생각했습니다.
> 
> 이러한 과정에서 ***Trivy***를 발견하게 되었고, 관련 학습을 진행하게 되었습니다.


## 👨‍💻Team

|<img src="https://avatars.githubusercontent.com/u/139302518?v=4" width="100" height="100"/>|<img src="https://avatars.githubusercontent.com/u/78792358?v=4" width="100" height="100"/>|
|:-:|:-:|
|곽병찬<br/>[@gato-46](https://github.com/gato-46)|박현우<br/>[@smartcow99](https://github.com/smartcow99)|

![image](https://github.com/user-attachments/assets/d73ba76b-c9f5-416d-9683-ada643f1339d)

> ##### [Trivy](https://trivy.dev/) is a comprehensive and versatile security scanner. 
> ##### 트리비는 포괄적이고 다재다능한 보안 스캐너입니다.  

<br>

**트리비의 타겟🚀**:

 - Container Image
 - Filesystem
 - Git Repository (remote)
 - Virtual Machine Image
 - Kubernetes
 - AWS

**트리비가 찾을 수 있는 문제점들❗**:

 - OS packages and software dependencies in use (SBOM)
 - Known vulnerabilities (CVEs)
 - IaC issues and misconfigurations
 - Sensitive information and secrets
 - Software licenses


## 실습💻
> ##### Docker Container Image를 직접 만들어보고 취약점 검사를 진행하여 해결방법 제시

#### 1. 환경설정
```bash
> docker run aquasec/trivy
```

#### 2. Docker Image 생성
> Main.java
```java
public class Main{
        public static void main(String [] args) {
                System.out.println("Image Build by DockerFile!");
        }
}
```

> Dockerfile
```bash
FROM openjdk:17
COPY . /usr/src/myapp
WORKDIR /usr/src/myapp
RUN javac Main.java
CMD ["java", "Main"]
```

```bash
> docker build -t trivytest
```

#### 3. Trivy를 사용하여 취약점 검사

```bash
> docker run --rm -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image --severity HIGH,CRITICAL trivytest
```

#### 4. 취약점과 해결 방법 정리


| 라이브러리  | 취약점  | 심각도 | 문제 설명  | 해결 방법  | 설치된 버전  | 수정된 버전  |
|-------------|---------|--------|------------|------------|--------------|--------------|
| expat       | CVE-2022-40674 | HIGH | `xmlparse.c`의 `doContent` 함수에서 발생하는 Use-after-free 취약점. 이로 인해 메모리 손상이 발생할 수 있으며, 잠재적으로 악용될 가능성이 있음. | 최신 버전으로 업데이트하여 해당 취약점을 수정함. | 2.2.5-4.0.1.el8_5.3 | 2.2.5-8.0.1.el8_6.3 |
| glibc       | CVE-2023-4527  | HIGH | `getaddrinfo` 함수에서 `no-aaaa` 모드로 인해 발생하는 스택 읽기 오버플로우. 잘못된 DNS 응답으로 인해 시스템에 손상을 줄 수 있음. | 해당 문제를 해결한 패치 버전으로 업데이트. | 2.28-164.0.5.el8_5.3 | 2.28-225.0.4.el8_8.6 |
| glibc       | CVE-2023-4806  | MEDIUM | `getaddrinfo()` 함수에서 발생하는 Use-after-free 취약점. 메모리 손상으로 인해 악성 코드 실행의 위험이 있음. | 해당 문제를 해결한 버전으로 업데이트. | 2.28-164.0.5.el8_5.3 | 2.28-225.0.4.el8_8.6 |
| glibc       | CVE-2023-4813  | MEDIUM | `gaih_inet()` 함수에서 발생하는 Use-after-free 취약점. 시스템이 비정상적으로 종료될 수 있는 문제를 유발함. | 업데이트된 패치 적용. | 2.28-164.0.5.el8_5.3 | 2.28-225.0.4.el8_8.6 |
| glibc       | CVE-2023-4911  | HIGH | `ld.so`에서 버퍼 오버플로우가 발생하여 권한 상승의 위험이 있음. | 버퍼 오버플로우 문제를 해결한 버전으로 업데이트. | 2.28-164.0.5.el8_5.3 | 2.28-225.0.4.el8_8.6 |
| glibc       | CVE-2024-2961  | HIGH | `iconv`에서 발생하는 Out-of-bounds 쓰기 취약점. 이로 인해 원격 코드 실행이 발생할 수 있음. | 해당 취약점을 해결한 최신 버전으로 업데이트. | 2.28-164.0.5.el8_5.3 | 2.28-251.0.2.el8_10.1 |
| glibc       | CVE-2024-33599 | HIGH | `netgroup` 캐시에서 스택 기반 버퍼 오버플로우가 발생하여 시스템이 비정상적으로 동작할 수 있음. | 패치된 버전으로 업데이트 필요. | 2.28-164.0.5.el8_5.3 | 2.28-251.0.2.el8_10.2 |
| glibc       | CVE-2024-33600 | MEDIUM | `netgroup` 캐시 실패 시 null 포인터 역참조로 인해 시스템이 비정상 종료될 수 있음. | 해당 문제를 해결한 패치 적용. | 2.28-164.0.5.el8_5.3 | 2.28-251.0.2.el8_10.2 |
| glibc       | CVE-2024-33601 | MEDIUM | `netgroup` 캐시의 메모리 할당 실패 시 데몬이 종료될 수 있음. | 패치된 버전으로 업데이트. | 2.28-164.0.5.el8_5.3 | 2.28-251.0.2.el8_10.2 |
| glibc       | CVE-2024-33602 | LOW | `NSS` 콜백이 버퍼 내 문자열을 사용할 것이라고 가정하여 발생하는 문제. | 최신 버전으로 업데이트하여 문제 해결. | 2.28-164.0.5.el8_5.3 | 2.28-251.0.2.el8_10.2 |
| glibc-minimal-langpack| CVE-2023-4527  | MEDIUM | `getaddrinfo`의 no-aaaa 모드에서 스택 읽기 오버플로우 발생                                      | 최신 glibc 패키지로 업데이트                                   | 2.28-225.0.4.el8_8.6    | 2.28-251.0.2.el8_10.1  |
| glibc-minimal-langpack| CVE-2023-4806  | MEDIUM   | `getaddrinfo()`에서 잠재적인 use-after-free 취약점                                              | 최신 glibc 패키지로 업데이트                                   | 2.28-225.0.4.el8_8.6    | 2.28-251.0.2.el8_10.1  |
| glibc-minimal-langpack| CVE-2023-4813  | MEDIUM | `gaih_inet()`에서 잠재적인 use-after-free 취약점                                                | 최신 glibc 패키지로 업데이트                                   | 2.28-225.0.4.el8_8.6    | 2.28-251.0.2.el8_10.1  |
| glibc-minimal-langpack| CVE-2023-4911  | HIGH | `ld.so`에서의 버퍼 오버플로우로 인한 권한 상승 문제 발생                                        | 최신 glibc 패키지로 업데이트                                   | 2.28-225.0.4.el8_8.6    | 2.28-251.0.2.el8_10.1  |
| glibc-minimal-langpack| CVE-2024-2961  | HIGH | `iconv`에서의 경계 밖 쓰기로 인해 원격 코드 실행 가능성 발생                                   | 최신 glibc 패키지로 업데이트                                   | 2.28-225.0.4.el8_8.6    | 2.28-251.0.2.el8_10.1  |
| krb5-libs             | CVE-2022-42898 | MEDIUM | `PAC` 구문 분석에서 정수 오버플로우 취약점으로 인한 보안 문제                                  | 최신 krb5 패키지로 업데이트                                    | 1.18.2-14.0.1.el8       | 1.18.2-22.0.1.el8_7    |
| libgcc                | CVE-2022-40982 | MEDIUM | 인텔 `Gather Data Sampling`(GDS) 사이드 채널 취약점                                             | 최신 libgcc 패키지로 업데이트                                   | 8.5.0-4.0.2.el8_5       | 8.5.0-18.0.5.el8       |
| libgcrypt             | CVE-2021-40528 | HIGH | `ElGamal` 구현에서 평문 복구 가능성 문제 발생                                                   | 최신 libgcrypt 패키지로 업데이트                               | 1.8.5-6.el8             | 1.8.5-7.el8_6_fips     |
| libksba               | CVE-2022-3515  | HIGH | 정수 오버플로우로 인해 원격 코드 실행 가능성 발생                                               | 최신 libksba 패키지로 업데이트                                 | 1.3.5-7.el8             | 1.3.5-8.el8_6          |
| libnghttp2            | CVE-2023-44487 | MEDIUM | 여러 HTTP/2 지원 웹 서버가 DDoS 공격에 취약함                                                   | 최신 libnghttp2 패키지로 업데이트                              | 1.33.0-3.el8_2.1        | 1.33.0-5.el8_8         |
| openssl-libs          | CVE-2022-4304  | MEDIUM | RSA 복호화 구현에서의 타이밍 공격 문제 발생                                                     | 최신 openssl 패키지로 업데이트                                 | 1.1.1k-6.el8_5          | 1.1.1k-9.el8_7         |
| systemd-libs          | CVE-2022-2526  | MEDIUM | `DnsStream` 처리 시 발생하는 use-after-free 취약점 발생                                         | 최신 systemd-libs 패키지로 업데이트                            | 239-51.0.1.el8_5.5      | 239-58.0.1.el8_6.4     |
| xz-libs               | CVE-2022-1271  | MEDIUM | gzip에서 임의 파일 쓰기 취약점 발생                                                             | 최신 xz-libs 패키지로 업데이트                                 | 5.2.4-3.el8             | 5.2.4-4.el8_6          |
| zlib                  | CVE-2018-25032 | MEDIUM | `zlib`에서 특정 입력을 압축할 때 발생하는 보안 문제                                              | 최신 zlib 패키지로 업데이트                                    | 1.2.11-17.el8           | 1.2.11-18.el8_5        |

## Troubleshooting🔥
> ##### 이미지가 로컬에 있지만, Trivy가 이미지를 원격 레지스트리에서 찾으려고 시도하는 문제 발생🤦‍♂️
- trivytest라는 이미지는 로컬에서 확인되지만, Trivy가 해당 이미지를 원격 레지스트리에서 찾으려고 시도하는 문제를 확인했습니다.
- `-v /var/run/docker.sock:/var/run/docker.sock`을 통해 docker 방식을 명시적으로 지정하고 로컬 이미지를 스캔하는 방식으로 변경하여 문제 해결하였습니다.


기존 코드
```bash
docker run --rm aquasec/trivy image trivytest
```
변경 코드
```bash
docker run --rm -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image --severity HIGH,CRITICAL trivytest
```

> ##### -v /var/run/docker.sock:/var/run/docker.sock을 사용한 이유
#### Docker Daemon 접근

- **Docker 소켓 파일**: `/var/run/docker.sock`는 Docker 데몬과의 통신을 위한 소켓 파일입니다. 이 파일을 통해 Docker 클라이언트가 Docker 데몬에 명령을 전달합니다.
- **컨테이너에서 Docker 사용**: 기본적으로 Docker 컨테이너는 호스트 시스템의 Docker daemon에 직접 접근할 수 없습니다. 하지만 이 소켓 파일을 컨테이너에 마운트하면, 컨테이너 내에서 Docker 명령어를 실행하여 호스트의 Docker 환경에 접근할 수 있게 됩니다.

#### 스캔할 이미지 찾기

- **이미지 접근**: `trivy`는 스캔할 이미지를 찾기 위해 Docker daemon에 요청을 보냅니다. 따라서 컨테이너가 `/var/run/docker.sock`을 통해 Docker daemon에 접근할 수 있어야 합니다.
- **로컬 이미지 스캔**: `trivy`는 주로 로컬 Docker 이미지나 컨테이너를 스캔하는 데 사용됩니다. Docker 소켓을 마운트함으로써, `trivy`가 해당 이미지를 조회하고 분석할 수 있는 권한을 부여받게 됩니다.

#### 보안 및 편리성

- **보안**: 이 방식은 간단하고 효과적이지만, 보안에 유의해야 합니다. 컨테이너가 Docker daemon에 접근할 수 있다는 것은, 해당 컨테이너가 호스트의 모든 Docker 리소스에 접근할 수 있다는 의미이므로, 신뢰할 수 있는 이미지와 환경에서만 사용하는 것이 좋습니다.
- **편리성**: 개발 및 테스트 환경에서 매우 유용합니다. Docker 소켓을 사용하면, CI/CD 파이프라인이나 자동화된 스크립트에서도 쉽게 Docker 이미지를 스캔하고 결과를 얻을 수 있습니다.

이러한 이유로 `/var/run/docker.sock`을 마운트하는 것은 `trivy`와 같은 도구를 사용하여 Docker 이미지를 효과적으로 스캔할 수 있도록 하는 중요한 단계입니다.
