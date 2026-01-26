# Ansible 패키지

노드 용도에 따라 원하는 플레이북을 실행하기 위한 패키지입니다.

## 실행 준비

### inventory 파일 작성

- /inventory.ini 파일을 작성합니다.
- 별도로 관리되는 inventory 파일을 사용한다면, script 내 셸 스크립트에서 -i 옵션의 값을 수정하여 사용합니다.

## 패키지 구조

```
PROJECT_ROOT
├── inventory.ini
├── group_vars
│   └── <타겟 그룹 이름>
│       ├── <타겟 그룹 이름>.yaml
│       └── <타겟 그룹 이름>.template.yaml
├── script
└── src
    ├── playbook
    │   └── <자동화 용도별 디렉터리>
    │       ├── <playbook>.yaml
    └── task
        └── <특정 기능별 디렉터리>
            ├── <task name>.<os 종류>-<os 메이저버전>.yaml
            └── <task name>.<os 종류>-<os 메이저버전>.yaml
```

- `/src/task`: NFS 마운트, Swap 메모리 비활성화 등 각 세부 기능의 task가 포함된 디렉터리. 같은 기능이라도 OS 종류와 버전에 따라 명령이 달라질 수 있으므로, 실제 task 파일명에 OS 종류 + 버전을 포함함.
- `/src/playbook`: 용도별 플레이북이 포함된 디렉터리. 플레이북 내부에 직접적인 태스크 작성을 피하고, /src/task에서 원하는 태스크를 include하여 실행.
- `/script`: ansible-playbook을 직접적으로 호출하는 셸 스크립트로, 자동화 작업의 엔트리포인트. 상황에 따라 2개 이상의 플레이북을 실행할 수 있으므로, 이러한 구조 채택.
- `/group_vars`: 타겟 그룹별 변수가 포함된 디렉터리. 타겟 그룹별 변수는 `group_vars/<타겟 그룹 이름>.yaml` 파일에 정의되며, git에 포함시키지 않습니다. template 파일을 참고하여 변수 파일을 새로 생성하여 사용합니다.

## 지원 환경

1. Linux

- Ubuntu
  - ✅ 24.04
  - ❓ 22.04
- Rocky Linux
  - ✅ 8

2. Python

- ✅ 3.12.3
- ✅ 3.9.25
- ❌ 3.6.8

3. Ansbile

- ✅ 2.19.3

## 그룹 변수 템플릿 사용

개발 환경의 인벤토리에는 k8s_dataplane과 같이 정해진 타겟 그룹 이름을 사용합니다.

이 타겟 그룹 이름은 `src/playbook/<target group 이름>/main.yaml` 등의 플레이북 파일에서 `hosts`로 사용됩니다.

그룹 변수는 `group_vars/<target group 이름>.yaml` 등의 파일에 정의되며,
필요한 변수들은 `group_vars/<target group 이름>.template.yaml` 템플릿 파일을 참고하여 정의합니다.

따라서 실제 사용 시, `group_vars/<target group 이름>.template.yaml` 템플릿 파일을 참고하여 `group_vars/<target group 이름>.yaml` 파일을 생성하여 사용합니다.
