# 8강 복습 · 딕셔너리와 집합

이 자료는 8강에서 헷갈리기 쉬운 내용을 다시 확인하기 위한 복습 자료다.

복습 순서는 다음과 같다.

1. 딕셔너리 조회
2. `get()`과 `pop()`
3. 빈 딕셔너리와 빈 집합
4. 단어 빈도 세기
5. 집합으로 중복 제거
6. 직접 작성하는 종합 문제

## 1. 딕셔너리 조회 기본

```python
scores = {
    "민수": 80,
    "지영": 95,
}
```

딕셔너리는 키와 값을 연결한다.

- 키: `"민수"`, `"지영"`
- 값: `80`, `95`

키가 확실히 존재한다면 대괄호로 조회할 수 있다.

```python
print(scores["민수"])
```

하지만 없는 키를 조회하면 `KeyError`가 발생한다.

```python
# print(scores["철수"])
```

키가 없을 가능성이 있으면 `in`이나 `get()`을 사용한다.

## 2. `get(key, default)`

```python
value = dictionary.get(key, default)
```

뜻은 다음과 같다.

> 키가 있으면 해당 값을 반환하고, 키가 없으면 기본값을 반환한다.

```python
scores = {"민수": 80}

print(scores.get("민수", 0))
print(scores.get("철수", 0))
```

출력은 각각 `80`, `0`이다.

중요한 점은 `get()`이 키를 새로 추가하지 않는다는 것이다.

```python
scores.get("철수", 0)
print(scores)
```

딕셔너리는 여전히 `{"민수": 80}`이다.

### `get()`과 대괄호의 차이

| 표현 | 키가 있을 때 | 키가 없을 때 |
| --- | --- | --- |
| `scores["민수"]` | 값 반환 | `KeyError` |
| `scores.get("민수")` | 값 반환 | `None` |
| `scores.get("민수", 0)` | 값 반환 | `0` |

### `get()` 대신 사용할 수 있는 방법

키가 있는지 먼저 확인할 수 있다.

```python
if "철수" in scores:
    print(scores["철수"])
else:
    print("점수가 없습니다.")
```

키가 없을 때 기본값과 함께 항목을 만들고 싶다면 `setdefault()`를 사용한다.

```python
scores.setdefault("철수", 0)
```

`get()`은 조회만 하지만, `setdefault()`는 키가 없을 때 딕셔너리를 변경한다.

## 3. `pop(key, default)`

`pop()`은 딕셔너리에서 항목을 삭제하고 삭제한 값을 반환한다.

```python
scores = {
    "민수": 80,
    "지영": 95,
}

score = scores.pop("민수")

print(score)
print(scores)
```

결과:

```text
80
{'지영': 95}
```

### 키가 없을 때

```python
scores = {"민수": 80}

value = scores.pop("철수", 0)
print(value)
print(scores)
```

`철수`가 없으므로 `0`을 반환하고, 딕셔너리는 바뀌지 않는다.

| 표현 | 키가 있을 때 | 키가 없을 때 |
| --- | --- | --- |
| `pop(key)` | 삭제 후 값 반환 | `KeyError` |
| `pop(key, default)` | 삭제 후 값 반환 | 기본값 반환 |

`pop()`의 기본값은 “키가 없을 때 반환할 값”이지, 딕셔너리에 저장할 값이 아니다.

## 4. 빈 딕셔너리와 빈 집합

```python
empty_dict = {}
empty_set = set()
```

둘 다 비어 있지만 자료형이 다르다.

```python
print(type(empty_dict))
print(type(empty_set))
```

결과는 각각 `dict`, `set`이다.

### 왜 `{}`는 빈 집합이 아닌가?

파이썬에서 `{}`는 빈 딕셔너리로 약속되어 있다.

```python
{}                       # 빈 딕셔너리
set()                    # 빈 집합
{"사과", "바나나"}       # 값이 있는 집합
{"과일": "사과"}        # 값이 있는 딕셔너리
```

집합에 값을 추가할 때는 `add()`를 사용한다.

```python
items = set()
items.add("사과")
```

딕셔너리에 값을 추가할 때는 키를 사용해 대입한다.

```python
items = {}
items["과일"] = "사과"
```

## 5. `counts.get(word, 0)` 실행 추적

다음 코드는 단어의 등장 횟수를 센다.

```python
words = ["apple", "banana", "apple"]
counts = {}

for word in words:
    counts[word] = counts.get(word, 0) + 1
```

실행 과정을 표로 나타내면 다음과 같다.

| 현재 `word` | `counts.get(word, 0)` | 저장 결과 |
| --- | ---: | --- |
| `apple` | 0 | `apple: 1` |
| `banana` | 0 | `banana: 1` |
| `apple` | 1 | `apple: 2` |

여기서 `0`은 처음 본 단어의 시작 개수다. 단어가 이미 있으면 현재 개수를 가져오고, 없으면 0부터 시작한다.

