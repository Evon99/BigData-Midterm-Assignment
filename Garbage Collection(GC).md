# 가비지 컬렉션 (Garbage Collection)

### 1. 정의: 메모리 관리 기법 중 하나로, 프로그램이 동적으로 할당했던 메모리 영역 중에서 필요없게 된 영역(사용되지 않는 메모리 자원(Garbage))을 자동으로 탐지하여 해제하여 회수하는 프로세스 <br>

### 2. 메모리 관리 <br>
#### 2-1. 메모리 직접 관리 <br>
+ 장점
  - 개발자(사람)가 직접 관리하기에 메모리 최적화가 더 잘되고 micro managing이 가능
+ 단점
  - 필요없는 메모리를 비워주지 않으면, 이것이 메모리 누수(memory leak)로 이어지고, 장기적인 관점에서 치명적인 문제가 될 수 있음
  - 사용중인 메모리를 비워버렸을 때, 존재하지 않는 메모리에 접근하려고 하면 프로그램은 중단되고, 일부 데이터가 손상될 수 있음


#### 2-2. 메모리 자동 관리 <br>
+ 장점 <br>
  - 메모리 누수(memory leak)와 같은 문제를 방지하고, 개발자가 명시적으로 메모리를 해제하는 부담을 줄여줌
+ 단점
  - 자동으로 메모리를 관리하다보니 개발자(사람)가 직접하는 것보다 최적화가 덜 됨

### 3. 파이썬의 GC 방식
#### 3-1. 레퍼런스 카운팅
+ 정의: 참조 횟수가 0이된 객체를 메모리에서 해제하는 방식
+ 파이썬에선 어떤 객체가 생성되면, C 수준 객체는 이 파이썬 객체의 타입과 레퍼런스 카운트를 가지고 있게됨
  - **레퍼런스 카운트(reference count)**: 특정 객체가 몇 번이나 참조(referenced)되고 있는지를 나타냄
  - 증가 방법
    * 객체를 변수에 할당
    * 객체를 리스트, 튜플과 같은 자료구조에 하거나 인스턴스 프로퍼티로 추가
    * 함수에 파라미터로 전달
