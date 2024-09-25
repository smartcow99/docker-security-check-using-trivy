# Docker-security-check-using-Trivy

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


## 실습
> ##### Docker Container Image를 대상으로 취약점 검사

#### 1. 
```bash
> docker run aquasec/trivy
```

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

```bash
> docker run --rm -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image --severity HIGH,CRITICAL trivytest
```

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
| glibc-minimal-langpack| CVE-2023-4527  | 중간   | `getaddrinfo`의 no-aaaa 모드에서 스택 읽기 오버플로우 발생                                      | 최신 glibc 패키지로 업데이트                                   | 2.28-225.0.4.el8_8.6    | 2.28-251.0.2.el8_10.1  |
| glibc-minimal-langpack| CVE-2023-4806  | 중간   | `getaddrinfo()`에서 잠재적인 use-after-free 취약점                                              | 최신 glibc 패키지로 업데이트                                   | 2.28-225.0.4.el8_8.6    | 2.28-251.0.2.el8_10.1  |
| glibc-minimal-langpack| CVE-2023-4813  | 중간   | `gaih_inet()`에서 잠재적인 use-after-free 취약점                                                | 최신 glibc 패키지로 업데이트                                   | 2.28-225.0.4.el8_8.6    | 2.28-251.0.2.el8_10.1  |
| glibc-minimal-langpack| CVE-2023-4911  | 높음   | `ld.so`에서의 버퍼 오버플로우로 인한 권한 상승 문제 발생                                        | 최신 glibc 패키지로 업데이트                                   | 2.28-225.0.4.el8_8.6    | 2.28-251.0.2.el8_10.1  |
| glibc-minimal-langpack| CVE-2024-2961  | 높음   | `iconv`에서의 경계 밖 쓰기로 인해 원격 코드 실행 가능성 발생                                   | 최신 glibc 패키지로 업데이트                                   | 2.28-225.0.4.el8_8.6    | 2.28-251.0.2.el8_10.1  |
| krb5-libs             | CVE-2022-42898 | 중간   | `PAC` 구문 분석에서 정수 오버플로우 취약점으로 인한 보안 문제                                  | 최신 krb5 패키지로 업데이트                                    | 1.18.2-14.0.1.el8       | 1.18.2-22.0.1.el8_7    |
| libgcc                | CVE-2022-40982 | 중간   | 인텔 `Gather Data Sampling`(GDS) 사이드 채널 취약점                                             | 최신 libgcc 패키지로 업데이트                                   | 8.5.0-4.0.2.el8_5       | 8.5.0-18.0.5.el8       |
| libgcrypt             | CVE-2021-40528 | 높음   | `ElGamal` 구현에서 평문 복구 가능성 문제 발생                                                   | 최신 libgcrypt 패키지로 업데이트                               | 1.8.5-6.el8             | 1.8.5-7.el8_6_fips     |
| libksba               | CVE-2022-3515  | 높음   | 정수 오버플로우로 인해 원격 코드 실행 가능성 발생                                               | 최신 libksba 패키지로 업데이트                                 | 1.3.5-7.el8             | 1.3.5-8.el8_6          |
| libnghttp2            | CVE-2023-44487 | 중간   | 여러 HTTP/2 지원 웹 서버가 DDoS 공격에 취약함                                                   | 최신 libnghttp2 패키지로 업데이트                              | 1.33.0-3.el8_2.1        | 1.33.0-5.el8_8         |
| openssl-libs          | CVE-2022-4304  | 중간   | RSA 복호화 구현에서의 타이밍 공격 문제 발생                                                     | 최신 openssl 패키지로 업데이트                                 | 1.1.1k-6.el8_5          | 1.1.1k-9.el8_7         |
| systemd-libs          | CVE-2022-2526  | 중간   | `DnsStream` 처리 시 발생하는 use-after-free 취약점 발생                                         | 최신 systemd-libs 패키지로 업데이트                            | 239-51.0.1.el8_5.5      | 239-58.0.1.el8_6.4     |
| xz-libs               | CVE-2022-1271  | 중간   | gzip에서 임의 파일 쓰기 취약점 발생                                                             | 최신 xz-libs 패키지로 업데이트                                 | 5.2.4-3.el8             | 5.2.4-4.el8_6          |
| zlib                  | CVE-2018-25032 | 중간   | `zlib`에서 특정 입력을 압축할 때 발생하는 보안 문제                                              | 최신 zlib 패키지로 업데이트                                    | 1.2.11-17.el8           | 1.2.11-18.el8_5        |


