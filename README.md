<div align="center">

# King of Tank

**OpenGL 기반 3D 탱크 전투 게임 · 컴퓨터 그래픽스 텀 프로젝트 (2024)**

![C++](https://img.shields.io/badge/C%2B%2B-20-00599C?logo=cplusplus&logoColor=white)
![OpenGL](https://img.shields.io/badge/OpenGL-Rendering-5586A4?logo=opengl&logoColor=white)
![Platform](https://img.shields.io/badge/Platform-Windows-0078D6?logo=windows&logoColor=white)
![Build](https://img.shields.io/badge/Build-Debug%2FRelease_x64-2ea44f)

</div>

## 프로젝트 소개

King of Tank는 플레이어가 탱크를 조작해 제한된 3D 전장에서 적 탱크 및 환경 오브젝트와 교전하는 OpenGL 게임 프로젝트입니다.

FreeGLUT 기반의 입력·게임 루프 위에 Scene, Object, Mesh, Renderer 책임을 나누고, OBJ 모델 로딩, 카메라 추적, 포탄, 충돌 판정, 미니맵, 조명과 텍스처 렌더링을 구현했습니다.

> **현재 상태**
>
> `Debug|x64`, `Release|x64` 구성은 vcpkg 의존성을 복원한 뒤 빌드할 수 있습니다. 기존 루트에 흩어져 있던 소스와 에셋은 `src`, `assets` 폴더로 정리되어 있습니다.

## 주요 기능

| 영역 | 구현 내용 |
| --- | --- |
| 전투 | 플레이어·적 탱크, 포탑 회전, 포탄 생성과 이동 |
| Scene | 동적 오브젝트, 정적 오브젝트, 포탄의 생성·업데이트·제거 관리 |
| 입력 | 키보드·마우스·휠 입력과 FreeGLUT 이벤트 콜백 |
| Camera | 플레이어 탱크를 추적하는 3인칭 카메라 |
| Collision | Mesh AABB 기반 오브젝트·벽·포탄 충돌 및 낙하 상태 판정 |
| Rendering | OBJ Mesh, 텍스처, GLSL 셰이더, 다중 렌더링 경로 |
| Lighting | Directional Light 및 Phong 조명 모델 |
| UI | 플레이어·적 위치를 표시하는 미니맵 토글 |

## 실행 흐름

~~~mermaid
flowchart LR
    I["Keyboard / Mouse"] --> G["FreeGLUT Callbacks"]
    G --> C["Player Controller"]
    C --> S["KOT Scene"]
    S --> O["Tank / Enemy / Bullet"]
    O --> X["Collision Checker"]
    S --> R["Renderer"]
    R --> GL["OpenGL"]
    A["OBJ / Texture / GLSL"] --> O
    A --> R
~~~

- **FreeGLUT Callbacks**가 입력과 프레임 갱신 요청을 받습니다.
- **KOT Scene**은 탱크, 적, 정적 월드, 포탄의 수명과 업데이트 순서를 관리합니다.
- **Collision Checker**는 이동·낙하·포탄 충돌을 검사해 오브젝트 상태를 갱신합니다.
- **Renderer**는 Scene의 Mesh와 Shader를 OpenGL 버퍼 및 Draw 호출로 연결합니다.

## 프로젝트 구조

~~~text
King-of-Tank
├── assets
│   ├── models                 # 탱크·환경·2D OBJ 모델
│   ├── shaders                # Vertex / Fragment 셰이더
│   └── textures               # 위장·바닥·벽 텍스처
├── src
│   ├── KOT_Scene.*            # 게임 전용 Scene 구성
│   ├── Tank.*, Enemy.*        # 탱크와 적 행동
│   ├── Bullet.*               # 포탄 생성과 갱신
│   ├── Collision.*            # AABB 충돌 표현
│   ├── Object.*, Mesh.*       # 게임 오브젝트와 메시
│   ├── Camera.*, Projection.* # 카메라와 변환 행렬
│   ├── Renderer.ixx           # 렌더러 C++ 모듈
│   ├── ObjectLoader.ixx       # OBJ 로더 C++ 모듈
│   └── CollisionChecker.ixx   # 충돌 검사 C++ 모듈
├── KOT.sln
├── KOT.vcxproj
└── vcpkg.json                 # 빌드 의존성 선언
~~~

## 기술 스택

| 구분 | 기술 |
| --- | --- |
| Language | C++20 및 C++ Modules |
| Graphics API | OpenGL |
| Window / Input | FreeGLUT |
| OpenGL Loader | GLEW |
| Math | GLM |
| Image Loading | stb_image |
| Build | Visual Studio, MSVC, vcpkg |

## 시작하기

### 요구 사항

- Windows
- Visual Studio 2022 이상과 **C++을 이용한 게임 개발** 또는 **C++ 데스크톱 개발** 워크로드
- vcpkg
- OpenGL을 지원하는 GPU와 드라이버

### 의존성 복원

저장소는 `vcpkg.json`으로 다음 의존성을 관리합니다.

- `freeglut`
- `glew`
- `glm`

저장소 루트에서 다음을 실행합니다.

~~~powershell
git clone <repository-url>
cd King-of-Tank
vcpkg install --triplet x64-windows
~~~

명령은 프로젝트의 `vcpkg_installed/x64-windows`에 헤더와 라이브러리를 복원합니다. 이 폴더는 로컬 빌드 산출물이므로 Git에 포함하지 않습니다.

### 빌드 및 실행

1. `KOT.sln`을 Visual Studio에서 엽니다.
2. 솔루션 플랫폼을 `x64`로, 구성을 `Debug` 또는 `Release`로 선택합니다.
3. `KOT` 프로젝트를 시작 프로젝트로 설정합니다.
4. 빌드한 뒤 로컬 Windows 디버거로 실행합니다.

프로젝트 파일은 Debug에서 `glew32d`, `freeglutd`를, Release에서 `glew32`, `freeglut`을 연결하고 실행 파일 폴더로 필요한 DLL을 복사합니다.

## 에셋 안내

런타임 에셋은 모두 `assets` 아래에 위치합니다.

| 경로 | 내용 |
| --- | --- |
| `assets/models` | 탱크, 포탄, 나무, 환경 및 미니맵용 OBJ 모델 |
| `assets/shaders` | 모델·텍스처·충돌·광원 렌더링용 GLSL 셰이더 |
| `assets/textures` | 위장, 바닥, 벽 텍스처 |

OBJ 파일은 컴파일 과정에서 생성되는 `.obj`가 아니라 런타임에 읽는 Wavefront 모델 에셋입니다. 따라서 소스와 함께 저장소에 포함됩니다.

## 검증 범위

- vcpkg로 `freeglut`, `glew`, `glm` 복원
- `Debug|x64`, `Release|x64` 빌드 확인
- 실제 플레이 밸런스와 GPU·드라이버별 그래픽 결과는 별도 확인 필요

## 팀 구성

| 담당 | 역할 |
| --- | --- |
| 유석진 | 게임 루프, 입력·이벤트 처리, Scene, 탱크·적·포탄, AI, 충돌 감지, Object / Mesh, OBJ 로더, 렌더러, 카메라·미니맵, 월드 구성 |
| 양주호 | 머티리얼, Directional Light·Phong 라이팅, 텍스처 적용 |