+ 모든 객체는 참조 당할 때 레퍼런스 카운터를 증가시키고 참조가 없어질 때 카운터를 감소시킴.
+ 객체 카운트 0: 더 이상 이 객체에 접근하고 있는 코드가 없음 -> 메모리에서 삭제, 즉 할당 해제를 할 수 있고, 바로 이 때 객체가 지워지게 됨. 따라서 안전하게 해당 메모리를 "즉시' 확보 가능
+ Ex) <br>
\>>> import sys <br>
\>>> a = 'python' <br>
\>>> sys.getrefcount(a) <br>
출력: 2 <br>
위 코드에서 'python'은 힙에 할당되고, a는 지역변수라는 가정 하에 스택에 할당되어 'python'이 위치한 힙의 주소를 갖고 있음 <br>
첫 번째 참조: a가 위치한 스택의 주소가 콜스택에서 참조되고 있음(variable 생성) <br>
두 번째 참조: a가 getrefcount()에 전달될 때 <br>
만약 콜스택에서 a가 위치한 레벨의 함수가 실행 완료되면, 콜스택에서 참조하고 있는 a가 사라지기에 reference count는 0이 되고 gc에 의해 메모리에서 해제 <br>
\>>> import sys <br>
\>>> a = 'garbage' <br>
\>>> b = \[a] <br>
\>>> c = { 'key': a } <br>
\>>> sys.getrefcount(a) <br>
출력: 4 <br>
**a = 'garbage'**: 문자열 'garbage'를 변수 a에 할당함으로써 참조 <br>
**b = \[a]**: 리스트 \[a]를 변수 b에 할당. 리스트 \[a]는 a를 참조 <br>
**c = { 'key': a}**:  딕셔너리 {'key': a}를 변수 c에 할당 이 딕셔너리는 키 'key'가 a를 참조하고 있습니다 <br>
**sys.getrefcount(a)**: a가 getrefcount()에 전달될 때 참조 <br>
+ 문제점: 레퍼런스 카운팅은 매우 직관적이고 즉각적으로 객체를 삭제할 수 있지만, 해결할 수 없는 하나의 케이스가 있음
  - 객체의 순환 참조가 발생하는 경우 객체가 메모리에서 영원히 지워지지 않는 문제 -> 두 객체가 서로를 참조하고 있다면 어느 객체 하나를 삭제하더라도 나머지 객체의 카운트가 항상 1이기 때문에 두 객체 모두를 삭제할 수 없다는 문제
  - Ex) <br>
  자기 자신을 참조하는 경우 <br>
  \>>> a = [] <br>
  \>>> a.append(a) <br>
  \>>> del a <br>
  a의 참조 횟수는 1이지만 이 객체는 더 이상 접근할 수 없으며 레퍼런스 카운팅 방식으로는 메모리에서 해제될 수 없음 <br>
  서로를 참조하는 경우 <br>
  <pre>
  <code>
  class Object:
    def __init__(self, value):
      self.value = value
      self.next = None
    
  def create_circular_reference():
    object1 = Object(1)
    object2 = Object(2)
    object1.next = object2  # object1의 next는 object2를 가리킴
    object2.next = object1  # object2의 next는 object1을 가리킴
    # 이 때 object1의 레퍼런스 카운터는 Object(1)와 object1.next로 2
    # object2는 Object(2)와 object2.next로 2
    del object1  # object1의 레퍼런스 카운터가 1로 감소. object2는 그대로 2
    del object2  # object2도 2로 감소

  create_circular_reference() 
  </code>
  </pre>
  위 코드에서 object1와 object2가 서로 참조하기 있기 때문에 레퍼런스 카운트는 둘 다 1이지만 도달할 수 없는(unreachable) 가비지가 됨
  - 이러한 순환 참조가 반복되어 가비지를 처리하지 못하게 되면 메모리 누수(leak)로 이어지게 됨.
  - 해결 방법
    1. None 활용
      * 더 이상 사용하지 않는 객체에 'None' 값을 주어 해당 객체에 대한 참조를 해제한다.
      * Ex)
      <pre>
      <code>
      class Object:
        def __init__(self, value):
          self.value = value
          self.next = None

      def solve_circular_reference():
        object1 = Object(1)
        object2 = Object(2)
        object1.next = object2  # object1의 next는 object2를 가리킴
        print(object1.next is object2)  # True로 object1의 next는 object2를 가리킴
        # 이 때 object1의 레퍼런스 카운터는 Object(1)와 object1.next로 2
        object2.next = object1
        print(object2.next is object1)  # True로 object2의 next는 object1을 가리킴
        # object2는 Object(2)와 object2.next로 2
        print(object1.next)
        # <__main__.Object object at 0x7efd7b3bbee0> 객체 표현 출력
        print(object2.next)
        # <__main__.Object object at 0x7efd7b3bbfd0> 객체 표현 출력
        object1.next = None
        object2.next = None
        print(object1.next)
        # None 출력
        print(object2.next)
        # None 출력

        solve_circular_reference()
      </code>
      </pre>
    2. 약한 참조
       * 정의: 대상 객체를 참조는 하지만, 대상 객체에 대해서 참조 수(레퍼런스 카운터)에는 영향을 주지 않는 것 <br>
       강한 참조(일반적인 참조)와 달리, GC가 해당 객체를 회수할 수 있는 상태 유지 <br>
       -> 객체를 참조하는 동안 객체의 생명 주기에 직접적인 영향을 주지 않음. 즉, 다른 코드에서 객체에 대한 강한 참조가 없는 경우에도 약한 참조는 해당 객체를 참조할 수 있음
       * 함수: class weakref.ref(object\[, callback])
         + 객체에 대한 약한 참조 객체를 반환 <br>
           객체가 메모리에 존재하는 경우 객체에 대한 참조를 반환하며, 그렇지 않은 경우 None을 반환
        * Ex)
          <pre>
          <code>
          import weakref

          class Object:
            def __init__(self, value):
              self.value = value
              self.next = None

          def solve_circular_reference():
            object1 = Object(1)
            object2 = Object(2)
            object1.next = weakref.ref(object2)
            print(object1.next is object2)  # object2를 약한 참조 하므로 False
            object2.next = weakref.ref(object1)
            print(object2.next is object1)  # object1을 약한 참조 하므로 False
            print(object1.next)
            # \<weakref at 0x7f458ff13bf0; to 'Object' at 0x7f45a4207670> 객체 표현 출력
            print(object2.next)
            # \<weakref at 0x7f45a41fca40; to 'Object' at 0x7f45a42072e0> 객체 표현 출력

            solve_circular_reference()
          </code>
          </pre>
      * 특징
        + 객체 캐싱, 캐시 관리, 콜백 등에서 유용함 <br>
          -> 대용량의 이미지를 가지고 있을 때, 메모리 관리를 편하게 하기 위해서 사용 (불필요한 데이터를 계속 가지고 있을 필요가 없음)
      * 한계
        + 약한 참조가 가능한 대상 <br>
          -> 클래스 인스턴스, 파이썬 함수, 인스턴스메서드, sets, frozensets, 파일객체, 생성자, 타입객체, bsddb모듈의 DBCursor, 소켓, arrays, dequeue, 정규식 패턴 객체

