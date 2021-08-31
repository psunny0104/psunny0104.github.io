---
title: '[Java] replace(), replaceAll() 의차이'
date: 2021-08-31 13:11:15
tags: ['Java']
categories: ['Languages', 'Java']
comments: true
---

# java.lang.String의 replace(), replaceAll()

[코딩테스트 연습 - 숫자 문자열과 영단어](https://programmers.co.kr/learn/courses/30/lessons/81301)

프로그래머스에서 위의 링크에 있는 문제를 풀었다. 구현해야할 핵심 사항은 0 ~ 9가 아니라 'one', 'eight' 처럼 숫자를 나타내는 문자열이 존재하는 경우 해당하는 숫자로 바꾸는 것이었다. 

주어진 문자열을 선형탐색하면서 숫자가 아닌 문자가 나오면 매칭되는 숫자가 나올 때까지 별도의 index 변수와 while을 이용해서 내부에서도 선형탐색을 하는 방법으로 풀었다.

문제를 풀고 나서 다른 분들의 풀이를 보았는데 매우 간략하게 잘 푸신 분들이 많았다. 그 중 특이한 점은 replaceAll()과 replace()를 사용한 풀이가 비슷한 비율로 존재했다는 것이다. 그런데 생각해보니 replace()도 분명히 메소드를 호출한 String에서 인자로 들어온 대상을 모두 바꿨던 것 같은 기억이 났다. 그래서 replaceAll()로 작성된 풀이를 replace()로 바꿔서 제출해도 모두 합격이 되는 것이다!

그래서 replace()와 replaceAll() 메소드와 차이점이 확실히 무엇인지 궁금하다는 생각을 했고 Java API 공식문서를 찾아보았다.

---

## 1. replace()

replace()는 2개로 overloading되어 있다.

### public String replace(char oldChar, char newChar)

- `parameters`

  - oldChar :`char` - 교체의 대상이 되는 char
  - newChar :`char` - replace를 통해 투입되는 char

- `returns`

  - : `String` - String에 존재하는 모든 oldChar를 newChar로 바꾼 String

- `examples`

  ```java
  String replaceTest = "Hello new World";
  System.out.println(replaceTest);
  System.out.println(replaceTest.replace('l', 'L'));
  
  // Hello new World
  // HeLLo new WorLd
  ```

char 단위로도 변환을 할 수 있으며, 존재하는 모든 oldChar를 newChar로 대체한 String을 리턴하고 있다. 결과를 보면 replace도 모두 바꿔주는데 끝에 All이 붙으면 어떤 차이가 존재하는걸까? 점점 궁금해졌다.

### public String replace(CharSequence target, CharSequence replacement)

- `parameters`

  - target :`CharSequence` - replace의 대상이 되는 charSequence
  - replacement :`CharSequence` - replace를 통해 투입되는 charSequence

- `returns`

  - : `String` - String에 존재하는 모든 target을 replacement로 바꾼 String

- `examples`

  ```java
  String replaceTestTwo = "Hello Hello new World";
  System.out.println(replaceTestTwo);
  System.out.println(replaceTestTwo.replace("ll", "LL"));
  
  // Hello Hello new World
  // HeLLo HeLLo new World
  ```

char 단위가 아니라 charSequence에 대해서 당연히 가능하다. 따라서 연속적인 문자열에도 적용이 가능하다. 참고로 `charSequence`는 String, StringBuffer, StringBuilder, CharBuffer 등이 implement 하는데 사용하고 있는 Interface이다. 이름 처럼 char값들을 sequence 형태로 읽을 수 있도록 제공하는데 별도로 정리를 해야할 것 같다.

## 2. replaceAll()

### replaceAll(String regex, String replacement)

- `parameters`

  - **regex** :`String` - replace의 대상이 되는 charSequence
  - replacement :`String` - replace를 통해 투입되는 charSequence

- `returns`

  - : `String` - regex에 매치되는 모든 substring을 replacement로 바꾼 String

- `examples`

  ```java
  String replaceTestThree = "Hello Hello new World";
  System.out.println(replaceTestThree);
  System.out.println(replaceTestThree.replace("[a-z]", ""));
  System.out.println(replaceTestThree.replaceAll("[a-z]", ""));
  
  // Hello Hello new World
  // Hello Hello new World
  // H H  W
  ```

첫번째 파라미터의 이름으로도 알 수 있지만, replace와 다르게 **정규표현식**을 사용한다. 따라서 특정한 하나의 문자나 연속된 문자열의 형태만 찾아낼 수 있는 replace와 다르게 찾고자하는 여러 대상에 대한 정규표현식을 작성한 뒤 한 번에 매치되는 모든 형태를 찾을 수 있다. 

예제를 보면 `[a-z]`라는 정규표현식을 통해서 알파벳소문자를 모두 없애버렸다. 즉, a부터 z까지 각각 replace를 사용하는 것이 아니라, replaceAll을 통해 한 번에 바꿀 수 있었다. 물론 replace 예제처럼 연속된 문자열의 형태로 regex 파라미터를 전달하여 정규표현식으로 처리하더라도 결과는 같으므로, 연속된 문자열을 바꾸기 위해 replace() 사용하나 replaceAll()을 사용하나 **같은 결과**를 리턴하는 것이다.

당연한 이야기이지만 replace에 정규표현식처럼 [a-z]를 target 파라미터로 주면 charSequence이기 때문에 "[a-z]"와 일치하는 문자열을 찾아 바꾸게 된다. "Hello Hello new World"에는 "[a-z]"가 없기 때문에 당연히 그대로 리턴하게 된다.

```java
// replaceAll

public String replaceAll(String regex, String replacement) {
        return Pattern.compile(regex).matcher(this).replaceAll(replacement);
    }
```

```java
// replace

public String replace(CharSequence target, CharSequence replacement) {
        String tgtStr = target.toString();
        String replStr = replacement.toString();
        int j = indexOf(tgtStr);
        if (j < 0) {
            return this;
        }
        int tgtLen = tgtStr.length();
        int tgtLen1 = Math.max(tgtLen, 1);
        int thisLen = length();

        int newLenHint = thisLen - tgtLen + replStr.length();
        if (newLenHint < 0) {
            throw new OutOfMemoryError();
        }
        StringBuilder sb = new StringBuilder(newLenHint);
        int i = 0;
        do {
            sb.append(this, i, j).append(replStr);
            i = j + tgtLen;
        } while (j < thisLen && (j = indexOf(tgtStr, j + tgtLen1)) > 0);
        return sb.append(this, i, thisLen).toString();
    }
```

그런데 regex의 타입이 String이기 때문에 어떻게 정규표현식으로 처리가 되는지 궁금할 수 있다. **replaceAll()과 replace()와 내부 구조가 다른데**, replaceAll()의 내부에서는 정규표현식으로 해당되는 substring을 찾기 위해 정규식의 컴파일된 표현인 `Pattern`와 Pattern을 해석하여 match 작업을 수행하는 `Matcher`라는 정규식 관련 클래스에 의해 동작한다. replace()는 `StringBuidler`를 통해 동작하는 것을 알 수 있다. 

### replaceFirst(String regex, String replacement)

replaceAll()과 다르게 **첫번째**로 매치되는 substring만 replacement로 바꿔준다. 따라서 용도에 따라 replaceFirst()과 replaceAll()을 구분해서 사용하면 된다. 

처음 생각했던 것과 다르게 replace()와 구분하는 용도가 아니라, **정규표현식을 이용하는 2개의 메소드를 구분하기 위해 All과 First를 각 메소드의 끝에 붙인 것 같다는 생각이 들었다.**

---

replace()와 replaceAll()의 차이에 대해 알아보았다. 위의 문제처럼 단순히 연속된 형태의 문자열을 변환하기 위한 것이라면 replaceAll()을 굳이 사용하지 않고 replace()를 사용해도 충분하다는 생각이 든다. char를 한 번에 찾고자 할 때 혹은 여러 문자열들을 정규표현식으로 표현하여 한 번에 바꾸는 것처럼 정규표현식이 유용한 순간에는 replaceAll() 혹은 replaceFirst()를 사용하면 될 것 같다. 

**공부를 하다보니 프로그래밍 언어를 개발하는 분들에 대한 존경심이 마구마구 생겼으며, 혹시 미래에 그런 경험을 할 수 있다면 정말 재밌을 것 같다는 생각이 들었다.**