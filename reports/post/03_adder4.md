# 실험 후 레포트: 4비트 가산기

작성일 2026-09-19.

[실험 전 레포트](../../reports/pre/03_adder4.md) · [해시·입력 기록](../../build/sim/result.json)

## Vivado GUI 과정과 사전 결과 비교

공개 템플릿 `86c15c5`를 새로 clone한 폴더에서 Vivado 2026.1 GUI의 New Project를 사용했습니다. 부품은 `xc7s75fgga484-1`, 설계 top을 `adder_4bit`, 시뮬레이션 top을 `tb_adder_4bit`로 지정하여 프로젝트를 구성했습니다. `src/adder_4bit.v`와 `sim/tb_adder_4bit.sv`를 임포트하여 사전 결과와의 정합성을 대조했습니다

Run Simulation → Run Behavioral Simulation에서 [실제 GUI 시뮬레이션 로그](../../evidence/03/vivado/simulation.log)의 `LAB1_PASS adder_4bit cases=256`와 2560ns 종료를 확인했습니다. 두 4비트 피연산자의 합과 비트 확장을 통한 올림수({cout, s} = {1'b0, a} + {1'b0, b})가 256개 전수 벡터에서 사전 파형과 완전히 일치했습니다.

## 합성·구현·bit

Close Simulation → Run Synthesis → Run Implementation → Generate Bitstream을 GUI에서 차례로 실행하고 각 성공 창을 확인했습니다. [GUI 빌드 로그](../../evidence/03/vivado/build.log)를 보관했습니다.

생성 파일은 `vivado/adder_4bit.runs/impl_1/adder_4bit.bit`, 크기는 3,687,013바이트입니다. 배포 [adder_4bit.bit](../../vivado/adder_4bit.runs/impl_1/adder_4bit.bit)의 SHA-256은 `1345EE26DDB6836B8B0185B7298212A9691B84EE2755F49926D24CC7705F9AA0`입니다.

오류 및 경고 여부는 실험 시에 기록해두지 못했습니다. 다음 실험 부터 기록하겠습니다.

## 보드 기록·촬영 상태

Hardware Manager를 통해 비트스트림을 보드에 프로그래밍했습니다. DIP1\~4를 a[3:0], DIP5~8을 b[3:0]에 매핑하고, LED1(cout) 및 LED2\~5(s[3:0])의 출력을 관측했습니다.

| 조건(a+b) | 시뮬레이션 cout,s | 실측 cout,s | 사진 |
|---|---|---|---|
| 0+0 | 0,0000 | 0,0000 | [0+0](../../evidence/03/board/photos/input-0+0.jpg) |
| 1+15 | 1,0000 | 1,0000 | [1+15](../../evidence/03/board/photos/input-1+15.jpg) |
| 15+15 | 1,1110 | 1,1110 | [111](../../evidence/03/board/photos/input-15+15.jpg) |
[LED 동장 영상](../../evidence/03/board/videos/demo.mp4)

## 결론

총 256가지의 모든 4비트 덧셈 조합에 대해 이론적 연산 모델과 Vivado GUI 시뮬레이션 결과가 일치했습니다. 특히 1+15=16 시 최상위 올림수 $cout$이 1로 전이되는 오버플로 처리와 최댓값 15+15=30 조건이 보드 LED 상에서도 정확히 점등됨을 검증했습니다. 
