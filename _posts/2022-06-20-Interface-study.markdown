
## PCIe 4.0

> PCI Express 에 대해 알아본다.
> https://www.trentonsystems.com/blog/pcie-gen4-vs-gen3-slots-speeds 의 내용을 정리한다.
>

PCIe 3.0 에서 PCIe 4.0으로 업그레이드 하는 것에 대한 효율을 분석한다

### PCI express 란

Pheripheral Component Interconnect Express 일명 `PCIe` 는 메인보드와 주변장치들 사이의 고속 인터페이스 표준이다. 주변장치로는 GPU, LAN 포트, SSD, USB 등을 말한다.

PCIe 인터페이스를 배치하는 것 만으로 기존의 GPU, network, Storage 설정으로 메인보드에서 사용할 수 있다.

Intel, IBM, Dell, HP, AMD 그리고 NVIDIA가 소속된 Pheripheral Component Interconnect Special Interest Group 일명 `PCI-SIG`에서 PCI 1.0을 2003년에 발표했다.

PCIe 2.0과 3.0 이 각각 2007, 2010 년에 발표되었고 PCIe 4.0 이 2017 년, 그리고 가장 최근인 `2019년에 PCIe 5.0` 표준이 발표되었다.

PCIe 인터페이스를 사용하기 위한 PCIe 슬롯은 메인보드의 칩셋에 따라 달리한다.


https://ee.kaist.ac.kr/research-division-01/

카이스트 전기전자 공학부에서 활발히 연구되는 분야에 대해 알아보자



그저 소프트웨어 & AI 이거 밖에 없는건가

그렇다면 AI 엔지니어 중에 특별한 것을 해야될까

근데 나에게는 컴퓨터가 없는걸

-> 근데 AI 하는 사람이 컴퓨터 없이 하는게 말이되나

-> 데스크톱 하나 마련해야되나

근데 천만원은 아무나 쓸 수 있는 돈이 아니잖아

-> 그러면 연구실 들어가는 방법 밖에 없나

## https://www.koreascience.or.kr/article/JAKO201851648109474.pdf
2018년에 작성된 동향이다.

최근 이동통신 분야
- 시스템의 기능과 서비스 환경을 개방
- 개방형 정합 (API) 기술 개발

- 이동통신 가입자에게 제공하여 새로운 서비스 수요를 창출
  - MEC (multi-access edge computing)
    가입자와 인접한 엑세스 네트워크에서 API 제공
    저 지연 서비스 환경 구성이 가능

  - 과거
    - PSTN 환경에서의 IN(intelligent network)
    - 유무선 시스템에서의
      - JAIN (JAVA API integrated network)
      - TINA (telecommunication information networking architecture)
      - CAMEL (customized applications for mobile network enhanced logic)
      -  AIN (advanced intelligent network)
    - 2세대 GSM(global system for mobile communications) 이동통신
      - Parlay/OSA (open service access)
    - 4세대 LTE(long term evolution)
      - 유의미한 수준의 결과 도출

ISDN, PSTN이 연결 기능과 동시에 음성 또는 데이터 서비스를 제공했다면,

통신 시스템이 인터넷 등의 형태로 발전하면서

연결 기능과 독립적인 형대로 다양한 서비스들을 제공힉 위해 기술들을 개발하여 적용하고 있다.

가입자는 서비스 제공 사업자에게 접속하여 다양한 서비스를 이용하는 단계까지도 연구 개발 및 사용화가 진행되고 있다
 - IPTV 내 유튜브, 넷플릭스 등의 다양한 서비스 제공을 말하는 것 같다

 대표적인 사례에 MEC 및 OMA (open mobile aliance) 기반의 서비스 등이 이에 해당한다.

## MEC
https://www.koreascience.or.kr/article/JAKO201851648109474.pdf

https://linkareer.com/cover-letter/28943?page=1&sort=PASSED_AT&tab=all

스터디를 알아봐야하나
