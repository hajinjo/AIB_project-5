# CNN을 활용한 손글씨 인식 사칙연산 계산기
코드스테이츠 AIB section4 프로젝트 

## ✔️ 개요

### 주제 선정

- 수학 공부를 하며 아이패드에 필기를 하던중 필기한 수식을 인식해 계산해주는 기능이 있으면 좋겠다는 아이디어가 떠올라 주제를 정하게 되었다

### 데이터셋

- 0~9까지 아라비아 숫자와 사칙연산자(+, - , /, x) 85,709개의 28x28 손글씨 이미지

### 문제 정의

- 숫자 10개와 연산자 4개 총 14개 이미지의 클래스를 분류하는 다중분류 문제
- 손글씨 데이터는 흑백으로 비교적 복잡하지 않은 데이터이기 때문에 CNN으로 간단하게 모델 구성
- 클래스 분류 후 새로운 이미지를 받아 예측하고 계산하는 부분까지 제작해야함

## ✔️ 프로젝트 진행

### 1) 데이터 수집 및 전처리

- 사칙연산 뿐 아니라 더 다양한 기능을 구현하고 싶었지만 손글씨 이미지를 구하는 것에 어려움을 겪어 캐글에서 받은 사칙연산 이미지로 프로젝트 진행
- 흰 배경에 검은 글씨의 흑백 이미지로 이미지 스케일링을 해주고 모델의 인풋에 맞춰 reshape하는 전처리 과정을 거침

### 2) CNN 모델 제작

![스크린샷 2022-05-21 오후 2 43 20](https://user-images.githubusercontent.com/83392231/175848704-7214289d-3b07-42d5-a33d-743255aca3de.png)


### 3) 모델 학습 및 평가

- 모델의 일반화를 위해 데이터 증강 기법을 적용(이미지 회전, 수평·수직 이동, flip)
- kFold로 5번의 교차검증을 하며 학습
- Early stopping을 loss를 기준으로 적용해 loss에 변화가 없으면 학습이 종료되도록 설정
- 만든 모델 저장
    
![스크린샷 2022-05-21 오후 2 50 06](https://user-images.githubusercontent.com/83392231/175848782-547ff511-dff7-4f95-ac7f-96cb507d3d49.png)
    

### 4) 예측

- 새롭게 작성된 이미지를 예측하기 위해서 하나의 긴 수식 이미지를 각각 숫자와 연산자로 분리하는 과정 필요

![스크린샷 2022-05-21 오후 3 01 47](https://user-images.githubusercontent.com/83392231/175848802-07069398-a55d-4280-acdb-2e5b9b560196.png)
    
- 배경을 검정으로 글씨를 흰색으로 뒤집어서 배경을 0(검정색)으로 만듦
- 이미지의 가로열을 왼쪽부터 훑으면서 0이 아닌 값이 연속해서 나오는 열을 찾아서 분리
- 분리하면서 양 옆의 배경인 0 값들이 전부 사라졌으므로 다시 원래 이미지의 폭에 맞춰 0값 열 채움
- 크기 복원한 이미지를 모델 인풋에 맞도록 리사이즈&리쉐입
- 상기 과정으로 전처리한 이미지를 저장한 모델에 넣고 클래스 예측
    
![스크린샷 2022-05-21 오후 3 13 09](https://user-images.githubusercontent.com/83392231/175848823-8cf13efd-4183-4558-a148-91d2913db193.png)
    

### 4) 계산기 부분

- 예측한 클래스 값을 이용해서 계산하여 답을 내주는 부분
    - 클래스 값을 숫자와 연산자로 변환 (10=/, 11=+, 12=-, 13=*)
    - 한자리 숫자거나 연산자의 경우 str과 replace로 바로 변환
    - 두자리 수 이상인 경우
        - 연산자가 들어오기 전까지의 숫자를 따로 리스트로 분리
        - 자릿 수 파악을 위해 리스트의 len을 구한 후 1씩 빼면서 10의 지수로 둠(10의 0승=1, 10의 1승=10…)
        - for 문을 돌면서 자릿수 파악을 위한수와 현재 위치의 수를 누적합하여 하나의 수를 구함
    - 마지막으로 변경한 수식을 하나의 문자열로 합친수 eval 함수를 통해 계산
        
![스크린샷 2022-05-21 오후 3 35 30](https://user-images.githubusercontent.com/83392231/175848827-808b9721-ad0a-4898-8157-42694d3053c8.png)
        
- 계산 불가인 경우 예외 처리 (연산자로 시작하거나 끝나는 경우 등)

## ✔️프로젝트 결과

### 예측 성공

input 

<img width="429" alt="스크린샷 2022-06-27 오전 11 29 51" src="https://user-images.githubusercontent.com/83392231/175849040-46e25492-79c7-4704-907d-08453d50a9b6.png">

output 

<img width="429" alt="스크린샷 2022-06-27 오전 11 30 02" src="https://user-images.githubusercontent.com/83392231/175849047-ca9fd8d7-98c9-48ab-a279-a21389d0130f.png">

### 예측 실패

input 

<img width="429" alt="스크린샷 2022-06-27 오전 11 30 06" src="https://user-images.githubusercontent.com/83392231/175849086-1f742f24-379b-4552-8429-563ec070b885.png">

output 

<img width="461" alt="스크린샷 2022-06-27 오전 11 30 13" src="https://user-images.githubusercontent.com/83392231/175849091-aa21b891-3dac-47be-9fa3-d7de29d78a8c.png">

- 첫번째 것은 9를 4로 잘못 인식해서 실패한 케이스로 사람이 보기에도 악필인 글씨는 모델도 헷갈려 함
- 이외에도 0&6, 7&9 처럼 비슷하게 생긴 숫자는 종종 예측에 실패

![데이터 셋에서 가져온 9와 4 이미지. 상당히 비슷하게 생김](https://user-images.githubusercontent.com/83392231/175848976-66734f9e-a15a-4b38-b420-2c3f04586946.png)


데이터 셋에서 가져온 9와 4 이미지. 상당히 비슷하게 생김

## ✔️보완할 점

- **비슷하게 생긴 숫자 예측 실패**
    - 일차적으로 생각해볼 수 있는 것은 더 많은 데이터를 확보 하여 더 깊은 신경망으로 학습시키기
    - 사용자 입력을 받아 예측 실패시 마다 모델을 업데이트 해나갈 수 있게 만들기
    - 더 나아가서 개인별로 글씨체를 학습하고 업데이트 할 수 있다면?

- **더 다양한 계산식 수행**
    - 대량의 **손글씨 학습 데이터**를 구하는데 어려움이 존재, 어디서 데이터를 구할 수 있을지 고민 필요

- 사용자에게 직접 입력 받을 수 있는 form 제작
