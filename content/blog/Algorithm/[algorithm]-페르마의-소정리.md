---
title: [Algorithm] 페르마의 소정리
date: 2021-04-19 20:04:28
category: algorithm
thumbnail: { thumbnailSrc }
draft: false
---

![img](./images/Thumbnail.png)

# 페르마의 소정리, Fermat’s little theorem

- 임의의 소수 p와 서로소인 한 수의 (p-1)승을 p로 나눈 나머지는 무조건 1이다.

## 들어가기 앞서

- 정수 -INF ~ INF 를 한 번에 다루기엔 시간과 메모리가 부족하다.
- 따라서, 큰 수를 다룰 때 mod 연산을 통해서 다루도록 한다.
- 특정 수를 m으로 mod 연산을 거치면 나올 수 있는 가지 수는 0부터 m-1까지 총 m개이다.

## 페르마의 소정리

- a^p ≡ a(mod p)
- a^p-1 ≡ 1(mod p)
- a^p-2 ≡ 1/a(mod p)
- a와 p는 서로소, p는 소수
- `≡` : 합동

> 8^202의 mod 41 값을 구해라.
> a=8, p=41이라고 하자,
>
> a^p-1(mod p) ≡ 1
> 8^40(mod 41) ≡ 1
> 8^202 = ((8^40)*5)*8^2
> 8^202 ≡ 8^2(mod 41) 로 풀 수 있다.
>
> 답 : 24

## 페르마의 소정리를 이용한 이항계수 문제

- [SWEA 5607](https://swexpertacademy.com/main/code/problem/problemDetail.do?contestProbId=AWXGKdbqczEDFAUo)

### 아이디어

- nCr = n! / ((n-r)! \* r!)
- 분모인 `((n-r)! * r!)`를 B라고 하자.
- 그렇다면 nCr = n!\*B^-1
- 페르마의 소정리에 의해 B^-1 ≡ B^p-2 (mod p)

```Java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class SWEA_5607_Combination {
	static long[] factorialDP;
	static int P = 1234567891;

	public static void main(String[] args) throws NumberFormatException, IOException {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		StringTokenizer st;
		StringBuilder sb = new StringBuilder();
		int T = Integer.parseInt(br.readLine());
		for(int t=1; t<=T; t++) {
			sb.append("#").append(t).append(" ");
			st = new StringTokenizer(br.readLine(), " ");
			int N = Integer.parseInt(st.nextToken());
			int R = Integer.parseInt(st.nextToken());

			//factorial 미리 구해놓기
			factorialDP = new long[N+1];
			factorialDP[1] = 1;

			//수가 커질 수 있으니 미리 P로 나눈 나머지를 넣어놓는다.
			for(int i=2; i<=N; i++) {
				factorialDP[i] = (factorialDP[i-1] *i) % P;
			}

			//분모 부분
			long bottom = (factorialDP[N-R] * factorialDP[R]) % P;

			//거듭제곱 분할정복으로 빠르게 구하는 함수.
			long B = pow(bottom, P-2);

			long answer = (factorialDP[N] * B) % P;

			sb.append(answer).append("\n");
		}

		System.out.print(sb);

	}

	private static long pow(long a, int b) { // a의 b승
		if(b == 0) return 1;
		else if(b == 1) return a;


		if(b%2 == 0) { // b가 짝수인 경우
			long tmp = pow(a, b/2);
			return (tmp * tmp) % P;
		}

		//b가 홀수인 경우
		long tmp = pow(a,b-1);
		return (tmp * a) % P;
	}

}
```
