# 콘텐츠 기반 추천 알고리즘
 > 협업은 유저의 기록에 기반, 콘텐츠는 유저가 그동안 다녔던 식당에 대한 정보를 기반으로, 그 식당과 유사한 곳을 추천함

    : 식당 정보(카테고리, 리뷰, 키워드)를 분석해서 특정 식당과 유사한, 평점이 높은 식당을 추천

(즉, 글자를 분석해서 추천해 주는 방식임)
- 적합한 고객: 신규 고객 혹은 기존 고객 무관하게 활용 및 추천이 가능함
### 장점
    : 초기에 사용자의 행동 데이터가 적더라도 추천이 가능하다.  -> 신규 고객 추천 해주기에 좋다.
### 단점
    1.  이용자의 성향을 세부적으로 파악해 추천해주기 어렵다. 콘텐츠 정보를 모두 함축해 고객 맞춤형 추천해주기 어렵다.
    2. 추천 원인에 대한 세부적인 파악과 검증이 어려움, 예를 들어, 중국집에 좋게 평가해서 또다른 중국집을 추천해주었으나, 사실은 특정 메뉴만에 대한 선호가 있었던 걸수도

### 문자 추출 방법: CounterVectorizer, TfidfVectorizer

    - CounterVectorizer:  텍스트에서 단어 출현 횟수를 카운팅하다. 조사, 지시 대명사 같이 의미 없는 글자들도 카운팅한다. 가장 단순하지만 효과가 떨어지는 방법이다.
    -  TfidfVectorizer: CounterVectorizer을 보완했다. 해당 문장 안에서 많이 등장하지만 다른 문서들에서는 적게 사용된 단어들 위주로 카운팅한다. 변별력 있는 단어들을 비중 있게 카운팅한다.

    (→ 맛있었다 라는 말이 많이 나왔다면?: 다른 문서에서도 '맛있다'가 많이 나온다면 그 단어의 비중은 줄어든다. . '맛있다'는 단어 인지 못해도, 평점 순으로 추천 해주면 된다. )

#### 문자 추출 대상: 리뷰, 카테고리, 키워드
    - 리뷰: 1천개 보유
    - 카테고리 & 평점: 2천개 보유
    - 카테고리(한식, 양식….): 70만개 보유
    - 키워드: 없음 ex) 인스타 해시태그 #분위기 #매움 #조명빨 #깨끗함 #친절

### 밀리언 데이터에 적용했을 때 문제점 & 해결 방법
    - 문제점:  리뷰 수는 1천개, 평점은 2천개 정도 있다. 전국 식당수는 85만개이지만, 1~2천개 식당 내에서만 추천해줄 수 있다.
    - 해결방법
     1. 공공 데이터, 사기업 데이터(식당 평점, 리뷰, 키워드)를 얻어 밀리언 데이터에 붙인다.
     2. 크롤링 방법(네이버, 구글, 카카오 지도)으로 데이터(식당 리뷰, 평점, 키워드)을 얻는다.
        - 만약 키워드만 많이 얻을 수 있다면, 평점 & 리뷰 없이도 정확도 높일 수 있다.
        - 85만개 식당 데이터 모두 다 필요없다. 지역별로 새로 생긴 음식점, 인기있는 음식점, 숨겨진 맛집 정도 만 뽑으면 1~5만개 데이터만 모아도 된다.
        - 어떻게 데이터를 얻을 수 있을지... 생각
