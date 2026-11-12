# 5강 · 누적·탐색·반복 제어

**목표:** 반복문 안에서 값을 누적하고, 조건에 맞는 값을 찾고, `break`와 `continue`로 반복 흐름을 조절한다.

이번 강의는 4강의 `for`, `range`, `while`을 바탕으로 작은 통계와 검색 프로그램을 만든다. 코드를 실행하기 전에 누적 변수의 변화를 손으로 추적하는 것이 핵심이다.

## 0. 지난 강의와 연결하기

4강에서는 같은 작업을 여러 번 실행하는 방법을 배웠다. 5강에서는 반복할 때마다 결과를 저장한다.

```python
numbers = [3, 7, 2, 9]

total = 0

for number in numbers:
    total += number

print(total)
```

`total`은 반복할 때마다 바뀌는 **누적 변수**다. 실행 흐름은 `0 → 3 → 10 → 12 → 21`이다.

## 1. 합계와 카운터

### 합계 구하기

```python
numbers = [3, 7, 2, 9]
total = 0

for number in numbers:
    total += number

print(f"합계: {total}")
```

합계를 구할 때는 보통 `0`에서 시작한다. 숫자를 더해도 결과에 영향을 주지 않는 값이기 때문이다.

### 조건에 맞는 개수 세기

```python
scores = [55, 80, 92, 61, 40]
passed = 0

for score in scores:
    if score >= 60:
        passed += 1

print(f"합격자 수: {passed}")
```

카운터는 보통 `0`에서 시작하고, 조건을 만족할 때만 `1`씩 증가시킨다.

## 2. 평균 구하기

평균은 합계를 항목 수로 나눈 값이다.

```python
scores = [80, 90, 70]
total = 0

for score in scores:
    total += score

average = total / len(scores)
print(f"평균: {average}")
```

단, 빈 목록에서는 `len(scores)`가 0이므로 나눌 수 없다.

```python
scores = []

if len(scores) == 0:
    print("점수가 없습니다.")
else:
    total = 0
    for score in scores:
        total += score
    print(total / len(scores))
```

빈 입력을 처리할지 먼저 결정하는 것이 중요하다.

## 3. 최댓값 갱신

최댓값을 찾을 때는 현재까지의 최댓값을 저장하고, 더 큰 값을 만나면 바꾼다.

```python
numbers = [3, 7, 2, 9]
maximum = numbers[0]

for number in numbers:
    if number > maximum:
        maximum = number

print(f"최댓값: {maximum}")
```

`maximum`을 `0`으로 시작하면 모든 숫자가 음수일 때 틀릴 수 있다. 그래서 목록의 첫 항목을 초기값으로 사용한다.

빈 목록에는 첫 항목이 없으므로 별도 처리가 필요하다.

```python
numbers = []

if len(numbers) == 0:
    print("숫자가 없습니다.")
else:
    maximum = numbers[0]
    for number in numbers:
        if number > maximum:
            maximum = number
    print(maximum)
```

## 4. 탐색하기

### 첫 번째 일치 항목 찾기

```python
names = ["민수", "지영", "철수"]
target = "지영"
found = False

for name in names:
    if name == target:
        found = True
        break

if found:
    print("찾았습니다.")
else:
    print("없습니다.")
```

`found`는 찾았는지를 기록하는 불리언 변수다. `break`를 만나면 반복문을 즉시 끝낸다.

### `break` 없이 모든 일치 항목 찾기

```python
numbers = [2, 5, 2, 8, 2]

for number in numbers:
    if number == 2:
        print("찾은 값: 2")
```

첫 번째 일치만 필요하면 `break`, 모든 일치를 확인해야 하면 `break`를 사용하지 않는다.

## 5. `continue`로 건너뛰기

`continue`는 현재 반복만 건너뛰고 다음 반복으로 이동한다.

