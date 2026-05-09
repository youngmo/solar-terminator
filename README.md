# Solar Terminator — 실시간 3D 지구본 (Cesium)

Cesium 기반의 실시간 3D 지구본. 줌인하면 위성 타일이 동적으로 로드되어 도시·도로 수준까지 확대 가능. 주야 경계는 Cesium 내장 천체 위치 계산(Simon 1994)으로 표시된다.

## 기능

- **타일 기반 줌**: ESRI World Imagery를 baseLayer로 사용. 줌 레벨에 따라 해당 영역의 위성 타일이 자동으로 스트리밍된다. API 키 불필요.
- **실시간(LIVE) / 수동(MANUAL) 모드**:
  - LIVE: 매 프레임 `Cesium.JulianDate.fromDate(new Date())`로 시계를 갱신, 주야 경계가 실시간 추적
  - MANUAL: 슬라이더와 날짜 선택으로 임의 시각 시뮬레이션
- **태양 직하점 표시**: HUD에 위·경도 좌표 표시. 균시차(equation of time) 보정을 적용해 ±1~2분 정확도 (이전 Three.js 버전의 ±15분에서 개선).
- **내 위치로 이동**: 브라우저 Geolocation API로 좌표 받은 뒤 `viewer.camera.flyTo`로 2초간 부드럽게 이동, 해당 지점에 주황색 포인트 마커 배치.
- **카메라 컨트롤**: 좌버튼 드래그로 회전, 우버튼 드래그로 기울이기, 휠로 줌.

## 구성

- `index.html` — 단일 파일. Cesium은 외부 CDN에서 로드.
- 외부 의존성:
  - **Cesium 1.122.0** (`cdn.jsdelivr.net/npm/cesium@1.122.0`, ~2MB minified): 3D 지구본·타일·시간·조명 라이브러리
  - **ESRI World Imagery** (`services.arcgisonline.com`): 위성 타일 (TOS상 출처 표기 필요, Cesium이 자동 처리)
  - Google Fonts (Sora, JetBrains Mono)

## GitHub Pages 배포 절차

1. 새 저장소 생성 (예: `solar-terminator`)
2. `index.html`을 루트에 push
3. **Settings → Pages**에서 Source = `Deploy from a branch`, Branch = `main` (또는 `master`) / `(root)` 설정
4. 1~2분 후 `https://<유저명>.github.io/<저장소명>/` 에서 접속

루트 도메인(`https://<유저명>.github.io/`)에 두려면 저장소 이름을 `<유저명>.github.io`로.

## 환경 요구사항

- WebGL2 지원 브라우저 (모든 주요 모던 브라우저, 2020년 이후 모바일 포함)
- Geolocation 기능은 HTTPS 또는 localhost에서만 동작. GitHub Pages는 기본 HTTPS이므로 문제없음.
- 첫 로드 시 Cesium 본체 ~2MB 다운로드. 이후는 브라우저 캐시.

## 시간·태양 위치 정밀도

| 항목 | Three.js 버전 (이전) | Cesium 버전 (현재) |
|---|---|---|
| 태양 위치 (조명/터미네이터) | 단순 사인 근사, ±15분 | Simon 1994 천체 위치, 천문학적 정밀도 |
| HUD 직하점 좌표 | 단순 사인, EoT 미보정 | 단순 사인 + EoT 보정, ±1-2분 |
| 일적위 (declination) | ±0.5° | 정밀 |
| 줌 한계 | 텍스처 픽셀 깨짐 | 도시·도로 수준까지 가능 |

HUD 좌표만 단순 공식을 유지한 이유는 Cesium의 내부 천체 모듈에서 직하점 lat/lon을 직접 추출하려면 ICRF↔ECEF 변환 데이터(SPICE 비슷한)를 비동기 로드해야 하므로 코드 복잡도가 늘기 때문이다. 시각적 터미네이터는 정확하므로, HUD 표시는 ±1-2분으로 충분하다고 판단했다.

## 라이선스 및 출처

- 코드: MIT 등으로 공개해도 무방 (사용자 결정)
- Cesium: Apache 2.0 라이선스
- ESRI World Imagery: ESRI 사용 약관 준수, 출처 표기는 Cesium의 credit 영역에 자동 표시됨

## Three.js 버전과의 차이

이전 Three.js 기반 버전 (`index-threejs-backup.html`로 보존)은 학습용·교육용으로 의미가 있다 (셰이더에서 직접 dot product로 주야 계산하는 과정이 코드에 명시적). 본격적인 줌과 천문학적 정밀도가 필요하면 Cesium 버전이 적합하다.
