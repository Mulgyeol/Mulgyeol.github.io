---
title: '[백준 2805] 나무자르기'
date: 2021-05-14 00:05:90
category: problem solving
thumbnail: { thumbnailSrc }
draft: false
---

![img](./images/BAEKJOON.png)

# [백준 2805] 나무자르기

## 첫 아이디어

나무들을 내림차순으로 정렬한 다음, 위부터 깎아 내려가면서 깎은 갯수를 센다.
가장 컸던 나무(0번 인덱스)가 그 다음으로 컸던 나무(1번 인덱스)보다 작아지면 타겟 인덱스를 1 증가시킨다.
타겟 인덱스가 0이 아닐 경우, 타겟 인덱스의 나무가 그 다음 인덱스의 나무보다 크다면 타겟을 0번으로 돌린다.

원하는 만큼의 나무를 얻을 경우, 0번째 인덱스의 나무 길이가 절단기의 최소 높이가 된다.

### Java Code

```Java
package algo.study.thisweek;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.Arrays;
import java.util.Collections;
import java.util.StringTokenizer;

public class Acmicpc_2805_나무자르기 {

	public static void main(String[] args) throws IOException {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		StringTokenizer st = new StringTokenizer(br.readLine(), " ");

		int N = Integer.valueOf(st.nextToken()); // 나무의 수
		int M = Integer.valueOf(st.nextToken()); // 최대 나무길이

		Integer[] trees = new Integer[N];

		st = new StringTokenizer(br.readLine(), " ");
		for(int i=0; i<N; i++) {
			trees[i] = Integer.valueOf(st.nextToken());
		}

		Arrays.sort(trees, Collections.reverseOrder());

		int answer = 0;
		int cnt = 0;
		int pointer = 0;
		if(N == 1) {
			answer = trees[0] - M;
		}else {
			while(cnt < M) {
				if(pointer == 0) {
					if(trees[pointer] >= trees[pointer+1]) {
						trees[pointer]--;
						cnt++;
					}else {
						pointer++;
					}
				}else {
					if(trees[pointer-1] < trees[pointer]) {
						trees[pointer]--;
						cnt++;
					}else {
						if(trees[pointer] < trees[pointer+1]) {
							pointer++;
						}else {
							pointer = 0;
						}
					}
				}
			}

			answer = trees[0];
		}

		System.out.println(answer);
	}

}
```

### 시간 초과

![](https://images.velog.io/images/mulgyeol/post/a2420b11-6b69-468f-9ebe-929aaace8b01/image.png)

## 이진 탐색으로 접근하기

나무를 1씩 깎아 내린다고 생각하지 않고, 절단기의 길이를 가지고 이분 탐색을 해본다.
제일 높은 나무를 기준으로 중간값을 절단기 높이(mid)로 처음 확인하고,
얻어지는 값이 많거나 같으면 start를 mid+1 높인다.
start가 높아지면 절단기 높이가 높아지고, 얻는 나무 길이가 줄어든다.
얻어지는 값이 적으면 end를 mid-1로 줄인다.

### 이진 탐색(Binary Search)

- 자료의 가운데에 있는 항목의 키 값과 비교하여 다음 검색의 위치를 결정하고 검색을 계속 진행하는 방법
  - 목적 키를 찾을 때까지 이진 검색을 순환적으로 반복 수행함으로써 검색 범위를 반으로 줄여가면서 보다 빠르게 검색을 수행함
- 이진 탐색을 하기 위해서는 자료가 정렬된 상태여야 한다.
- 검색 과정
  1. 자료의 중앙에 있는 원소를 고른다.
  2. 중앙 원소의 값과 찾고자 하는 목표값을 비교한다.
  3. 중앙 원소의 값과 찾고자 하는 목표값이 일치하면 탐색을 끝낸다.
  4. 목표 값이 중앙 원소의 값보다 작으면 자료의 왼쪽 반에 대해서 새로 검색을 수행하고, 크다면 자료의 오른쪽 반에 대해서 새로 검색을 수행한다.
  5. 찾고자 하는 값을 찾을 때까지 위의 과정을 반복한다.

### 에러

얻어지는 나무 길이가 많으면 int범위를 초과할 수 있다.
long 타입으로 바꿔주니 통과됐다.

### Java Code

```Java
package algo.study.thisweek;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class Acmicpc_2805_나무자르기 {

	public static void main(String[] args) throws IOException {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		StringTokenizer st = new StringTokenizer(br.readLine(), " ");

		int N = Integer.valueOf(st.nextToken()); // 나무의 수
		int M = Integer.valueOf(st.nextToken()); // 최대 나무길이
		int max = Integer.MIN_VALUE;
		Integer[] trees = new Integer[N];

		st = new StringTokenizer(br.readLine(), " ");
		for(int i=0; i<N; i++) {
			trees[i] = Integer.valueOf(st.nextToken());
			if(trees[i] > max) max = trees[i];
		}

		int start = 0; // 절단기 높이 값찾기의 시작
		int end = max; // 절단기의 최대 높이 = 나무의 최대 높이
		int answer = 0;

		while(start<=end) {
			int mid = (start+end) / 2; // 중간값을 절단기의 높이로!
			long sum = 0; // 얻어지는 나무길이
			for(int i=0; i<N; i++) {
				if(trees[i] > mid) {
					sum += trees[i] - mid;
				}
			}

			if(sum >= M) {
				start = mid+1;
				answer = mid;
			}else {
				end = mid-1;
			}

		}

		System.out.println(answer);
	}
}
```

### 정답

![](https://images.velog.io/images/mulgyeol/post/83c740b9-17ce-4057-9b8b-e49e5373879e/image.png)

### 재귀 이분탐색 Code

```Java
static int[] list = {1, 3, 5, 7, 9, 11, 14, 15, 18, 19, 25, 28};

// mid = 찾을 값의 인덱스
int search(int start, int end, int target) {
	if(start > end) return -1; //못찾음
	int mid = (start + end) / 2;
	if(list[mid] == target) return mid;
	else if(list[mid] > target) return search(start, mid - 1, target);
	else return search(mid + 1, end, target);
}
```