#### 3-2. 세대 기반(Generational Garbage Collector)
+ 레퍼런스 카운팅(주된 방법)과 함께, 위에서 언급함 문제점을 해결하기 위해 파이썬은 세대 기반 쓰레기 수집 기법 사용
+ generation(세대)과 threshold(임계값)로 가비지 컬렉션 주기와 객체를 관리
+ 기본 흐름: 가비지 컬렉터는 총 3개의 세대를 지니고 파이썬의 모든 객체를 추적. 만일 객체의 수가 임계값을 넘으면 파이썬 인터프리터에 의해 GC가 작동. 이때 첫 세대가 시작되고, 여기서 생존한 객체는 다음(older) 세대로 넘어감.
+ 수행 과정
  1. 새로운 객체가 생성되면, 메모리와 0세대에 객체를 할당한다. 이 때, 객체 수가 0세대 임계값보다 크면 collect_generations()가 호출
  2. collect_generations()은 0, 1, 2세대 모두에 대해서 검사를 수행하며 2세대부터 역순으로 진행
  3. 각 세대에 대해 임계값을 검사한 후 GC 수행
+ Ex)
\>>> import gc <br>
\>>> gc.get_threshold() <br>
(700, 10, 10) <br>
각각 threshold 0, threshold 1, threshold 2를 의미, n세대에 객체를 할당한 횟수가 threshold n을 초과하면 가비지 컬렉션이 수행되며 이 값은 변경할 수 있음(gc.set_threshold()) <br>
  - 0세대(threshold 0): 메모리에 객체 할당 횟수에 해제된 횟수를 뺀 값으로, 객체 수를 의미. 메모리에 객체 수가 threshold 0을 초과하면 실행.
  - 1세대(threshold 1): 0세대 가비지 컬렉팅이 발생한 횟수에 대한 임계치로, 0세대 GC가 일어난 후 살아남은 0세대 객체를 1세대로 이동시킨 후 카운터를 1 증가. 이 1세대 카운터가 threshold 1을 초과하면 그 때 1세대 GC 수행
  - 2세대(threshold 2): 1세데 가비지 컬렉팅이 발생한 횟수에 대한 임계치, 1세대와 마찬가지로 1세대 GC가 일어난 후 살아남은 1세대 객체를 2세대로 이동시킨 후 카운터를 1 증가. 이 2세대 카운터가 threshold 1을 초과하면 그 때 2세대 GC 수행
