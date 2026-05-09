# Solar Terminator — 실시간 3D 지구본 (Three.js, v0.6.0)

Three.js 기반의 실시간 3D 지구본. 현재 시각 기준으로 태양빛이 닿는 지구 영역과 어둠 영역을 보여준다.

## 버전 이력 요약

- **v0.6.0** (현재): Three.js 기반, 4K 주간 텍스처. v0.3.0에서 검증된 베이스에 텍스처 해상도만 업그레이드.
- v0.4.0–v0.5.2 (폐기): Cesium으로 재작성을 시도했으나 사용자 환경에서 카메라 드리프트, 성능 저하 등 다섯 차례에 걸친 시도에도 안정화되지 않아 롤백.
- v0.3.0: Three.js + 슬라이더 + 위치 기능. 사용자가 GitHub Pages에서 정상 동작 확인한 버전.

## 기능

- **실시간(LIVE) / 수동(MANUAL) 모드**:
  - LIVE: 매 프레임 현재 UTC 시각으로 태양 직하점 재계산
  - MANUAL: 슬라이더와 날짜 선택으로 임의 시각 시뮬레이션
- **태양 직하점 표시**: 위·경도 좌표를 HUD에 표시 (단순 사인 근사, ±15분 오차)
- **내 위치로 이동**: 브라우저 Geolocation API 권한 요청 후 카메라가 해당 좌표 위로 slerp 이동, 박동하는 마커 배치
- **마우스 드래그로 회전, 휠로 확대/축소**

## 줌 한계

4K 텍스처(4096×2048)이므로:
- 지구 전체 보기: 매우 선명
- 대륙 단위: 선명
- 비행기 고도(~1만m): 선명
- 도시 거리·도로 수준: 픽셀화됨 — 이 수준의 줌은 타일 스트리밍이 필요한데, 그건 Cesium 같은 전문 라이브러리 영역. v0.4–v0.5에서 시도했다가 환경 호환 문제로 롤백한 이력이 있다.

## 구성

- `index.html` — 단일 파일. Three.js와 텍스처는 외부 CDN에서 로드.
- 외부 의존성:
  - **Three.js r128** (`cdn.jsdelivr.net/npm/three@0.128.0`)
  - **Earth 텍스처** (`cdn.jsdelivr.net/gh/mrdoob/three.js@r128/examples/textures/planets/`):
    - `earth_atmos_4096.jpg` — 주간 (697KB)
    - `earth_lights_2048.png` — 야간 도시 빛 (718KB, 2K만 존재)
  - Google Fonts (Sora, JetBrains Mono)
- 텍스처 로드 실패 시 자동 폴백 체인: jsdelivr 4K → jsdelivr 2K → unpkg three-globe → 절차적 셰이더(노이즈 기반 가짜 지형)

## 정밀도와 한계

- 일적위(declination) δ ≈ 23.44° · sin(2π · (N − 81) / 365) — ±0.5° 오차
- 직하점 경도 λ = (12 − UTC시각) · 15° — 균시차(EoT) 미보정으로 ±15분 오차
- 정밀한 천문 계산이 필요하면 [NOAA Solar Position Calculator](https://gml.noaa.gov/grad/solcalc/) 등과 대조

## GitHub Pages 배포

1. 새 저장소 생성 (예: `solar-terminator`)
2. `index.html`을 루트에 push
3. **Settings → Pages**에서 Source = `Deploy from a branch`, Branch = `main` / `(root)` 설정
4. 1~2분 후 `https://<유저명>.github.io/<저장소명>/` 에서 접속

루트 도메인은 저장소 이름을 `<유저명>.github.io`로.

## 환경 요구사항

- WebGL 지원 브라우저 (모든 주요 모던 브라우저)
- Geolocation 기능은 HTTPS 또는 localhost 필요. GitHub Pages는 기본 HTTPS이므로 OK.

## 라이선스 및 출처

- 코드: MIT 등으로 공개해도 무방
- 지구 텍스처: NASA Blue Marble (Earth Observatory) / NASA Black Marble (Suomi NPP VIIRS, Miguel Román 외 / NASA GSFC)
