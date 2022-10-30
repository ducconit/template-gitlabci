# Container registry

## Sử dụng gitlabci để build và deploy images

- Bạn có thể tham khảo mẫu sau hoặc kế thừa từ [đây](../template/build-and-deploy-container-registry.yml)

```yaml
build and deploy image:
  stage: build
  variables:
    DOCKER_HOST: tcp://docker:2375
    DOCKER_TLS_CERTDIR: ""
  rules:
    if: $CI_COMMIT_TAG
  services:
    - docker:20.10.16-dind
  before_script:
    - echo $CI_JOB_TOKEN | docker login $CI_REGISTRY -u $CI_REGISTRY_USER --password-stdin
  script:
    - docker build -t $CI_REGISTRY_IMAGE:$CI_COMMIT_TAG $CI_REGISTRY_IMAGE:latest .
    - docker tag $CI_REGISTRY_IMAGE:$CI_COMMIT_TAG $CI_REGISTRY_IMAGE:latest
    - docker push $CI_REGISTRY_IMAGE:$CI_COMMIT_TAG
    - docker push $CI_REGISTRY_IMAGE:latest
```

## Pull image

Để pull image bạn cần login vào máy chủ gitlab lưu trữ container registry của bạn trước

```bash
# Username & password
docker login <registry URL> -u <username> -p <password>
# Access token (quyền read registry là tối thiểu)
docker login <registry URL> -u <username> -p <access-token>
# Group access token (quyền read registry là tối thiểu)
docker login <registry URL> -u oauth2 -p <access-token>
```

Sau khi đăng nhập thành công thì bạn có thể pull image theo format: `<registry URL>/<namespace>/<project>/<image>`

ví dụ: `registry.gitlab.com/myproject:some-tag`

hoặc vào **Packages & Registries** > **Container Registry** > chọn và copy phần image mà bạn muốn pull

chạy lệnh sau để pull image

```bash
docker pull <container registry image>:<tag>
```

ví dụ: docker pull registry.gitlab.com/myproject:some-tag

Nguồn: [Gitlab](https://docs.gitlab.com/ee/user/packages/container_registry/index.html)