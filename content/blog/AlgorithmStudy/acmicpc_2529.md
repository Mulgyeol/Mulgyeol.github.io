---
title: '[백준 - 2529] 부등호 - Java'
date: 2020-04-21 14:01:00
category: 'Algorithm Study'
---

![img](./images/BAEKJOON.png)

# [백준 - 2529] 부등호 - (Algorithm Study - 9주차)

[문제 바로가기](https://www.acmicpc.net/problem/2529)

## 아이디어

### 부등호 정보 저장 담기

- boolean 배열 sign 에 부등호를 담는다
- ">"면 true
- "<"면 false

### 숫자 순열 생성

- 0부터 9까지 숫자 중에서 N+1개를 뽑는 순열을 만들어 배열에 채운다.

### 가지치기

- 순열을 만들어 나가면서 부등호를 만족하지 못하는 것은 바로 버리는 가지치기를 해준다.

### 완성된 순열 숫자화

- 부등호를 다 만족하는 N+1개의 숫자를 뽑으면 배열에서 숫자를 뽑아 수로 만든다.
- `int number = 0;` 을 선언하고 하나씩 뽑아 더해준 다음 10을 곱해준다.
- 4.2 반복문이 종료되면 마지막엔 10으로 나눠준다.

### 최댓값과 최솟값

- 순열이 완성될 때마다 최대값,최소값과 비교하면서 갱신해준다.
- 5.1 최소값과 최대값을 출력하기 전에 자릿수를 구해서 N+1이 아니라 N이면 앞에 '0'이 빠진 것이므로 '0'을 추가해준다. -> 스트링으로 바꿔서 앞에 0을 더해준다.

## 디버깅

1. **첫 번째 시도에서 6%에서 실패**

   - 극단적인 경우에서 테스트 해본다.
   - 9 < < < < < < < < < : 통과
   - 9 > > > > > > > > > : 실패
     - 원인 : 9876543210 은 int의 범위를 넘어선다.
     - 해결 : long으로 자료형을 바꿔준다.

2. **두 번째 시도에서 6%에서 실패**
   - 9 > > > > > > > > > : 실패
   - 원인 : 최소값과 최대값을 선언할 때, Integer.MAX_VALUE와 Integer.MIN_VALUE를 사용했다.
   - 해결 : Long.MAX_VALUE와 Long.MIN_VALUE로 바꿔줬다.

## 정답

![](https://images.velog.io/images/mulgyeol/post/770ce2e6-2113-47c0-8eb6-a0b78c38202a/image.png)

## 코드

```Java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;


public class acmicpc_2529_InequalitySign {
	static int N;
	static long min = Long.MAX_VALUE, max = Long.MIN_VALUE;
	static int[] num;
	static boolean[] sign;
	static boolean[] visited;
	static String maxStr, minStr;

	public static void main(String[] args) throws IOException {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		StringTokenizer st;

		N = Integer.parseInt(br.readLine());
		st = new StringTokenizer(br.readLine(), " ");
		sign = new boolean[N];
		visited = new boolean[10];
		num = new int[N+1];

		for(int i=0; i<N; i++) {
			if(st.nextToken().equals(">")) sign[i] = true;
			else sign[i] = false;
		}

		permutation(0);
		maxStr = Long.toString(max);
		if(maxStr.length() == N)
			maxStr = "0"+maxStr;
		minStr = Long.toString(min);
		if(minStr.length() == N)
			minStr = "0"+minStr;

		System.out.println(maxStr);
		System.out.println(minStr);
	}

	private static void permutation(int cnt) {
		if(cnt >= 2) {
			if(sign[cnt-2] == true) { // >
				if(num[cnt-2] < num[cnt-1]) return;
			}else {// <
				if(num[cnt-2] > num[cnt-1]) return;
			}
		}

		if(cnt == N+1) {
			long number = 0;

			for(int i=0; i<N+1; i++) {
				number += num[i];
				number *= 10;
			}

			number /= 10;

			if(number > max) max = number;
			if(number < min) min = number;

			return;
		}


		for(int i=0; i<10; i++) {
			if(!visited[i]) {
				num[cnt] = i;
				visited[i] = true;
				permutation(cnt+1);
				visited[i] = false;
			}
		}
	}

}


```
