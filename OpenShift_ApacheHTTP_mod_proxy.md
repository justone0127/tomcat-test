# OpenShift Apache HTTP - mod_proxy

- Red Hat 에서 제공하는 Apache HTTP 이미지, 템플릿을 사용
  - registry.redhat.io/rhscl/httpd-24-rhel7
- jsp, do 등의 mod_proxy로 연계할 애플리케이션



## 1. 환경

- 사용할 프로젝트 생성 : mod-proxy-test
- Apache HTTP에서 사용할 애플리케이션 소스를 준비하고 OpenShift에서 Catalog를 통해 실행합니다.
  - 구성할 프로젝트 선택
  - 좌측 Catalog에서 EAP나 Tomcat을 선택 후 배포 (샘플로 진행 가능)
  - 좌측 Catalog에서 Apache HTTP를 선택 후 배포 (샘플로 진행 가능)



## 2. OpenShift Apache HTTP 확인

- 실행 된 Aapache HTTP (e.g. http-sample) Pod를 선택하여 terminal로 이동합니다.

- /etc/httpd/conf/httpd.conf 의 마지막 줄에 `include` 항목을 확인

  ```ini
  ...
  IncludeOptional /opt/app-root/etc/httpd.d/*.conf
  ```

- /opt/app-root/etc/httpd.d 디렉토리는 기본으로는 존재하지 않고, 해당 디렉토리에 사용자가 작성한 conf를 등록





## 3. Config map 작성

- 좌측 Resource > Config Map 을 선택하여 `Create Config Map`으로 새로운 Config Map을 생성

  - Name: mod-proxy (e.g.)

  - Key : mod_proxy.conf

  - Value

    ```ini
    ProxyPassMatch ^/.*\.(jsp|do)$ http://sample-app:8080
    ```

- 생성 후 해당 `mod-proxy` Config Map을 선택

- 우측 상단의 Add to Application
  - Add this config map to application: httpd-example (e.g.)
  - Volume 입력: /opt/app-root/etc/httpd.d
- 재배포가 발생



## 4. Test

구성된 Apache HTTD의 Route url 을 클릭하고 확장자가 앞서 mod_proxy.conf에서 설정한 jsp, do로 끝나는 확장자의 경우 해당 애플리케이션으로 호출 되는지 확인