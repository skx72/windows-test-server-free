# Docker 컨테이너에서 실행하는 Windows

Windows를 Docker 컨테이너 안에서 실행합니다.

## 주요 기능 ✨

- Docker 컨테이너 안에서 Windows 실행
- 자동 다운로드 및 무인 설치
- 최신 Windows와 구형 Windows 버전 지원
- KVM 가속을 통한 네이티브에 가까운 성능
- CPU, 메모리, 저장 공간 할당을 사용자 지정 가능
- 메모리 벌루닝을 통한 동적 메모리 할당
- USB 장치 전달 및 호스트 폴더 공유
- NAT, 사용자 모드, macvlan, macvtap 네트워킹 지원

## 사용 방법 🐳

### Docker Compose

```yaml
services:
  windows:
    image: dockurr/windows
    container_name: windows
    environment:
      VERSION: "11"
    devices:
      - /dev/kvm
      - /dev/net/tun
    cap_add:
      - NET_ADMIN
    ports:
      - 8006:8006
      - 3389:3389/tcp
      - 3389:3389/udp
    volumes:
      - ./windows:/storage
    restart: always
    stop_grace_period: 2m
```

### Docker CLI

```bash
docker run -it --rm --name windows -e "VERSION=11" -p 8006:8006 --device=/dev/kvm --device=/dev/net/tun --cap-add NET_ADMIN -v "${PWD:-.}/windows:/storage" --stop-timeout 120 docker.io/dockurr/windows
```

### Kubernetes

```shell
kubectl apply -f https://raw.githubusercontent.com/dockur/windows/refs/heads/master/kubernetes.yml
```

### 데스크톱 애플리케이션

