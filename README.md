# Solar Terminator — 실시간 3D 지구본

현재 시각 기준으로 태양빛이 닿는 지구 영역을 보여주는 단일 HTML 파일.
JavaScript는 사용자 브라우저에서 실행되므로 정적 호스팅만 있으면 동작한다.

## 기능

- 실시간(LIVE) 모드: 현재 UTC 시각의 태양 직하점을 매 프레임 다시 계산
- 수동(MANUAL) 모드: 날짜 선택과 시간 슬라이더로 임의의 시각 시뮬레이션 가능
  - 슬라이더는 1분 단위, 0~1439분 (UTC 0시 0분 ~ 23시 59분)
  - 날짜 선택은 HTML5 native date picker
  - `NOW` 버튼으로 LIVE 모드 복귀
- 마우스 드래그로 회전, 휠로 확대/축소

## 구성

- `index.html` — 단일 파일. Three.js와 텍스처는 외부 CDN에서 로드.
- 외부 의존성:
  - Three.js r128 (`cdn.jsdelivr.net`)
  - 지구 텍스처 (`unpkg.com`, three-globe 패키지의 NASA Blue Marble 및 Black Marble 이미지)
  - Google Fonts (Sora, JetBrains Mono)

## GitHub Pages 배포 절차

1. 새 저장소를 만든다 (예: `solar-terminator`).
2. `index.html`을 저장소 루트에 추가하고 push.
3. 저장소의 **Settings → Pages**로 이동.
4. Source를 `Deploy from a branch`, Branch를 `main` (또는 `master`) / `(root)`로 설정.
5. 1~2분 후 `https://<username>.github.io/<repo>/`에서 접속 가능.

루트 도메인(`https://<username>.github.io/`)에 두려면 저장소 이름을 `<username>.github.io`로 만들면 된다.

## 계산식과 한계

태양 직하점(subsolar point) 위·경도는 다음과 같이 근사한다.

- 일적위(declination) δ ≈ 23.44° · sin(2π · (N − 81) / 365), N은 일년 중 일수
- 직하점 경도 λ_sun = (12 − UTC시각[hr]) · 15°

균시차(equation of time) 보정을 적용하지 않았기 때문에 시기에 따라 ±15분(약 ±3.75°) 오차가 발생한다. 천문학적 정밀도가 필요하면 [NOAA Solar Position Calculator](https://gml.noaa.gov/grad/solcalc/) 또는 [USNO 데이터](https://aa.usno.navy.mil/)와 대조해야 한다.

## 좌표 매핑 메모

Three.js의 `SphereGeometry` 기본 UV는 다음과 같이 배치된다:

| 경도 | 3D 좌표 |
|---|---|
| 0° (Greenwich) | +X |
| 90°E | −Z |
| 180° | −X |
| 90°W | +Z |

따라서 (lat, lon) → 단위 벡터 변환은:

```
x =  cos(lat) · cos(lon)
y =  sin(lat)
z = −cos(lat) · sin(lon)
```

이 매핑이 어긋나면 조명 반구가 텍스처와 90° 회전한다.

## 텍스처 출처

- 낮(Day): NASA Earth Observatory Blue Marble — three-globe 예제 자산을 통해 unpkg에서 로드.
- 밤(Night): NASA Black Marble (Suomi NPP VIIRS, Miguel Román 외 / NASA GSFC) — 동일 경로.

CDN의 가용성이나 CORS 정책이 변경되면 자동으로 절차적 셰이더 폴백이 작동한다 (대륙 모양은 사라지고 노이즈 기반 가짜 지형이 표시되지만, 주야 경계 계산은 그대로 유지된다).

## 라이선스

코드는 MIT로 공개해도 무방하다. 다만 NASA의 Blue Marble / Black Marble 이미지는 별도의 NASA media usage guidelines를 따르므로, 텍스처를 직접 호스팅하려는 경우 출처 표기와 약관 확인이 필요하다.