```python
numbers = [1, 2, 3, 4, 5]

for number in numbers:
    if number % 2 == 0:
        continue
    print(number)
```

짝수에서는 출력하지 않고 다음 숫자로 넘어가므로 `1, 3, 5`가 출력된다.

## 6. 중첩 반복

반복문 안에 반복문을 넣을 수 있다.

```python
for row in range(1, 3):
    for column in range(1, 4):
        print(f"({row}, {column})")
```

바깥 반복이 한 번 실행될 때 안쪽 반복이 모두 실행된다. 따라서 바깥쪽 2회와 안쪽 3회로 총 6번 출력된다.

작은 표를 출력할 때 사용할 수 있다.

```python
for row in range(1, 4):
    for column in range(1, 4):
        print(row * column, end=" ")
    print()
```

## 7. 자주 하는 실수

### 누적 변수를 반복문 안에서 초기화함

```python
for number in [1, 2, 3]:
    total = 0
    total += number
```

이렇게 하면 매번 `total`이 0으로 돌아간다. 누적 변수는 보통 반복문 **밖에서** 초기화한다.

### 최댓값을 0으로 시작함

음수 목록에서는 잘못된 결과가 나올 수 있다. 비어 있지 않다면 첫 항목을 초기값으로 사용한다.

### `break`와 `continue`를 혼동함

- `break`: 반복문 전체를 끝낸다.
- `continue`: 현재 회차만 건너뛰고 다음 회차로 간다.

### 빈 목록을 확인하지 않음

평균을 구할 때 0으로 나누거나, `numbers[0]`에서 인덱스 오류가 발생할 수 있다.

## 8. 연습 문제

먼저 결과를 손으로 예상하고, 그다음 VS Code에서 실행한다.

### 문제 1 · 합계 추적

```python
numbers = [4, 1, 6]
total = 0

for number in numbers:
    total += number

print(total)
```

각 회차의 `total` 값과 최종 출력값을 적어 보자.

### 문제 2 · 조건별 개수

`numbers = [3, 8, 10, 1, 5]`에서 5 이상인 숫자의 개수를 출력하는 코드를 작성한다.

### 문제 3 · 최댓값

`numbers = [-4, -1, -7]`에서 최댓값을 출력하는 코드를 작성한다. 최댓값의 초기값을 무엇으로 정했는지 설명한다.

### 문제 4 · 탐색

```python
names = ["민수", "지영", "철수"]
```

`target`이 목록에 있으면 `찾았습니다`, 없으면 `없습니다`를 출력하는 코드를 작성한다.

### 문제 5 · `continue`

1부터 10까지 중 3의 배수만 출력하는 코드를 `continue`를 사용해 작성한다.

### 문제 6 · 중첩 반복

다음과 같은 출력을 만드는 코드를 작성한다.

```text
*
**
***
****
```

## 9. 오늘 배운 내용 확인

1. 합계 누적 변수는 왜 보통 `0`에서 시작하는가?
2. 최댓값을 첫 번째 항목으로 초기화하는 이유는 무엇인가?
3. `break`와 `continue`의 차이는 무엇인가?
4. 빈 목록에서 평균과 최댓값을 구할 때 어떤 문제가 생기는가?
5. 중첩 반복에서 안쪽 반복은 몇 번 실행되는가?

## 통과 기준

- 누적 변수와 카운터의 초기값 이유를 설명한다.
- 합계·평균·최댓값·조건별 개수 중 2개 이상을 복사 없이 구현한다.
- 일치 항목이 없는 탐색과 빈 목록을 처리한다.
- `break`와 `continue`의 실행 흐름을 설명한다.
- 중첩 반복의 총 실행 횟수를 손으로 계산한다.

## 나에게 보낼 것

문제 1~6의 답이나 작성한 코드를 보내고, 가장 헷갈린 부분을 하나 적는다. 통과 기준을 확인한 뒤 다음 강의인 함수와 코드 분해 교재를 만든다.
