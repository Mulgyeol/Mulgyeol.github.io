---
title: '[Algorithm] 순열(Permutation)을 생성하는 방법 3 - 비트마스킹(bit masking)'
date: 2021-04-16 16:04:47
category: 'Algorithm'
---

![img](./images/Thumbnail.png)

## **순열(Permutation)을 생성하는 방법 4가지**

1\. 반복문을 통한 순열 생성  
2\. 재귀를 통한 순열 생성  
3\. 비트마스킹을 통한 순열 생성 - 비트연산자를 사용  
4\. 현 순열에서 사전 순으로 다음 순열 생성 - NextPermutation

순열을 생성하는 방법은 대표적으로 위의 네 가지가 있다.
1번 반복문을 통한 순열 생성과
2번 재귀를 통한 순열 생성은 익숙한 내용이니 건너뛰고,
3번과 4번을 정리해보자.

---

# **순열(Permutation)을 생성하는 방법 3. 비트마스킹을 통한 순열 생성**

### **그래서 비트마스킹이 뭔데?**

집합의 요소들의 구성요소를 비트를 이용해서 표현하는 테크닉이다.

예를 들어, `[1,2,3]`라는 배열이 있다고 해보자.
여기서 1과 3은 포함하고 2를 포함하지 않는다는 것을 어떻게 표현할 수 있을까?

같은 길이의 boolean 배열을 생성하고 1과 3에 해당하는 인덱스에는 true값을, 2의 인덱스에는 false 값을 줄 수 있다.

또 다른 방법으로는 bit를 이용할 수 있다.

간단하게 이진수 101로 표현하면 된다.
응? 뭔 소리지? 싶다면
1은 true로 0은 false로 생각하면 된다.

'아, 포함시키면 해당 자리에 1로, 아니면 0로 표시하는구나.'라고 이해했다면 맞다.

집합의 요소들의 구성요소를 비트를 이용해서 표현하는 테크닉이다. 라는 첫 문장이 이제는 조금 이해가 되었을 것이다.
아니 그러면 이건 부분집합만 나타낼 수 있는거 아닌가?

이제부터 이걸로 어떻게 순열을 생성하는지 알아보려고 한다.
그 전에, 먼저 비트 연산자를 한 번 살펴보자.

<div align="center">
  <비트연산자><br>
<table align = "center" style="border-collapse: collapse;" border="1">
<tbody>
<tr>
<td style="width: 20%; text-align: center;">연산자</td>
<td style="width: 80%; text-align: center;">연산자의 기능</td>
</tr>
<tr>
<td style="width: 20%; text-align: center;"><span style="color: #333333;">&amp;</span></td>
<td style="width: 80%; text-align: center;"><span style="color: #333333;">비트단위로 AND 연산을 한다.</span><br>
<span style="color: #333333;">예) num1 &amp; num2</span>
</td>
</tr>
<tr>
<td style="width: 20%; text-align: center;">|</td>
<td style="width: 80%; text-align: center;">
<span style="color: #333333;">비트단위로 OR 연산을 한다.</span><br>
<span style="color: #333333;">예) num1 | num2</span>
</td>
</tr>
<tr>
<td style="width: 20%; text-align: center;">^</td>
<td style="width: 80%; text-align: center;">
<span style="color: #333333;">비트단위로 XOR 연산을 한다.</span><br>
<span style="color: #333333;">예) num1 ^ num2</span>
</td>
</tr>
<tr>
<td style="width: 20%; text-align: center;">~</td>
<td style="width: 80%; text-align: center;">
<span style="color: #333333;">다한 연산자로서 피연산자의 모든 비트를 반전시킨다.</span><br>
<span style="color: #333333;">예) ~num1</span>
</td>
</tr>
<tr>
<td style="width: 20%; text-align: center;">&lt;&lt;</td>
<td style="width: 80%; text-align: center;">
<span style="color: #333333;">피연산자의 비트 열을 왼쪽으로 이동시킨다.</span><br>
<span style="color: #333333;">예) num1 &lt;&lt; 2</span><br>
<span style="color: #333333;">비는 오른쪽 비트를 0으로 채운다</span>
</td>
</tr>
<tr>
<td style="width: 20%; text-align: center;">&gt;&gt;</td>
<td style="width: 80%; text-align: center;">
<span style="color: #333333;">피연산자의 비트 열을 오른쪽으로 이동시킨다.</span><br>
<span style="color: #333333;">예) num1 &gt;&gt; 2</span><br>
<span style="color: #333333;">비는 왼쪽 비트를 부호 비트로 채운다.</span>
</td>
</tr>
<tr>
<td style="width: 20%; text-align: center;">&gt;&gt;&gt;</td>
<td style="width: 80%; text-align: center;">
<span style="color: #333333;">피연산자의 비트 열을 오른쪽으로 이동시킨다.</span><br>
<span style="color: #333333;">예) num1 &gt;&gt;&gt; 2</span><br>
<span style="color: #333333;">비는 왼쪽 비트를 0으로 채운다.</span>
</td>
</tr>
</tbody>
</table>
</div>