####   현재 컨텐츠 알고리즘으로 가능한 추천 방식
    : 1. 사전 지역 선택 추천 알고리즘(가능)
      2. 고객이 최근에 (방문한 or 검색한) 식당과 유사한 식당 추천 
       (리뷰 별로, 카테고리 & 평점 별로, 카테고리 별로)
       
 ### 컨텐츠 기반 필터링 프로세스(순서 과정)

    1. 컨텐츠 내용의 ‘텍스트’, BOW(Bag of Words) or Word Embedding 방식으로 Feature Vectorization
    2. 컨텐츠의 Feature 벡터간에 Distance Fuction 
    → Hamming distance, Eucludien distance, Cosine distance, KL Divergence, Malarobis Distance
    3. 유사도 행렬과 별개로, 또 다른 파생 변수로 컨텐츠에 대한 고객들의 평점 갯수와 평점을 이용해 가중 평점을 계산한다. 이 파생 변수는 컨텐츠 성격에 맞게 유동적으로 변경할 수 있다. 
    4. 특정 컨텐츠를 기준으로 그 컨텐츠와 유사도, 가중 평균이 가중 높은 순으로 정렬한 후, 컨텐츠를 추천해준다.
       
 ### Sklearn 자연어 특징 추출

    - CounterVectorizer: 각 텍스트에서 단어 출현 횟수를 카운팅한 벡터 (문서 단위, 문장 단위, 단어 단위)
    단점: 조사, 지시대명사는 카운팅에서 높은 횟수 but, 실질적 의미는 없다. → 별로 좋지 않는 형태가 만들어지다. → (보완) TF-IDF
    - TfidfVectorizer
        - TF-IDF: TF와 IDF를 곱한 값
        즉, TF가 높고,  DF가 낮을 수록 값이 커지는 것을 이용하다.
        → 해당 문장안에서는 많이 등장하지만 다른 문서들까지 전체에서는 적게 사용될 수록 분별력 있는 특징 증가한다.
            - TF(Team Frequency): 특정 단어가 하나의 데이터 안에서 등장하는 횟수
            - DF(Document Frequency): 특정 단어가 여러 데이터에 자주 등장하는지 알려주는 지표
            - IDF(Inverse Document Frequency): DF에 역수를 취해 (inverse) 구함
        - TF-IDF Parameter
            - min-df: DF의 최소 빈도값 설정 , DF는 특정 단어가 나타나는 문서 수 
            ex) min-df = 2 , output = ‘go’, ‘home’, ‘my’
            - analyzer: 학습 단위를 결정하는 parameter
            -  학습단위 : ‘word’ ex) ‘go’ , ‘home’ , ‘my’
            -  학습단위 : ‘char’ ex) a , b, c, d
            - ngram-range: (n-gram: 단어의 묶음)
            -   n-gram_range = (1,1)  ex) (’back’, 0), (’go’, 2), (’is’, 4)
            -   n-gram_range = (1,2)  ex) (’go back’ , 5), (’back to’, 1), (’back’, 0), very good, very bad 같이 묶어야 의미가 살아나는 단어가 있어서 사용하다
            - sublinear_tf: TF(단어 빈도)값이 스무딩 여부 결정 (True / False) → TF값에 대해 아웃라이어 처리를 해준다.
            - max-features: tf-idf vector의 최대 feature를 설정
        - Hashing Vectorizer: CounterVectorizer에서 해시 함수를 사용하여 속도를 높임
        
        
        
 ### Clustering Distance Function   종류

    **데이터 간 거리를 ‘어떤 방법’으로 계산하는지?** 

    - Euclidean distance: 단어의 빈도수 계산 → 비효율적인 방법 → NLP에서 사용 적다
    - cosine similarity: 두 벡터들 사이의 각도를 계산, 벡터 크기는 무시하고 방향 차이만 계산한다. → NLP(자연어 처리)에서 주로 사용한다. 
    단점: 상호관계를 갖는 Features들을 지니고 있는 데이터들간의 유사도는 계산을 잘하지 못한다.
    - Hamming Distance: 한 문자열을 다른 문자열로 바꾸기 위해 몇개의 글자를 바꿔주어야 하는지에 대한 계산 방법
    - KL(Kullback-Leibler Diverge): 두 확률 분포의 차이를 계산하는 방식(차이는 실제 거리를 의미하지 않는다.) 예측 확률 entropy에서 실제 확률 entropy를 빼주어 구하다. 예측 확률이 실제와 가까울 수록 0에 가까워진다.