완전한 그래픽 데스크톱 환경을 원한다면 [WinBoat](https://winboat.app), [WinPodX](https://www.winpodx.org), 또는 [WinApps](https://github.com/winapps-org/winapps)를 참고하세요. 이 프로젝트들은 모두 이 컨테이너를 백엔드로 사용합니다.

### GitHub Codespaces

[GitHub Codespaces에서 열기](https://codespaces.new/dockur/windows)

## 요구 사항 ⚙️

- KVM을 지원하는 Linux 호스트의 Docker 또는 Podman
- 중첩 가상화가 활성화된 Windows 11의 Docker Desktop 또는 Podman Desktop
- 사용 가능한 RAM 최소 2GB
- 여유 디스크 공간 최소 32GB

> [!NOTE]
> Linux, macOS 및 Windows 10의 Docker Desktop은 현재 컨테이너에 KVM 접근을 제공하지 않으므로 지원되지 않습니다.

## 자주 묻는 질문 💬

### 어떻게 사용하나요?

아주 간단합니다. 다음 단계를 따르세요.

- 컨테이너를 시작한 뒤 웹 브라우저에서 [포트 8006](http://127.0.0.1:8006/)에 접속합니다.
- 잠시 기다리면 설치가 자동으로 모두 진행됩니다.
- 바탕 화면이 표시되면 Windows 설치가 완료되어 사용할 수 있습니다.

새로운 가상 머신을 즐겨 보세요. 이 저장소에 별표(Star)를 누르는 것도 잊지 마세요!

### Windows 버전은 어떻게 선택하나요?

기본적으로 Windows 11 Pro가 설치됩니다. 다른 Windows 버전을 다운로드하려면 Compose 파일에 `VERSION` 환경 변수를 추가하세요.

```yaml
environment:
  VERSION: "10"
```

아래 값 중에서 선택할 수 있습니다.

| 값 | 버전 | 크기 |
|---|---|---:|
| `11` | Windows 11 Pro | 7.9 GB |
| `11l` | Windows 11 LTSC | 4.7 GB |
| `11e` | Windows 11 Enterprise | 6.6 GB |
| `10` | Windows 10 Pro | 5.7 GB |
| `10l` | Windows 10 LTSC | 4.6 GB |
| `10e` | Windows 10 Enterprise | 5.2 GB |
| `8e` | Windows 8.1 Enterprise | 3.7 GB |
| `7u` | Windows 7 Ultimate | 3.1 GB |
| `vu` | Windows Vista Ultimate | 3.0 GB |
| `xp` | Windows XP Professional | 0.6 GB |
| `2k` | Windows 2000 Professional | 0.4 GB |
| `me` | Windows ME | 0.5 GB |
| `98` | Windows 98 | 0.7 GB |
| `95` | Windows 95 | 0.6 GB |
| `2025` | Windows Server 2025 | 7.6 GB |
| `2022` | Windows Server 2022 | 6.0 GB |
| `2019` | Windows Server 2019 | 5.3 GB |
| `2016` | Windows Server 2016 | 6.5 GB |
| `2012` | Windows Server 2012 | 4.3 GB |
| `2008` | Windows Server 2008 | 3.0 GB |
| `2003` | Windows Server 2003 | 0.6 GB |
| `core11` | Tiny11 Core | 3.0 GB |
| `tiny11` | Tiny11 | 5.3 GB |
| `tiny10` | Tiny10 | 3.6 GB |
| `reactos` | ReactOS | 0.1 GB |

> [!TIP]
> Windows ARM64 버전을 설치하려면 [dockur/windows-arm](https://github.com/dockur/windows-arm/)을 사용하세요.

### 저장 위치는 어떻게 변경하나요?

Compose 파일에 다음 바인드 마운트를 추가해 저장 위치를 변경할 수 있습니다.

```yaml
volumes:
  - ./windows:/storage
```

예시 경로인 `./windows`를 원하는 저장 폴더 또는 이름이 지정된 볼륨으로 바꾸세요.

### 디스크 크기는 어떻게 변경하나요?

기본 디스크 크기인 64GB를 늘리려면 Compose 파일에 `DISK_SIZE` 설정을 추가하고 원하는 용량을 지정하세요.

```yaml
environment:
  DISK_SIZE: "256G"
```

> [!TIP]
> 이 설정은 기존 디스크의 크기를 데이터 손실 없이 늘리는 데에도 사용할 수 있습니다. 다만 추가 공간은 할당되지 않은 공간으로 표시되므로, 이후 디스크 파티션을 수동으로 확장해야 합니다. [디스크 파티션 확장 안내](https://learn.microsoft.com/en-us/windows-server/storage/disk-management/extend-a-basic-volume?tabs=disk-management)를 참고하세요.

### 호스트와 파일을 어떻게 공유하나요?

설치가 끝나면 바탕 화면에 `Shared` 폴더가 생성됩니다. 이 폴더를 사용해 호스트 컴퓨터와 파일을 주고받을 수 있습니다.

호스트 컴퓨터의 특정 폴더를 공유하려면 Compose 파일에 다음 바인드 마운트를 추가하세요.

```yaml
volumes:
  - ./example:/shared
```

`./example`을 원하는 폴더 경로로 바꾸세요. 해당 폴더는 Windows 바탕 화면의 `Shared` 폴더와 `Z:` 드라이브에서 표시됩니다.

### CPU 또는 RAM 용량은 어떻게 변경하나요?

기본적으로 Windows에는 CPU 코어 2개와 RAM 4GB가 할당됩니다.

이를 변경하려면 다음 환경 변수에 원하는 값을 지정하세요.

```yaml
environment:
  RAM_SIZE: "8G"
  CPU_CORES: "4"
```

### 오디오는 어떻게 활성화하나요?

RDP를 사용하는 경우가 아니라면 기본적으로 오디오가 비활성화되어 있습니다. 브라우저로 오디오를 스트리밍하려면 다음 환경 변수를 추가하세요.

```yaml
environment:
  AUDIO: "Y"
```

그런 다음 웹 뷰어의 **설정 → 고급**에서 **Audio(오디오)**를 활성화하세요. 이 옵션이 켜져 있는 동안에만 스트리밍이 작동하므로, 꺼져 있을 때는 대역폭을 사용하지 않습니다.

### RDP로 어떻게 연결하나요?

웹 뷰어는 RDP보다 반응 속도가 느리고 클립보드 공유 등의 기능을 지원하지 않으므로 주로 설치 과정에서 사용하도록 만들어졌습니다.

더 나은 사용 경험을 원한다면 Microsoft 원격 데스크톱 클라이언트를 사용해 컨테이너의 IP 주소로 연결하세요. 사용자 이름은 `Docker`, 비밀번호는 `admin`입니다.

- Android용 RDP 클라이언트는 [Google Play 스토어](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx)에서 이용할 수 있습니다.
- iOS용 클라이언트는 [Apple App Store](https://apps.apple.com/nl/app/microsoft-remote-desktop/id714464092?l=en-GB)에서 이용할 수 있습니다.
- Linux에서는 [FreeRDP](https://www.freerdp.com/)를 사용할 수 있습니다.
- Windows에서는 검색창에 `mstsc`를 입력하세요.

### 사용자 이름과 비밀번호는 어떻게 설정하나요?

기본적으로 `Docker`라는 사용자가 생성되며 비밀번호는 `admin`입니다.

설치 중 다른 로그인 정보를 설정하려면 Compose 파일에 다음을 지정하세요.

```yaml
environment:
  USERNAME: "bill"
  PASSWORD: "gates"
```

`DOMAIN`을 설정한 경우 이 변수들은 도메인 로그인 정보로 사용됩니다.

### Active Directory 도메인에 어떻게 가입하나요?

설치 중 Windows가 Active Directory 도메인에 자동으로 가입하도록 설정할 수 있습니다. Compose 파일에 도메인 이름을 지정하세요.

```yaml
environment:
  DOMAIN: "example.com"
  DOMAIN_OU: "OU=Virtual Machines,OU=Servers,DC=example,DC=com"
```

`https://` 같은 URL이 아니라 `example.com`과 같은 도메인 이름을 사용하세요. 입력한 계정은 로컬 Administrators 그룹에 추가되고, 설치 후 자동으로 로그인됩니다. `DOMAIN_OU`는 선택 사항이며 컴퓨터 계정을 생성할 위치를 지정합니다.

Windows는 도메인의 DNS 서버를 통해 도메인 컨트롤러의 주소를 확인하고 연결할 수 있어야 합니다.

### Windows 언어는 어떻게 선택하나요?

기본적으로 영어 버전의 Windows가 다운로드됩니다.

다른 언어를 다운로드하려면 Compose 파일에 `LANGUAGE` 환경 변수를 추가하세요.

```yaml
environment:
  LANGUAGE: "French"
```

선택할 수 있는 언어는 아랍어, 불가리아어, 중국어, 크로아티아어, 체코어, 덴마크어, 네덜란드어, 영어, 에스토니아어, 핀란드어, 프랑스어, 독일어, 그리스어, 히브리어, 헝가리어, 이탈리아어, 일본어, 한국어, 라트비아어, 리투아니아어, 노르웨이어, 폴란드어, 포르투갈어, 루마니아어, 러시아어, 세르비아어, 스페인어, 스웨덴어, 태국어, 터키어, 우크라이나어입니다.

### 키보드 배열은 어떻게 선택하나요?

선택한 언어의 기본값과 다른 키보드 배열이나 지역 설정을 사용하려면 `KEYBOARD` 및 `REGION` 변수를 다음과 같이 추가하세요.

```yaml
environment:
  REGION: "en-US"
  KEYBOARD: "en-US"
```

### 사용자 지정 이미지는 어떻게 설치하나요?

지원되지 않는 ISO 이미지를 다운로드하려면 `VERSION` 환경 변수에 해당 이미지의 URL을 지정하세요.

```yaml
environment:
  VERSION: "https://example.com/win.iso"
```

또는 다운로드를 건너뛰고 로컬 파일을 사용할 수도 있습니다. Compose 파일에 다음과 같이 바인드 마운트를 추가하세요.

```yaml
volumes:
  - ./example.iso:/custom.iso
```

`./example.iso`를 사용할 ISO 파일 이름으로 바꾸세요. 이 경우 `VERSION` 값은 무시됩니다.

### 설치 후 명령을 실행하려면 어떻게 하나요?

자동 설치의 마지막 단계에서 명령 하나를 실행하려면 `COMMAND` 환경 변수를 추가하세요.

```yaml
environment:
  COMMAND: 'reg add "HKLM\Software\Example" /v Enabled /t REG_DWORD /d 1 /f'
```

스크립트를 실행하거나 추가 파일을 포함하려면 `install.bat` 파일을 만들고 필요한 파일들과 함께 폴더에 넣으세요.

그런 다음 Compose 파일에 다음과 같이 해당 폴더를 연결하세요.

```yaml
volumes:
  - ./example:/oem
```

예시 폴더 `./example`은 `C:\OEM`으로 복사되며, 그 안의 `install.bat` 파일은 자동 설치의 마지막 단계에서 실행됩니다.

### 컨테이너에 개별 IP 주소를 할당하려면 어떻게 하나요?

기본적으로 컨테이너는 브리지 네트워킹을 사용하므로 호스트와 IP 주소를 공유합니다.

컨테이너에 별도의 IP 주소를 할당하려면 다음과 같이 macvlan 네트워크를 만들 수 있습니다.

```bash
docker network create -d macvlan \
    --subnet=192.168.0.0/24 \
    --gateway=192.168.0.1 \
    --ip-range=192.168.0.100/28 \
    -o parent=eth0 vlan
```

이 값을 사용 중인 로컬 네트워크 대역에 맞게 수정하세요.

네트워크를 만든 후 Compose 파일을 다음과 같이 변경하세요.

```yaml
services:
  windows:
    container_name: windows
    ..<snip>..
    networks:
      vlan:
        ipv4_address: 192.168.0.100

networks:
  vlan:
    external: true
```

이 방법의 추가 장점은 포트 매핑을 더 이상 설정하지 않아도 된다는 점입니다. 모든 포트가 기본적으로 노출됩니다.

> [!IMPORTANT]
> macvlan의 설계상 호스트와 컨테이너 간 통신이 허용되지 않으므로, 이 IP 주소는 Docker 호스트에서 접근할 수 없습니다. 문제가 된다면 [두 번째 macvlan을 만드는 방법](https://blog.oddbit.com/post/2018-03-12-using-docker-macvlan-networks/#host-access)을 우회 방법으로 사용할 수 있습니다.

### Windows가 라우터에서 IP 주소를 받아오게 하려면 어떻게 하나요?

컨테이너에 [macvlan을 설정](#컨테이너에-개별-ip-주소를-할당하려면-어떻게-하나요)한 후에는 Windows가 실제 PC처럼 라우터에 IP 주소를 요청해 홈 네트워크의 일부가 되도록 할 수 있습니다.

컨테이너와 Windows가 서로 다른 IP 주소를 사용하도록 하려면 Compose 파일에 다음 내용을 추가하세요.

```yaml
environment:
  DHCP: "Y"
devices:
  - /dev/vhost-net
device_cgroup_rules:
  - 'c *:* rwm'
```

### 디스크를 여러 개 추가하려면 어떻게 하나요?

추가 디스크를 만들려면 Compose 파일을 다음과 같이 수정하세요.

```yaml
environment:
  DISK2_SIZE: "32G"
  DISK3_SIZE: "64G"
volumes:
  - ./example2:/storage2
  - ./example3:/storage3
```

### 디스크를 직접 연결하려면 어떻게 하나요?

Compose 파일에 다음과 같이 지정해 디스크 장치 또는 파티션을 직접 연결할 수 있습니다.

```yaml
devices:
  - /dev/sdb:/disk1
  - /dev/sdc1:/disk2
```

`/disk1`은 기본 드라이브로 사용하려는 경우 지정하세요. 설치 중 포맷됩니다. `/disk2` 이상은 보조 드라이브로 추가되며 기존 내용이 유지됩니다.

### USB 장치를 연결하려면 어떻게 하나요?

먼저 `lsusb` 명령으로 USB 장치의 공급업체 ID와 제품 ID를 확인한 다음, Compose 파일에 다음과 같이 추가하세요.

```yaml
environment:
  ARGUMENTS: "-device usb-host,vendorid=0x1234,productid=0x1234"
devices:
  - /dev/bus/usb
```

> [!WARNING]
> Windows 설치가 완료되기 전에 USB 저장 장치를 연결하면 설치가 실패할 수 있습니다. 더 심각한 경우 해당 드라이브가 시스템 디스크로 포맷되어 모든 데이터가 사라질 수 있습니다. 따라서 컨테이너를 처음 실행할 때는 USB 저장 장치를 반드시 분리해 두세요.

### 동적 메모리 할당은 어떻게 활성화하나요?

기본적으로 가상 머신은 `RAM_SIZE`로 설정한 전체 메모리를 실행 중 내내 할당받습니다.

호스트의 메모리 압박 상태에 따라 게스트 Windows에서 사용하지 않는 RAM을 동적으로 회수하고 싶다면 [메모리 벌루닝](https://github.com/qemus/qemu/blob/master/docs/ballooning.md)을 활성화할 수 있습니다.

### 사용할 수 있는 설정이 이게 전부인가요?

아닙니다. 지원되는 모든 설정은 [환경 변수 문서](docs/environment.md)에서 확인할 수 있습니다.

### KVM을 사용할 수 있는지 어떻게 확인하나요?

먼저 사용 중인 플랫폼과 컨테이너 런타임이 위의 [요구 사항](#요구-사항-️)을 충족하는지 확인하세요.

Linux 호스트에서는 `cpu-checker`를 설치하고 다음 명령을 실행하세요.

```bash
sudo apt install cpu-checker
sudo kvm-ok
```

정상적으로 설정되어 있다면 다음 메시지가 표시됩니다.

```text
KVM acceleration can be used
```

KVM 장치가 존재하는지도 다음 명령으로 확인할 수 있습니다.

```bash
ls -l /dev/kvm
```

KVM을 사용할 수 없다면 다음 사항을 확인하세요.

- BIOS 또는 UEFI에서 하드웨어 가상화(`Intel VT-x` 또는 `AMD-V`)가 활성화되어 있는지
- 호스트 자체가 가상 머신인 경우 중첩 가상화가 활성화되어 있는지
- VPS 또는 클라우드 제공업체가 중첩 가상화를 지원하는지

`kvm-ok` 명령은 성공하지만 컨테이너에서 여전히 KVM을 사용할 수 없다고 표시된다면, 권한 또는 장치 접근 문제인지 확인하기 위해 일시적으로 Compose 설정에 `privileged: true`를 추가해 볼 수 있습니다.

### 컨테이너에서 macOS를 실행하려면 어떻게 하나요?

이 프로젝트와 비슷한 기능을 제공하는 [dockur/macos](https://github.com/dockur/macos)를 사용할 수 있습니다. 다만 자동 설치 기능은 제공하지 않습니다.

### 컨테이너에서 Linux 데스크톱을 실행하려면 어떻게 하나요?

이 경우에는 [qemus/qemu](https://github.com/qemus/qemu)를 사용할 수 있습니다.

### 이 프로젝트는 합법인가요?

네. 이 프로젝트에는 오픈 소스 코드만 포함되어 있으며 Windows 자체를 배포하지 않습니다. 코드에서 발견되는 제품 키는 Microsoft가 평가 목적으로 공개한 일반 설치 키이며, 정품 인증 라이선스로 사용할 수 없습니다.

유효한 Windows 라이선스를 보유하고 있는지 확인하고 Microsoft의 라이선스 조건을 준수하는 것은 사용자 책임입니다.

## 별표(Star) 🌟

[GitHub에서 별표를 준 사용자 보기](https://github.com/dockur/windows/stargazers)

## 면책 조항 ⚖️

이 프로젝트에서 언급한 제품 이름, 로고, 브랜드 및 기타 상표는 각 상표권자의 소유입니다. 이 프로젝트는 Microsoft Corporation과 제휴 관계가 없으며, Microsoft의 후원이나 보증을 받지 않습니다.

---

관련 링크:
- [프로젝트 저장소](https://github.com/dockur/windows/)
- [Docker Hub 이미지](https://hub.docker.com/r/dockurr/windows/)
- [Docker Hub 태그](https://hub.docker.com/r/dockurr/windows/tags)
- [GitHub 패키지](https://github.com/dockur/windows/pkgs/container/windows)