이 비트 연산자는 공부할 때 한 번쯤 들어보긴 했지만,
아직까지 알고리즘 문제를 풀면서 써본 기억이 많이 없다.
아무튼, 저런것들이 있구나 하고 확인했다면, 다음 단계로 넘어가보자.
저 표에서 사용할 연산자는 &, |, << 이다.

확실히 알지 못한다싶으면 다시 한 번 확인하고 오자.

---

### **비트마스킹을 통한 순열 구현**

Java로 한 번 구현해 보자.

input이라는 int배열에 N개의 원소를 입력받아 저장했다.
그리고 이 배열의 구성요소들을 모두 포함하는 순열을 만들어보려고 한다.
순열을 생성하는 함수의 이름을 permutation이라고 하자.

이 함수는 매개변수로 int 타입 변수인 cnt와 flag를 받는다.
cnt는 순열에 구성원소를 하나씩 늘려가면서 N개까지 담아가는 변수이다.
flag는 비트마스킹을 할 이진수이다.

함수의 호출은 permutation(0,0);으로 한다.
이제 구성요소들을 담을 것이므로 cnt = 0, flag = 0 이 들어간다.
구성요소들을 담을 때마다 cnt를 1씩 증가시키고,
flag에 변화를 주어 permutation 함수를 재귀적으로 호출할 것이다.

```
private static void permutation(int cnt, int flag) {

}
```

이 함수의 역할은 무엇일지 곰곰히 생각해보자.
N개 중에서 N개를 뽑는 순열은 결국, 모든 원소를 선택하지만
순서가 중요하다.

따라서, 순서에 변화를 줘야한다.

이 순서에 변화를 주는 것을 재귀적으로 구현할 예정이다.
어떻게? 비트마스킹을 이용해서.

먼저, 반복문을 이용해서 순열의 가장 첫 번째 원소를 선택해보자.
어떻게? 비트마스킹을 이용해서.

이 과정은 두 단계로 나눌 수 있다.

1\. 해당 인덱스의 원소가 선택되었는지 flag를 확인한다.
2\. 해당 인덱스의 원소를 선택하고, flag에 표시한다.

---

### **1\. 해당 인덱스의 원소가 선택되었는지 flag를 확인한다.**

1번 과정은 어떻게 할 수 있을까?
예를 들어 `[3,1,2]`이라는 배열이 있고,
지금 만들고 있는 중인 순열을 의미하는 flag가 100이다.
그렇다면 지금 만들고 있는 순열에는 원소 '3'만이 포함되어 있는 상태이다.

이 3이 포함되어있다는걸 어떻게 알 수 있을까?
100에서 오른쪽에서부터 3번 째 자리, 2의 2승자리가 1인지 확인하면 될 것이다.

확인하는 방법은 다음과 같다.

```
if((flag & 1<<2) != 0)
```

하나씩 해석해보면, flag와 1<<2가 & 연산을 한다.

1<<2 는 100이 될 것이고,
& 연산을 통해,
flag의 2의 2승 자리가 1이라면, (flag & 100)은 100이,
flag의 2의 2승 자리가 0이라면, 0이 될것이다.
즉, 0이 아니면 선택된 것이다.
0이면 선택되지 않은것이다.

위의 확인 과정은 순열을 생성하는 도중 중복을 피하기 위해서 사용될 것이다.

---

### **2\. 해당 인덱스의 원소를 선택하고, flag에 표시한다.**

이 과정은 어떻게 이루어질까?
먼저, 1번의 과정을 통해 해당 인덱스의 요소가 선택되지 않았다는 것을 확인한 상태라고 가정한다.

그렇다면 flag에 어떻게 1을 집어넣어 선택했다고 표시할 수 있을까?
현재 `[3,1,2]`라는 배열에서 3만을 포함하고 있고, flag는 100이라고 해보자.

나는 지금 '1'라는 원소를 선택하고 싶다.
그렇다면 방법은 flag = flag | 1<<1 이다.
1<<1는 감을 잡았을 것이다.
1을 왼쪽으로 한 칸 옮기고 오른쪽의 빈자리는 0을 채우기 때문에 10이라는 이진수가 된다.
현재 flag인 100과 10의 '|' 연산은 값이 110이다.
결국, flag가 110이 되면서 3이라는 원소, 2라는 원소가 선택되었음을 표시했다.

---

## **진짜로 순열을 만들어보자.**

위의 두 과정을 이해했다면 다음 단계는
순서를 바꾸면서 순열을 만드는 것이다.
각 순열은 크기가 N인 배열 numbers에 재귀형태로 완성되어 출력하는 것을 반복할 것이다.

먼저, cnt =0, flag = 0값을 매개변수로 permutation 함수가 처음 호출되면,
첫 번째 순서로 올 원소를 선택해야한다.
이 과정은 반복문을 N번 돌면서,
0부터 N-1의 인덱스에 해당하는 원소를 첫 번째 순서로 각각 넣어
다음 두 번째 순서로 올 원소를 선택하는 permutation을 재귀적으로 호출한다.

현재 cnt = 0이므로, numbers[cnt]에 input의 해당 인덱스의 원소를 집어 넣으면 첫 번째 순서가 된다.

