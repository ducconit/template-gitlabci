# Package registry

## Sử dụng gitlabci để publish packages

Bạn có thể tham khảo mẫu sau đây hoặc kế thừa từ [đây](../template/publish-package-registry.yml)

```yaml
stages:
  - publish

publish:
  stage: publish
  rules:
    - if: $CI_COMMIT_TAG
  script:
    - curl --header "Job-Token: $CI_JOB_TOKEN" --data tag=$CI_COMMIT_TAG "${CI_API_V4_URL}/projects/$CI_PROJECT_ID/packages/composer"
```

## Setup môi trường để import packages

- Nếu là package private hoặc internal thì ta cần xác minh danh tính trước con nếu là public thì có thể bỏ qua bước này

```bash
# group access token (quyền read và write package registry)
composer config gitlab-oauth.<domain> <access-token>
# persional access token (quyền API là tối thiểu)
composer config gitlab-token.<domain> <personal-access-token>
# deploy token (quyền read và write package registry)
composer config gitlab-token.<domain> <deploy-token-username> <deploy-token>
```

*Bạn có thể hủy với lệnh sau:*

```bash
composer config --unset --auth gitlab-token.<domain>
```

- Tiếp theo bạn cần cho biết composer có thể đọc được package của bạn ở đâu

*Hãy đặt các packages của bạn vào trong 1 groups*

```bash
composer config repositories.{group-id} composer https://<domain>/api/v4/group/{group-id}/-/packages/composer/
```

- Import packages: Như bao packages được require từ composer thôi:

```bash
composer require <vendor>/<package>
```

Nguồn: [Gitlab](https://docs.gitlab.com/ee/user/packages/composer_repository/index.html)