-> 0세대 GC가 객체 생성 700번만에 일어난다면 1세대는 7000번만에, 2세대는 7만번만에 일어남
+ 세대가 낮을수록 더욱 자주 GC를 수행 -> 어린 객체가 오래된 객체보다 해제될 가능성이 높다는 가설(generational hypothesis)에 근거
+ **GC 모듈**
  - **gc.collect_generations()** : 모든 세대에 대해서 2세대부터 0세대까지의 순서로 확인하고, 임계치 초과시 collect() 호출
  - **gc.get_count()**: 각 세대의 객체 수 확인
  - Ex) <br>
  \>>> import gc <br>
  \>>> gc.get_count() <br>
  출력: (314, 7, 1) <br>
  0세대: 314개의 객체  1세대: 7개의 객체  2세대: 1개의 객체
  - **gc.collect()**: threshold에 근거해서 GC 수행하지만, 수동으로 GC를 수행하는 과정이 필요할 수 있다. (메모리 최적화를 더 하기 위해). 이 때 gc.collect()로 수동 실행
    * 특징
      + 개발자가 직접 호출하면 원하는 시점에 쓰레기 수집 수행 가능
      + 가비지 컬렉션 수행하여 순환참조 객체를 메모리에서 해제
      + 순환 참조 탐지 알고리즘을 바탕으로 도달 가능 객체(rechable), 도달 불가능 객체(unrechable)로 구분. 이 때, 도달 가능 객체는 세대 이동, 도달 불가능 객체는 콜백 수행 후 메모리에서 해제.
      + 반환값: 점유된 객체 숫자와 메모리가 해제된 객체 숫자 의미
    * Ex) <br>
  \>>> import gc <br>
  \>>> gc.get_count() <br>
  (314, 7, 1) <br>
  \>>> gc.collect() <br>
  28 <br>
  \>>> gc.get_count() <br>
  출력: (20, 0, 0) <br>
  0세대: 294개의 객체 GC 수행  1세대: 7개의 객체 GC 수행  2세대: 1개의 객체 GC 수행
  - **gc.set_threshold**: 가비지 컬렉션 트리거 임계값 변경
    * Ex) <br>
    \>>> import gc <br>
    \>>> gc.get_threshold() <br>
    (700, 10, 10) <br>
    \>>> gc.set_threshold <br>
    (800, 20, 20) <br>
    \>>> gc.get_threshold() <br>
    (800, 20, 20)
  - **gc.disable()**: 쓰레기 수집 off
+ **순환 참조 해결법(Cyclic GC)**
  - 순환 참조는 컨테이너 객체(e.g. tuple, list, set, dict, class)에 의해서만 발생함. 컨테이너 객체는 다른 객체에 대한 참조를 보유할 수 있음. 그러므로 정수, 문자열을 무시한 채 관심사를 컨테이너 객체에만 집중
    - ![Cycle](/images/Cycle.png)
  1. 객체에 gc_refs 필드를 레퍼런스 카운트와 동일하게 설정 
  2. 각 객체에 참조하고 있는 다른 컨테이너 객체를 찾고, 참조되는 컨테이너의 gc_refs를 감소시킴
  3. gc_ref가 0이면, 그 객체는 컨테이너 집합 내부에서 자기들끼리 참조하고 있음
  4. 참조 불가능 객체(unreachable)로 표시하고 메모리에서 해제
+ 단점: GC를 수행하려면 프로그램을 완전히 중지(stop-the-world)해야함. 따라서 GC가 빈번히 일어날수록 프로그램의 수행 성능이 낮아질 수밖에 없음. CPU 로드 관점에서 보면 oscillation이 발생하는걸 알 수 있음
+ 임계점이 높은 경우: GC의 수행 빈도가 낮아지므로 프로그램이 덜 자주 멈추고 메모리 공간에 가비지가 많이 쌓임, 죽은 객체를 오래 유지하는 cost(비용)로 프로그램에서 계산 비용이 줄어듬
+ 임계점이 낮은 경우: GC의 수행 빈도가 높아져 프로그램이 자주 멈추고 메모리 사용량이 적어짐 <br>
-> 시행 착오를 겪으며 응용 프로그램의 성능을 끌어올려야함