```
private static void permutation(int cnt, int flag) {
	for(int i=0; i<N; i++) {
		numbers[cnt] = input[i];
		permutation(cnt+1, flag | 1<<i);
	}
}
```

두 번째 호출부터는 반복문을 N번 돌면서 첫 번째 호출과 같은 일을 하지만, 첫 번째 순서로 선택된 원소는 선택하면 안된다는 차이점이 있다.

이 과정에서 _1\. 해당 인덱스의 원소가 선택되었는지 flag를 확인한다._ 가 사용된다.

flag &  1 << i 를 통해서 해당 원소가 선택되었는지 확인하고,
선택 되었다면 다시 반복문으로 돌아가 다음 인덱스를 확인한다.

첫 번째 호출에서는 flag가 0이므로 확인할 필요가 없지만,
두 번째 호출부터는 계속해서 확인을 해줘야 하기때문에
이 확인 과정을 반복문 내부에 넣어준다.

```
private static void permutation(int cnt, int flag) {
	for(int i=0; i<N; i++) {
		if((flag & 1<<i) != 0) continue;

		numbers[cnt] = input[i];
		permutation(cnt+1, flag | 1<<i);
	}
}
```

마지막으로, 종료조건을 생각해보자.

cnt는 순열에 선택된 원소의 갯수를 의미한다.
따라서 cnt가 N이 되었을 때,
완성된 순열로 어떤 동작을 하도록 하고, 재귀호출을 종료한다.

이번 구현에서는 순열을 출력하고 재귀 호출을 종료시켜보자.

```
private static void permutation(int cnt, int flag) {
	if(cnt == N) {
		System.out.println(Arrays.toString(numbers)); // 완성된 순열 numbers로 취할 동작
		return;
	}

	for(int i=0; i<N; i++) {
		if((flag & 1<<i) != 0) continue;

		numbers[cnt] = input[i]; // cnt자리부터 들어가서 순서가 결정됨
		permutation(cnt+1, flag | 1<<i); //선택한 인덱스 1로 마킹, 결국 다 111111...이 됨
	}
}
```

완성되었다.

다음은 전체 코드와 입출력 결과를 확인해보자.

```
import java.util.Arrays;
import java.util.Scanner;

public class PermutationBitmasking {
	static int N;
	static int[] input, numbers;

	public static void main(String[] args) {
		Scanner sc = new Scanner(System.in);
		N = sc.nextInt(); // 원소의 갯수,
		input = new int[N]; // 원소들을 저장할 배열
		numbers = new int[N]; // 순열을 저장할 배열

		for(int i =0; i<N; i++) {
			input[i] = sc.nextInt(); // 원소들을 저장
		}

		System.out.println("===============permutation================");

		permutation(0, 0); // 순열을 생성하는 함수 호출
		sc.close();
	}


	private static void permutation(int cnt, int flag) {
		if(cnt == N) {
			System.out.println(Arrays.toString(numbers)); // 완성된 순열로 취할 동작
			return;
		}

		for(int i=0; i<N; i++) {
			if((flag & 1<<i) != 0) continue;

			numbers[cnt] = input[i]; // cnt자리부터 들어가서 순서가 결정됨
			permutation(cnt+1, flag | 1<<i); //선택한 인덱스 1로 마킹, 결국 다 111111...이 됨
		}
	}

}
```

입력을 
4
6 3 4 1 로 주면,

![](https://images.velog.io/images/mulgyeol/post/c9a9017f-7267-41e9-8ceb-4142018d31ee/img1.png)

순열이 잘 생성되어 출력되는 것을 확인할 수 있다.
안익숙해서 그렇지
익숙해지면 의외로
간단한 방법일지도 모르겠다.

---

### +) nPn 말고, nPr은??

n개 중에 r개를 뽑아 만드는 순열은,
numbers의 크기를 r로 하고,
종료 조건을 cnt == r로 주면 가능하다.

```
package com.ssafy.exhaustive;

import java.util.Arrays;
import java.util.Scanner;

public class P3_PermutationBitmaskTest {
	static int N,R;
	static int[] input, numbers;
	static boolean[] isSelected;

	public static void main(String[] args) {
		Scanner sc = new Scanner(System.in);
		N = sc.nextInt();
		R = sc.nextInt();
		input = new int[N];
		numbers = new int[R];

		for(int i =0; i<N; i++) {
			input[i] = sc.nextInt();
		}

		System.out.println("===============permutation================");

		permutation(0, 0);
		sc.close();
	}


	private static void permutation(int cnt, int flag) {
		if(cnt == R) {
			System.out.println(Arrays.toString(numbers));
			return;
		}

		for(int i=0; i<N; i++) {
			if((flag & 1<<i) != 0) continue;

			numbers[cnt] = input[i]; // cnt자리부터 들어가서 순서가 결정됨
			permutation(cnt+1, flag | 1<<i); //선택한 인덱스 1로 마킹, 결국 다 111111...이 됨
		}
	}

}

```

![](https://images.velog.io/images/mulgyeol/post/8935a862-99ef-4875-af68-53adcaccd20f/img2.png)

## 끝!

---
