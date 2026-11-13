# Allen CCF Slicer

[Allen CCF Explorer](https://sjleen.github.io/allen_ccf_tools/explorer.html)를 바탕으로, coronal 단면을 비스듬히 자를 수 있게 만든 도구입니다. 내 histology 절편이 어떤 각도로 잘렸는지 찾고, 실제 절편 두께 간격으로 section을 한 장씩 넘겨 볼 수 있습니다.

웹(https://sjleen.github.io/allen_ccf_tools/slicer.html)에서 바로 열거나, html 파일과 `ccf_mesh_data.js`를 같은 폴더에 두고 html을 열면 됩니다.

3D 뷰, ontology 목록, flat skull 보정, 내보내기, session은 Explorer와 똑같이 동작합니다. **두 입력칸은 일부러 비워 둔 채로 시작하며, 가장 먼저 채워야 합니다:** bregma AP / DV 보정(**Coordinates** 탭, 보정이 없으면 0 입력. 채우기 전에는 track을 추가하거나 가져올 수 없습니다)과 절편 **Thickness**(**Slicing** 탭. 채우기 전에는 section 번호, section 이동, **Series → CSV**, track fit이 모두 꺼져 있습니다). Explorer에 더해 왼쪽에 **Slicing** 탭, 오른쪽 패널에 **Track fit**(Explorer의 **Points** 자리이며, **Points**와 **Path filter** 탭은 여기서 숨겨져 있습니다)이 있고, **Coronal** 뷰가 기울어진 절단면을 따라갑니다.

## 왜 필요한가

뇌를 coronal 면에 완벽히 직각으로 블록하는 일은 거의 없습니다. 작은 pitch(등쪽 가장자리가 배쪽보다 앞으로 나감)나 yaw(한쪽 반구가 다른 쪽보다 앞으로 나감)만 있어도, 모든 절편이 표준 coronal 아틀라스와 "어긋나" 보입니다. 이 앱은 이 두 각도를 찾아서, 내 절편이 실제로 잘린 방향 그대로 아틀라스를 잘라 보여 줍니다.

## 좌표 계산 방식 (항상 두 단계)

1. **CCF → flat skull** (**Coordinates** 탭): Explorer의 보정 그대로입니다. bregma 기준으로 축마다 스케일을 맞춘 뒤 뇌 전체를 기울입니다. 결과는 bregma 기준 stereotaxic ML / AP / DV입니다.
2. **Flat skull → 절단면** (**Slicing** 탭): 그 좌표계 안에서 절단면은 `AP = A + tan(yaw)·ML + tan(pitch)·(DV − DVref)`입니다. section *k*는 정해진 두께의 판이고, 이웃 section과는 면에 수직 방향으로 정확히 두께 한 장만큼 떨어져 있습니다. section 번호는 **#0**부터 AP와 같은 부호로 매깁니다: **+ = 앞쪽, − = 뒤쪽** (*#0 center AP*로 #0의 위치를 정합니다).

기울어지는 것은 **Coronal** 뷰뿐입니다. **Sagittal**과 **Horizontal**은 축에 맞춘 그대로이며, 현재 section(주황)과 이웃 section들을 선으로 표시합니다. 3D 뷰에서는 절단면이 주황색 판으로 보입니다.

## 각도 찾기

**직접 맞추기**: **Slicing** 탭에서 pitch와 yaw를 돌려 가며 **Coronal** 뷰를 내 절편과 비교합니다.

**track으로 맞추기**: 오른쪽 패널의 표에 track을 한 줄씩 입력합니다. 새 track은 좌표가 빈칸으로 시작하며, ML / AP / DV가 다 채워져야 계산에 들어갑니다. 주입 또는 probe track마다 다음을 입력합니다.

- 수술 때 쓴 좌표 (flat skull 기준 ML / AP / DV, 각도는 선택);
- *Span*: 뇌 표면이 보이는 section부터 tip이 보이는 section까지, 양 끝을 포함해 track이 걸친 장수 (1 = track 전체가 한 장 안에 있음);
- *Deeper goes*: 깊이 들어갈수록 track이 뒤쪽 section으로 가는지 앞쪽 section으로 가는지;
- *Tip offset* (선택): 이 tip이 다른 tip들에 비해 몇 장 떨어져 있는지. 부호는 AP와 같습니다: **+ = 더 앞쪽, − = 더 뒤쪽** (−4 = 네 장 뒤). tip 하나를 0으로 정하면 되고, 첫 track의 offset이 비어 있으면 0으로 칩니다. 절단면이 fit을 따라가면 offset 0인 tip이 section #0에 놓이므로, section 번호가 그대로 offset처럼 읽힙니다 (span 11이고 깊어질수록 뒤로 가는 track은 표면 #+10부터 tip #0까지 걸칩니다).

표의 마지막 두 열은 그 track 하나만으로 계산한 pitch와 yaw입니다 (pitch는 span과 방향에서, yaw는 기준 tip과의 tip offset에서 나옵니다. 기준 track과 ML이 거의 같은 track은 "—"로 표시됩니다). 그래서 다른 track들과 안 맞는 track이 바로 눈에 띕니다. span과 방향만으로 **pitch**가 정해집니다. ML이 서로 다른 track에 tip offset이 있으면 **yaw**(와 section #0의 위치)도 정해지며, 없으면 이 값들은 현재 값으로 유지됩니다. 모든 track을 하나의 최소제곱 fit에 넣습니다. 결과 표에는 track마다 입력한 값과 fit된 면이 예측한 값이 나란히 나오고, 한 장 넘게 어긋난 행은 강조됩니다.

**절단면은 fit을 자동으로 따라갑니다.** 모든 track에 ML / AP / DV와 span이 채워지면 pitch, yaw, #0 위치가 **Slicing** 탭에 들어가고, 화면은 보고 있던 section 번호를 그대로 유지합니다. 각도, *Reset angles*, *#0 center AP*를 손으로 바꾸면 **manual**로 바뀌어 더 이상 따라가지 않습니다. **Follow fit**을 누르면 다시 따라가기 시작하며, 첫 track의 tip이 있는 section으로 이동합니다. 이 상태는 session에 저장됩니다 (이 기능 전에 저장한 session은 manual로 열립니다).

**Also fit effective section thickness**는 실제 한 장의 두께도 미지수로 함께 구합니다. 수축이나 압착 때문에 실제 두께가 vibratome 설정값과 다를 때 씁니다. 서로 다른 section 번호에서 본 정보가 필요하며 (두 개 이상의 track에 tip offset, 또는 span), 절단면이 fit을 따라갈 때 구한 두께가 **Thickness** 칸을 대신합니다. track이 한두 개뿐이면 끄세요. 두께가 각도 오차를 대신 흡수할 수 있습니다.

**Zeroing error**(표 위의 ML / AP / DV)는 모든 track을 한꺼번에 옮깁니다. 수술 때 bregma나 정중선을 조금 잘못 잡았을 때, track을 하나하나 고치는 대신 AP에 0.1만 넣으면 됩니다. track은 "입력값 + 이 값" 위치에 그려지고 fit됩니다. 입력한 값과 track CSV는 그대로이며, 이 값은 session에 저장됩니다. 부호는 stereotaxic 그대로입니다 (+ = 오른쪽 / 앞쪽 / 등쪽). **Target DV**가 뇌 표면 기준 깊이일 때는 DV를 쓰지 않습니다.

**Import CSV**는 Explorer에서 내보낸 point 목록(`label,ml,ap,dv,color`)도 읽습니다. label은 track 이름이 되고, 색은 유지되며, **Target DV**는 "from bregma"로 바뀝니다. 비어 있는 ML / AP / DV 칸은 빈칸 그대로 들어옵니다. Explorer CSV에는 각도가 없으므로, 비스듬한 track은 각도를 직접 넣어 주세요.

정밀도는 장수를 정수로 세는 데서 정해집니다. 3 mm track과 65 µm 절편이면 span 한 장 차이가 pitch 약 1.2°에 해당하므로, track이 많을수록 평균이 좋아집니다.

**Coronal** 뷰에서 각 track은 점선(투영)으로 그려지고, 현재 section 안에 실제로 들어 있는 부분만 실선으로 그려집니다. tip 번호가 주어지면 표면과 tip 지점이 해당 section에서 흰 원으로 표시됩니다. 3D 뷰에서는 각 track이 뇌 표면 진입점부터 tip까지의 막대로, tip에는 공이 달린 모양으로 track 색으로 그려집니다.

## section 넘겨 보기

- **Slicing** 탭이나 **Coronal** 뷰 아래의 ◀ ▶, 또는 ← → (Shift = 다섯 장씩).
- *Jump to AP*는 가장 가까운 section으로 갑니다. **Series → CSV**는 모든 section의 중심 AP와 등쪽, 배쪽, 왼쪽, 오른쪽 가장자리의 AP를 표로 내보냅니다.
- **Coronal** 슬라이더는 여전히 자유롭게 움직입니다. section 위에 있으면 헤더에 `#k`, 사이에 있으면 `≈#k`로 표시됩니다.

## 파일

- `allen_ccf_slicer_v1_0.html`: 앱 (Explorer v1.0.1 + slicing).
- `ccf_mesh_data.js`: Explorer와 같은 mesh 데이터 파일입니다. html 옆에 두세요.

session은 `ccf-slicer-session-<시각>_<메모>.json`(이미지는 `ccf-slicer_*.png/svg`)으로 저장되어 Explorer 파일과 헷갈리지 않습니다. Explorer 형식에 `slicer` 항목이 더해진 것이라 Explorer session도 여기서 열립니다.

## 주의와 한계

- fit은 track의 stereotaxic 위치가 정확한 만큼만 정확합니다. 빗나간 track은 큰 잔차로 드러납니다.
- 2D SVG 내보내기에는 track이 그려지지 않습니다 (PNG에는 들어갑니다).
- 조직 수축은 모델에 넣지 않았습니다. 선택 기능인 두께 fit으로만 반영됩니다.

## 데이터 출처와 인용

아틀라스 데이터: **Allen Institute**, Allen Mouse Brain Common Coordinate Framework (CCFv3, 2017), [portal.brain-map.org](https://portal.brain-map.org). [Allen Institute 이용약관](https://alleninstitute.org/legal/terms-use/)에 따라 사용합니다. 인용은 [인용 정책](https://alleninstitute.org/legal/citation-policy)을 따라 주세요. 3D 렌더링은 [three.js](https://threejs.org) (MIT)를 씁니다.

---

만든이: [sjleen](https://github.com/sjleen).
