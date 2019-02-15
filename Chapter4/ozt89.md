# 사원수

> 앞부분 회사가서 merge

> 사원수 연산
* 사원수 합은 사용되지 않는다.
* 사원수 곱셈
    * 사원수 p, 사원수 q
    * pq = [(ps * qv + qs * pv + pv x qv), (ps * qs - pv * qv)]
* 켤레와 역
    * q^-1(사원수 q의 역): 원래 값과 곱하면 스칼라 값 1이 되는 사원수
    * q*(사원수 q의 켤레): [-qv, qs]
    * q^-1 = q* / |q|^2
    * 3D 회전변환의 사원수는 항상 단위 길이이므로 q^-1 == q*
* 켤레와 역의 곱셈
    * (pq)* == q* x p*
    * (pq)^-1 == q^-1 x p^-1

> 사원수를 이용한 벡터 회전
* 벡터를 사원수로 표현하기
    * 벡터의 x, y, z값에 스칼라값 0을 사용
    * v = [vx, vy, vz, 0]
* 벡터를 사원수 q로 화전하기
    * v` = rotate(q, v) = qvq^-1 = qvq*
* v`(회전한 결과 벡터)에서 벡터 요소만 가져오면 끝

> 사원수 결합
* 오일러 회전변환처럼 사원수도 결합가능
* 회전 변환되는 순서와 반대로 계산한다. (v로부터 가까운순으로 계산)
    * Qnet = q3q2q1
    * v` = rotate(Qnet, v) = (q3q2q1)v(q1*q2*q3*)

> 사원수를 행렬로 표현하기
* 사원수 q [x, y, z, w] 를 3x3 행렬 R로 표현할 수 있다 (책 수식 참조)
* 코드로 표현한것 (책 코드 참조)

> 회전 변환의 보간
* 선형 보간 (LERP)
    * 사원수는 두 회전변환을 보간할 수 있다.
    * 에니메이션, 역학, 카메라 시스템등 다양한 부분에 응용된다.
    * Lerp(q1, q2, x) = normalize((1-x)q1 + xq2)

* 구면 선형보간 (SLERP)
    * 사원수의 lerp는 구면사이의 현을 선형으로 보간한다.
    * 보간값에 따른 회전각이 일정하지 않다 (애니메이션의 각속도가 일정하지 않다)
    * SLERP는 사인과 코사인을 통해 원을 따라 회전 보간하는 것을 구현한다.
    * SLERP(q1, q2, x) = w1q1 + w2q1
        * w1 = sin((1-x)*사이각) / sin(사이각)
        * w2 = sin(x*사이각) / sin(사이각)
        * 사이각 = cos^-1(q1*q2)
            * cos(사이각)은 두 사원수의 4차원 내적과 같다
    * SLERP는 성능적으로는 LERP보다는 느리다 (최적화 잘하면 괜찮을수도)

# 회전 변환간 비교

> 오일러 각
* 세 개의 스칼라값 [요, 피치, 롤]
* 단순, 직관적, 한축으로 회전하는 경우 보간도 쉽다
* 임의의 축을 기준으로 회전변환하는 경우 보간은 어렵다.
* 짐벌락
    * 한축으로 90도 회전하는 경우 나머지 두 축이 곂쳐지는 현상
    * 축이 곂쳐서 나머지 두 축중 한축으로 회전 변환할 수가 없다.
* 회전 변환에 따라 나머지 축이 변형되므로 회전 순서에 따라 결과가 달라진다.

> 3x3 행렬
* 변형할 기저축 3개를 사용한 회전 변환
* 짐벌락 없고, 임의의 회전변환도 표현 가능하다.
* 점과 벡터 회전변환시 행렬 곱셈으로 쉽게 계산할 수 있다.
* 사람이 직관적으로 이해하기 어렵다. 변형된 결과를 예측하기 어렵다.
* 보간하기 힘들다. 저장공간이많이 필요하다 (float 9개)

> 회전축 + 회전각
* 회전축을 나타내는 단위 벡터와 회전각으로 회전 변환을 표현
* 직관적이며 적은 저장공간을 차지
* 복수의 회전 변환을 결합할 수 없다.
* 회전 변환을 보간하기 어렵고 벡터를 바로 회전변환하기 어렵다.
* 벡터를 회전축 + 회전각 표현법 내지 사원수로 변형해야 한다.

> 사원수
* 회전축 + 회전각 표현법과 유사하나 더 많은 장점이 있다.
* 회전 변환을 결합할 수 있다.
* 사원수 곱을 통해 점과 벡터를 회전 변환 할 수 있다.
* 회전 변환을 보간하기 쉽다.
* 적은 저장 공간을 차지한다 (flaot 4개)

> SQT 변환
* 4x4 행렬의 아핀변환의 사원수 버젼
* 스케일(S) + 사원수(Q) + 평행이동(T)
* 균등 스케일인 경우 float 8개, 아니면 10개

> 겹 사원수
* 사원수의 pair, 8차원 벡터
* 어려워보인다 패스

> 회전 변환과 자유도(DOF)
* DOF: 한 물체의 외적인 상태가 변할 수 있는 고유한 방법의 수
* 6자유도: 3차원의 물체의 자유도 (평행이동 3 + 회전변환 3)
* 모든 회전변환의 자유도는 3인데 제약조건의 개수가 더해져서 변수가 늘어난다.
    * 오일러각: 3
    * 회전축 회전각: 4 (축은 단위길이 제약조건 1)
    * 사원수 4 (사원수는 단위길이 제약조건 1)
    * 3x3 9 (행3, 열3 모두 단위길이 제약조건 6)
    * 억지논리

# 기타 유용한 수학적 개념

> 직선, 반직선, 선분
* 직선은 점 p와 방향단위벡터 u로 표현가능
    * P(t) = P0 + t*u
* 반직선은 t >= 0 인 직선
* 선분은 l <= t <= r 인 직선
    * 두 끝점을 P0, P1이라 할때
    * L = P1 - P0
    * P(t) = P0 + t*u (0 <= t <= |L|)
    * P(t) = P0 + t*L (0 <= t <= 1)

> 구
* 중심점 C와 반지름 r로 정의 [Cx, Cy, Cz, r]

> 평면
* 평면의 방정식: Ax + By + Cz + D = 0
* 평면위의 한 점 P0와 평면 법선 벡터 u로 정의 가능
    * normalize([A, B, C]) == u    
    * d = D / |[A, B, C]
    * d가 양수면 u가 원점을 향한다
    * 정규화한 평면식 ax + by + cz = -d
    * n * P = -d 의 또다른 표현이다.
* 4차원 벡터 [ux, uy, uz, d] 로 평면을 고유하게 표현할 수 있다.
* 평면의 4차원 벡터를 좌표계 변환할때 변환 행렬의 역전치 행렬을 곱해서 변환할 수 있다.

> AABB (axis- aligned bounding box)
* 모든 면이 좌표축에 나란한 3D직육면체
* 세 좌표축의 최대 최소값 6개 성분으로 구성
* 좌표값 대소 비교만으로 AABB 교차 검사할 수 있다.
* 충돌 대상을 조기에 걸러내는데 유용

> OBB (oriented bounding box)
* 안에 감싸고 있는 물체의 경계에 맞게 정렬된 직육면체
* 물체의 로컬 공간 축에 정렬된 AABB
* OBB가 정렬된 좌표계로 변환후 AABB 교차 검사

> 절두체 (frustrum)
* 6개의 평면으로 구성된 머리가 잘린 피라미드 형태
* 3D월드를 가상 카메라 시점으로 원근 투영할때 보이는 영역을 정의
* 4개의 평면은 화면의 가장자리 경계, 2개는 far, near plane
* 교차 검사
    * 모든 평면의 앞에 있으면 교차
    * 카메라 원근 투영을 이용해 변환해서 절두체를 AABB로 변환후 교차 검사

> 볼록 다면체 공간 (convex polyhedral region)
* 복수개의 임의의 평면으로 구성된 공간
* 모든 평면의 앞에 있으면 교차
* 복잡한 트리거 지역을 정의할때 사용됨

# SIMD 연산

> SIMD (단일 명령어 다중 데이터) 연산이란
* 여러개의 데이터에 같은 연산을 할때, 기계어 하나로 병렬 연산하는 것
* 벡터, 행렬 곱을 훨씬 빠른 속도로 연산할 수 있다.
* 게임엔진에서 자주 사용되는 SSE 모드
    * 32 비트 부동소스 4개를 하나의 레지스터로 묶음
    * 덧셈, 곱셈등의 연산을 4쌍의 부동소수에 한명령어로 동시 수행

> SSE 레지스터
* 128비트 레지스터 하나에 32비트 부동소수 4개를 묶어서 저장
* 각각의 부동소수는 동차 좌표계 표기법과 동일하게 [x, y, z, w]로 표현
    * ex) 덧셈연산 addps (xmm0, xmm1) = 결과는 상상에 맡긴다.
* SSE 레지스터의 각 부동 소수를 각각 따로 빼면 속도가 느려진다.
* float과 SSE를 섞어쓰는 코드는 최대한 지양하고 SSE는 최대한 오래 가져가자.
* 스칼라 값이라도 SSE로 넣어서 쓰면 연산속도 저하를 막을 수 있다.
    * s = [s, s, s, s]

> __m128 데이터 타입
* sse 128비트 레지스터를 C, C++에서 사용하는 데이터 타입
* ram에 저장되었다가 연산에 사용될때 sse 레지스터로 처리

> __m128 변수 정렬
* ram에 저장될때 16바이트 메모리 주소 경계에 위치하도록 보장해야 한다.
    * 변수의 주소가 0x0으로 끝나야한다.
* __m128 변수가 있는 구조체, 클래스는 
    * 자동 or 전역변수인 경우 컴파일러가 알아서 정렬한다. (패딩)
    * 동적으로 할당된 메모리에 있는 자료구조의 경우는 프로그래머가 책임져야 한다.

> SSE 내장 명령어로 코드 짜기
* 인라인 어셈블리 코드를 짜서 할 수 있긴한데 어렵고 관리하기 힘들다
* 지원하는 내장 명령어들을 사용하자 
    ```c++
    #include <xmmintrin.h>
    __m128 addWithInterinsics(__m128 a, __m128 b) {
        /*
        __m128 r;
        __asm{
            movaps xmm0, xmmword ptr [a]
            mvaps xmm1, xmmword ptr [b]
            addps xmm0, xmm1
            movaps xmmword ptr [r], xmm0
        }
        */
        // 두개의 __m128 변수를 합한다.
        __m128 r = _mm_add_ps(a, b);
        return r;
    }
    ```
* _mm_load_ps: 메모리에 있는 float 배열을 __m128 변수로 불러온다
* _mm_mul_ps: 두개의 __m128 변수를 곱한다.
* __declspec(align(16)): float 배열을 16바이트 정렬이 되게한다

> 행렬 곱 v * M
* 1번 방법
    * v와 M의 각 열을 곱한다 (_mm_mul_ps)
    * 곱한 결과 레지스터 안의 값들을 각각 더해서 결과 레지스터의 각 위치에 두어야한다.
    * sse구조상 쉽지가 않다.
* 2번 방법
    * v의 각 스칼라값을 4개의 __m128 형태로 만든다.
        * _mm_shuffle_ps: sse 레지스터의 성분을 임의로 위치를 바꿀 수 있는 명령어
        * 코드는 책참조
    * 4개의 스칼라 벡터를 M의 각 행에 곱하고 결과를 모두 더한다.
* 곱하고 더하기 명령어가 있는 CPU에서는 더 빠르고 간단하게 할 수 있다.
    * SSE에는 없다.

# 난수 생성

* 난수 생성기는 결정적이고 미리 정해진 숫자를 연속적으로 줄 세울 뿐이다.
* 훌륭한 난수생성기는 주기가 길고, 검증된 테스트를 통과할 수 있어야 한다.

> 선형 합동 생성기
* 실행 속도가 빠르고 단순하다.
* Numerical Recipes in C 라는 책에 자세히 나와있다
* 품질이 그렇게 좋지는 않다.

> 메르센 트위스터
* 2^19937 - 1 의 긴 주기를 갖고 있다.
* 매우 높은 동일 분포 차원을 갖는다. (연속된 숫자간의 연관성이 적다)
* 여러가지 고난도 통계적 무작위성 테스트를 통과했다.
* 빠르다. SIMD를 사용한 SFMT가 훌륭하다.

> 마더-오브-올과 Xorshift
* 마더-오브-올
    * 다이하드 랜덤 테스트 개발한 조지 마사글리아가 만든 조흔 난수생성기
    * 주기가 2^250인 난수 생성기
    * 빠른 속도가 필요한 상황에 가장 훌륭한 난수생성기중 하나
* Xorshift
    * 마사글리아가 또 만들었는뎅 좀더 빠르고 좀더 임의성이 떨어짐