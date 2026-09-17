# EDA Playground 사용법 — VHDL 실행하기

[교재 회로로 배우는 HDL](../../Chapters/교재_회로로_배우는_HDL.md)의 VHDL 코드를 브라우저에서 직접 실행합니다. 설치할 것은 없고 구글 계정 로그인만 필요합니다.

- 주소: <https://edaplayground.com>
- 처음 사용하는 회차: **4회차**

---

## 1. 설정 — 처음 한 번만

화면 왼쪽 패널에서 네 가지를 맞춥니다. **기본값이 Verilog용이라 그대로 두면 실행되지 않습니다.**

| 항목 | 기본값 | 바꿀 값 |
|---|---|---|
| **Testbench + Design** | SystemVerilog/Verilog | **VHDL** |
| **Tools & Simulators** | Select… | **GHDL** (버전은 목록의 최신 것) |
| **Open EPWave after run** | 꺼짐 | **켬** |
| **Top entity** | 비어 있음 | **테스트벤치 이름** (예: `tb_kmap_demo`) |

**확인** — Tools & Simulators 칸에 `GHDL …`이 표시되고 Open EPWave에 체크가 있으면 됩니다.

- Tools & Simulators 목록에 GHDL이 없으면 → 언어를 아직 바꾸지 않은 것입니다. 표의 첫 줄로 돌아가 **Testbench + Design을 VHDL로** 고치십시오. 목록은 언어에 따라 바뀝니다.

**Import · Make · Run · Simulator Options 네 칸은 모두 비워 둡니다.** Libraries·VUnit 등 나머지 항목도 손대지 않습니다 — 이 과목에서 쓰지 않습니다.

---

## 2. 코드 넣기

화면 가운데가 **Testbench**, 오른쪽이 **Design**입니다.

### 2-1. 먼저 기본 주석을 지웁니다

새로 열면 두 창에 아래 같은 줄이 들어 있습니다.

```
// Code your design here
// Code your testbench here
// or browse Examples
```

`//`는 **Verilog 주석**이고 VHDL은 `--`를 씁니다. 이 줄이 남아 있으면 첫 줄에서 바로 실패합니다.

**조치** — 두 창에서 `//`로 시작하는 줄을 **모두 지웁니다.**

### 2-2. 코드를 붙여넣습니다

| 창 | 넣을 것 |
|---|---|
| **Design** (오른쪽) | HDL 자료의 **Design** 코드 |
| **Testbench** (가운데) | HDL 자료의 **Testbench** 코드 |

두 창은 역할이 다릅니다. **Design은 만들려는 회로 그 자체**이고, **Testbench는 그 회로를 시험하는 장치**입니다. Logisim에서 입력 핀을 손으로 토글하던 동작이 Testbench 코드에 적혀 있습니다.

**확인** — Top entity 칸의 이름이 Testbench 코드의 `entity tb_…` 이름과 같은지 확인합니다.

- 다르면 → Top entity 칸을 코드에 맞춰 고칩니다.

---

## 3. 실행

위쪽 **Run** 버튼을 누릅니다. 아래에 Log 탭이 나타납니다.

**확인 1 — Log**

성공하면 마지막 줄이 `Done`이고 그 위에 오류 줄이 없습니다.

- `missing entity, architecture, package or configuration`이 나오고 그 아래에 `//`로 시작하는 줄이 함께 표시되면 → **2-1로 돌아가 기본 주석을 지우고** 다시 Run 합니다.
- 그 밖의 오류에는 **파일 이름과 줄 번호**가 함께 표시됩니다(예: `design.vhd:12:5`). 그 줄을 HDL 자료의 코드와 한 글자씩 대조하십시오. 세미콜론 누락과 `end` 뒤의 이름이 가장 흔합니다.

**확인 2 — 파형**

Run이 성공하면 EPWave 창이 열립니다.

- 열리지 않으면 → 1번의 **Open EPWave after run** 체크를 확인하고 다시 Run 합니다.

---

## 4. 파형 읽기

- 가로축은 시간이며 단위는 **fs**(펨토초)입니다. `10,000,000 fs = 10 ns`입니다.
- Testbench의 `wait for 10 ns;` 한 줄이 파형의 **계단 한 칸**을 만듭니다.
- 신호가 보이지 않으면 **Get Signals**를 눌러 추가합니다.

**확인** — 출력 신호가 HDL 자료의 「실행 결과 확인」 표와 구간마다 일치하는지 확인합니다. 일치하면 그 코드는 설계한 대로 동작하는 것입니다.

---

## 5. 저장 (선택)

오른쪽 위 **Save**를 누르면 이 코드에 주소가 생깁니다. 복습할 때 그 주소로 다시 열 수 있습니다.
