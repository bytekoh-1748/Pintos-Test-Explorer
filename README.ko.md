# Pintos Test Explorer

언어: [English](README.md) | 한국어

VS Code에서 Pintos 테스트를 실행, 디버그, 초기화, 확인할 수 있고, 터미널에서는 같은 흐름을 `pt`나 `pintos-tests`로 그대로 사용할 수 있습니다.

## 시연 영상

[![시연 영상 보기](https://img.youtube.com/vi/FyJ1jKg3zNk/hqdefault.jpg)](https://youtu.be/FyJ1jKg3zNk)

바로 보기: [YouTube 시연 영상](https://youtu.be/FyJ1jKg3zNk)

## 무엇을 할 수 있나

- VS Code 전용 사이드바에서 `threads`, `userprog`, `vm`, `filesys` 테스트 탐색
- 각 테스트를 한 번 클릭으로 실행하거나 디버그
- 체크한 테스트 여러 개를 묶어서 배치 실행
- `output`, `result`, `errors` 아티팩트 빠르게 열기
- 선택한 테스트만 초기화하거나 워크스페이스 전체 초기화
- 로컬 터미널, 셸 스크립트, CI에서도 같은 CLI 사용
- 하나의 CLI를 `pt`와 `pintos-tests` 두 이름으로 제공

## 설치

### VS Code 확장

1. VS Code에서 `Extensions`를 엽니다.
2. `Pintos Test Explorer`를 검색합니다.
3. `Install`을 누릅니다.
4. Dev Container를 사용 중이면 컨테이너 안에도 설치합니다.
5. 설치 후 한 번 `Developer: Reload Window`를 실행합니다.

### 배포된 확장에서 CLI 쓰기

VS Code 통합 터미널에서 확장이 활성화되어 있으면 `pt`와 `pintos-tests`를 둘 다 자동으로 쓸 수 있습니다. 확장 활성화 뒤 새 터미널을 열고 다음처럼 실행하세요.

```bash
pt --help
pintos-tests --help
```

모든 셸에서 같은 명령을 계속 쓰고 싶다면 Command Palette에서 `Pintos: Install CLI Wrappers to Shell`을 실행하세요.

### 소스 체크아웃에서 CLI 쓰기

이 저장소를 직접 clone해서 작업한다면 보통 아래 세 가지 방식 중 하나를 쓰면 됩니다.

1. 한 번만 설치해서 모든 셸에서 쓰기:

```bash
source scripts/install-pintos-cli.sh
pt --help
```

2. 현재 셸에서만 잠깐 쓰기:

```bash
source scripts/pintos-shell.sh
pt --help
```

3. 저장소 안의 로컬 명령을 바로 호출하기:

```bash
./pt --help
./pintos-tests --help
```

세 방식 모두 결국 같은 CLI 구현을 실행합니다. 특히 3번은 CI에서 가장 설명하기 쉽습니다.

## 빠른 시작

### VS Code에서

1. Pintos 워크스페이스를 Dev Container나 Linux 환경에서 엽니다.
2. Activity Bar에서 `P os` 아이콘을 누릅니다.
3. `Threads`, `User Programs`, `Virtual Memory`, `File System` 중 하나를 펼칩니다.
4. 테스트 옆 초록색 `Run` 액션으로 바로 실행합니다.
5. 주황색 `Debug` 액션으로 단일 테스트 디버깅을 시작합니다.
6. 여러 테스트를 체크한 뒤 툴바의 `Run Checked Tests`를 사용합니다.
7. 정렬 버튼으로 `Number order`와 `Latest first`를 전환합니다.
8. 맨 왼쪽 빨간 `Reset All Tests`로 전체를 초기화하거나, `Reset Checked Tests`로 선택한 테스트만 초기화합니다.

테스트에 아티팩트가 있으면 트리에서 `output`, `result`, `errors` 링크가 함께 보입니다.

### 터미널에서

Pintos 워크스페이스 안에서는:

```bash
pt projects
pt list threads
pt run threads alarm-zero
pt run threads 11-20
pt debug vm 4 --server-only
pt reset threads alarm-zero
pt reset-all
pt artifacts threads alarm-zero
```

워크스페이스 밖에서는 `PINTOS_ROOT`를 직접 지정하면 됩니다.

```bash
PINTOS_ROOT=/path/to/pintos pt list threads
PINTOS_ROOT=/path/to/pintos pintos-tests run filesys all
```

`pt`는 짧은 일상용 이름이고, `pintos-tests`는 더 명시적인 긴 이름입니다. 두 명령은 완전히 같은 서브커맨드와 옵션을 받습니다.

## 명령어 레퍼런스

아래 명령은 모두 `pt`와 `pintos-tests` 둘 다에서 똑같이 동작합니다. 예시는 짧게 보이도록 `pt` 기준으로 적었습니다.

### `projects`

현재 워크스페이스에서 사용 가능한 Pintos 프로젝트 목록을 출력합니다.

```bash
pt projects
pt projects --json
```

스크립트에서 `threads`, `userprog`, `vm`, `filesys` 중 무엇이 있는지 먼저 확인하고 싶을 때 유용합니다.

### `list <project>`

하나의 프로젝트에 속한 테스트 목록을 출력합니다.

```bash
pt list threads
pt list vm --recent-first
pt list filesys --json
```

- `--recent-first`는 최근 사용한 테스트를 위로 올립니다.
- `--json`은 자동화에 쓰기 쉬운 형태로 출력합니다.

### `pick <project> [selectors...]`

selector를 해석한 뒤 매칭된 테스트 이름만 출력합니다. 주로 자동화나 다른 스크립트에서 쓰기 좋습니다.

```bash
pt pick threads 1 3-5 alarm-*
pt pick threads alarm-* --recent-first
pt pick threads alarm-zero --single
```

- `--single`은 정확히 하나만 매칭되도록 강제합니다.
- 다른 스크립트가 먼저 테스트 이름을 정규화한 뒤 `run`, `debug`, 아티팩트 업로드에 넘기고 싶을 때 편합니다.

### `run <project> [selectors...]`

하나 이상의 테스트를 실행합니다.

```bash
pt run threads alarm-zero
pt run threads 1 3-5 alarm-*
pt run filesys all
```

- 여기서는 `all`을 사용할 수 있습니다.
- selector를 생략하면 테스트 목록을 보여주고 인터랙티브하게 선택을 받습니다.
- 빌드 단계에서 실패하더라도 가능하면 `FAIL` 결과와 에러 아티팩트를 남깁니다.

### `debug <project> [selectors...]`

정확히 하나의 테스트를 디버깅합니다.

```bash
pt debug threads 12
pt debug threads alarm-zero
pt debug vm 4 --server-only
```

- `debug`는 정확히 하나의 테스트로 해석되어야 합니다.
- `--server-only`는 GDB를 바로 attach하지 않고 Pintos GDB 서버만 띄웁니다.
- selector를 생략하면 단일 테스트 선택 프롬프트로 들어갑니다.

### `reset <project> [selectors...]`

선택한 테스트의 `output`, `result`, `errors` 아티팩트를 삭제합니다.

```bash
pt reset threads alarm-zero
pt reset threads 4 7-9
pt reset threads all
pt reset threads alarm-* --json
```

- 여기서는 `all`을 사용할 수 있습니다.
- `--json`은 스크립트나 CI 로그에서 쓰기 좋은 요약을 출력합니다.
- selector를 생략하면 인터랙티브 선택으로 들어갑니다.

### `reset-all`

워크스페이스 전체의 테스트 아티팩트를 모두 삭제합니다.

```bash
pt reset-all
pt reset-all --json
```

프로젝트 하나만 지우는 것이 아니라 전체를 깨끗하게 초기화하고 싶을 때 사용합니다.

### `artifacts <project> <selector>`

정확히 하나의 테스트에 대한 아티팩트 경로를 출력합니다.

```bash
pt artifacts threads alarm-zero
pt artifacts threads alarm-zero --json
```

생성된 `output`, `result`, `errors` 파일을 열거나 업로드할 때 유용합니다.

## Selector 규칙

- `11-20`은 양끝 포함 숫자 범위입니다.
- `alarm-zero`는 정확한 짧은 이름으로 선택합니다.
- `tests/threads/alarm-zero` 형태도 사용할 수 있습니다.
- `alarm-*`는 와일드카드 패턴입니다.
- leaf 이름만 써도 유일하게 매칭되면 사용할 수 있습니다.
- `all`은 `run`과 프로젝트 단위 `reset`에서만 지원합니다.
- `debug`와 `artifacts`는 정확히 하나의 테스트로만 해석되어야 합니다.

selector가 매칭되지 않으면 CLI가 비슷한 이름을 추천해줍니다.

## 아티팩트와 사용 기록

테스트 실행 후에는 보통 아래 파일들이 생깁니다.

- `output`: 실행 출력
- `result`: `PASS`, `FAIL` 같은 결과 요약
- `errors`: 오류 상세 내용

VS Code 트리와 CLI는 같은 아티팩트를 함께 사용합니다. `--recent-first`는 로컬 run/debug 기록을 이용해 자주 쓰는 테스트를 위로 올려줍니다.

## CI와 자동화

`pt`와 `pintos-tests`는 의도적으로 서로 바꿔 써도 되게 만든 이름입니다. 다른 동작을 하는 별도 도구가 아닙니다. `pt`는 짧은 이름이고, `pintos-tests`는 더 명시적인 이름일 뿐입니다. 그래서 CI, 셸 스크립트, 로컬 터미널에서 두 이름을 자유롭게 섞어 써도 됩니다.

실전 기준으로는 이렇게 보면 됩니다.

- CI 로그를 더 명확하게 남기고 싶으면 `./pintos-tests ...`
- 명령을 짧게 쓰고 싶으면 `./pt ...`
- 현재 셸 step에서 `PATH`에 올려 쓰고 싶으면 `source scripts/pintos-shell.sh`
- 자동화에는 `projects --json`, `list --json`, `pick`, `artifacts --json`, `reset --json`, `reset-all --json`이 특히 유용함

예시:

```bash
./pintos-tests list threads --json
./pt pick threads alarm-zero --single
./pintos-tests run threads 1 3-5
./pt artifacts threads alarm-zero --json
```

하나의 workflow 안에서 두 이름을 섞어 써도 괜찮습니다.

```yaml
- name: Run Pintos smoke tests
  run: |
    ./pintos-tests run threads alarm-zero
    ./pt run threads 1-3
```

## 요구 사항

- VS Code `1.85.0` 이상
- 아래 둘 중 하나 형태의 Pintos 워크스페이스
  - `<workspace>/threads`, `<workspace>/userprog`, `<workspace>/vm`, `<workspace>/tests`
  - `<workspace>/pintos/threads`, `<workspace>/pintos/userprog`, `<workspace>/pintos/vm`, `<workspace>/pintos/tests`
- `make`가 가능한 Linux 또는 Dev Container 환경
- 디버깅용 `gdb`
- `ms-vscode.cpptools`

## 문제 해결

- `pt list ...`에서 Pintos 루트를 찾지 못한다면 실제 Pintos 워크스페이스를 열거나 `PINTOS_ROOT=/path/to/pintos`를 지정하세요.
- VS Code 디버깅이 실패하면 먼저 `Pintos Tests` 출력 채널을 확인하세요.
- 실행이 컴파일 또는 빌드 에러로 중단되면, 확장은 가능하면 해당 테스트를 `FAIL`로 표시하고 에러 아티팩트를 남깁니다.
- 디버깅이 시작되지 않으면 활성 환경에서 `gdb`가 설치되어 있는지 확인하세요.

## 추가 문서

- [`extension/README.md`](extension/README.md)
- [`extension/SUPPORT.md`](extension/SUPPORT.md)
- [`extension/CHANGELOG.md`](extension/CHANGELOG.md)