## 6. 문제 4 풀이: 단어 빈도표

### 요구사항

단어 리스트를 받아 단어별 등장 횟수를 딕셔너리로 반환한다.

입력:

```python
["apple", "banana", "apple"]
```

기대 결과:

```python
{"apple": 2, "banana": 1}
```

### 풀이 구조

1. 빈 딕셔너리 `counts`를 만든다.
2. 단어를 하나씩 꺼낸다.
3. 현재 단어의 기존 개수를 조회한다.
4. 기존 개수에 1을 더한다.
5. 딕셔너리에 다시 저장한다.
6. 반복이 끝나면 딕셔너리를 반환한다.

```python
def count_words(words):
    counts = {}

    for word in words:
        counts[word] = counts.get(word, 0) + 1

    return counts
```

빈 리스트를 전달하면 반복이 실행되지 않으므로 빈 딕셔너리가 반환된다.

```python
print(count_words([]))
```

결과:

```python
{}
```

## 7. 문제 5 풀이: 중복 제거

### 요구사항

중복된 숫자가 있는 리스트를 받아 중복이 제거된 집합을 반환한다.

입력:

```python
[3, 3, 1, 2, 1, 4]
```

결과에 들어갈 값:

```python
{1, 2, 3, 4}
```

가장 간단한 방법은 리스트를 `set()`으로 변환하는 것이다.

```python
def remove_duplicates(numbers):
    return set(numbers)
```

호출:

```python
numbers = [3, 3, 1, 2, 1, 4]
result = remove_duplicates(numbers)
print(result)
```

주의할 점:

- 집합은 중복을 제거한다.
- 집합은 입력 순서를 보장하지 않는다.
- 집합은 인덱스로 접근할 수 없다.
- 원본 리스트는 `set(numbers)`만 사용하면 바뀌지 않는다.

## 8. 집합이지만 순서를 유지하고 싶다면

중복 제거와 입력 순서 유지가 모두 필요하면 새 리스트를 만든다.

```python
def remove_duplicates_keep_order(numbers):
    result = []

    for number in numbers:
        if number not in result:
            result.append(number)

    return result
```

이 함수는 `[3, 3, 1, 2, 1]`을 `[3, 1, 2]`로 만든다.

집합을 사용하는 문제와 리스트를 사용하는 문제의 목적이 다르다는 점을 기억한다.

## 9. 복습 문제

### 문제 1 · 안전한 점수 조회

학생 점수 딕셔너리와 학생 이름을 받아 점수를 반환하는 함수를 작성한다. 이름이 없으면 `None`을 반환한다. 함수 밖에서 `None`인지 확인해 안내 문장을 출력한다.

### 문제 2 · 기본값 조회

재고 딕셔너리에서 상품명을 조회한다. 상품이 없으면 재고를 0으로 표시한다. `get()`을 사용한다.

### 문제 3 · 안전한 삭제

상품 딕셔너리와 삭제할 상품명을 받아 상품을 삭제하고 삭제한 가격을 반환하는 함수를 작성한다. 상품이 없으면 `None`을 반환한다. `pop(key, default)`를 사용한다.

### 문제 4 · 단어 빈도

`["cat", "dog", "cat", "bird", "dog", "cat"]`을 전달했을 때 단어별 횟수가 정확히 나오는지 확인한다. 결과를 `items()`로 순회해 `단어: 횟수` 형태로 출력한다.

### 문제 5 · 중복 제거

`[1, 2, 2, 3, 4, 4, 5]`의 중복을 제거하는 함수를 작성한다. 반환값의 자료형이 집합인지 확인하고, 원본 리스트가 바뀌지 않았는지 출력해 확인한다.

### 문제 6 · 공통 항목

두 과일 리스트를 받아 두 리스트에 모두 들어 있는 과일을 집합으로 반환한다. 리스트를 집합으로 바꾼 뒤 교집합 연산을 사용한다.

## 10. 최종 확인

다음 질문에 자기 말로 답한다.

1. `get()`은 키가 없을 때 무엇을 반환하는가?
2. `pop(key, default)`는 `get()`과 달리 어떤 상태 변화를 만드는가?
3. 빈 집합을 `{}`로 만들면 안 되는 이유는 무엇인가?
4. `counts.get(word, 0)`에서 0은 언제 사용되는가?
5. 단어 빈도에는 왜 리스트보다 딕셔너리가 편리한가?
6. 중복 제거에서 집합의 단점은 무엇인가?

## 복습 통과 기준

- `get()`과 대괄호 조회의 차이를 설명한다.
- `pop(key, default)`의 삭제·반환 동작을 설명한다.
- 빈 딕셔너리와 빈 집합을 올바르게 만든다.
- 단어 빈도 함수를 직접 작성한다.
- 집합으로 중복을 제거하고 순서가 보장되지 않음을 설명한다.
