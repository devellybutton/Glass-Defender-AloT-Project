# 폴더 내 파일 설명

| 파일명 | 내용 |
|---------|--------|
| <b>`crawl_google(Jupyter).ipynb`</b>      |    Selenium을 사용하여 <b>`구글 이미지 검색`</b>에서 이미지를 스크롤 다운하며 수집하고, 각 이미지의 src 링크를 통해 이미지를 다운로드하여 지정된 경로에 저장하는 스크립트      |
| <b>`crawl_naver(Jupyter).ipynb`</b> |  Selenium을 사용하여 <b>`네이버 이미지 검색`</b>에서 이미지를 스크롤 다운하며 수집하고, 각 이미지의 src 링크를 통해 이미지를 다운로드하여 지정된 경로에 저장하는 스크립트     |

-----

# 1. 데이터 수집

## 1) 클래스 분류와 모델 학습 개요

<div style="width: 60%; margin: 0 auto;">

![흰색1](https://github.com/user-attachments/assets/5189a2b3-5dee-4a95-a74a-7a9772c156f9)

</div>

- 프로젝트의 목적에 따라 카메라 프레임에서 깨지기 쉬운 **유리**로 된 **병**과 **잔**을 **감지**하는 것이 필요하였다. 이를 위해 초기에는 **YOLOv5** 딥러닝 객체 탐지 알고리즘을 활용해 실험을 하였다.
- 그 결과, 소주병과 맥주병은 '**bottle**' 클래스로 잘 감지되었지만, 소주잔과 맥주잔을 포함한 여러 가지 종류의 잔은 ‘**wine glass**’ 혹은 '**cup**'으로 감지되어 **추가적인 분류**가 필요하였다.
- 식당에서 제공되는 술병의 대부분은 유리 재질인 경우가 보편적이기 때문에, 우선 '**bottle**' 클래스에 대한 세부적인 분류는 진행하지 않았다.

<br>

<div style="width: 60%; margin: 0 auto;">

![흰색3](https://github.com/user-attachments/assets/33dd7551-1432-4e74-85e2-cb1337a9b8b4)

![흰색2](https://github.com/user-attachments/assets/d88b8aad-477e-40ca-87c6-0761a405048e)

</div>

- 기본 YOLOv5에서는 일차적으로 'cup' 클래스로 분류된 다양한 종류의 컵을 **세부적으로 분류**하기 위해 CNN모델 중 **MobileNetV2** 모델을 활용하였다.
- 유리 **소주잔(2)** 과 유리 **맥주잔(1)** 을 각각의 클래스로 설정하고, *스테인리스, 종이컵, 손잡이가 달린 머그컵 등*을 모두 '**컵(0)**'이라는 하나의 클래스로 통합한 후, **MobileNetV2**를 **전이학습**하여 새로운 모델을 생성하였다.
- 본 프로젝트에서는 '**bottle**' 클래스에 대한 세부적인 분류는 수행하지 않았다. 하지만 이후에 기술될 MobileNetV2을 이용한 전이학습 과정과 유사한 접근으로 '**bottle**'에 대한 세부적인 분류를 진행할 예정이다.

## 2) 이미지 크롤링

- **대상** : ‘소주잔’, ‘맥주잔’ 및 ‘그 외 종류의 컵(종이, 스테인리스, 플라스틱 등)’ 이미지
- **방법** :
    - 네이버 이미지검색 및 구글 이미지검색 페이지에 접속
    - 각 수집 대상에 맞는 키워드를 검색창에 입력
- **검색어**
    - **컵** : ‘paper cup’, ‘cup’, ‘stainless steel cup’, ‘컵’
    - **맥주잔** : ‘맥주잔’, ‘beer cup’, ‘beer glass’
    - **소주잔** : ‘소주잔’, ‘small glass cup’, ‘soju glass’, ‘soju cup’
- **개수**
    - 해당 검색결과 페이지의 끝이 나올 때까지 *(평균 200~500)*
    - **컵** : 2169개, **맥주잔** : 2303개, **소주잔** : 1155개
    - 각 키워드별 중복되는 이미지가 있을 수 있으며, 이를 고려하여 수집함.

-------

# 2. 데이터 처리

- 중복 이미지 제거 : <b>`VisiPics V1.31`</b> 프로그램을 활용함.
- 학습에 적합한 이미지를 선별함.
- 맥주와 소주잔의 경우 액체가 담긴 유무에 따라 분류하여 선별함.
- *바이젠, 필스너, 머그 등* 다양한 종류의 맥주잔을 수집했지만, *고블릿과 튜립*과 같은 와인잔과 유사한 둥근 형태의 잔은 식당에서 흔히 볼 수 없는 편이며, 다른 맥주잔들과 형태가 크게 다르기 때문에 맥주가 담긴 1~2개 이미지만 남겨둠.
<details>
<summary>이미지 제외 기준 가이드라인</summary>

![제외기준001-vert](https://github.com/user-attachments/assets/025c4a6c-98d5-4c9e-91b5-be118c6a13ba)

</details>