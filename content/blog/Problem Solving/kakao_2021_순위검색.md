---
title: '[Programmers] 2021 KAKAO BLIND RECRUITMENT - 순위 검색'
date: 2021-05-03 14:04:57
category: Problem Solving
thumbnail: { thumbnailSrc }
draft: false
---

![img](./images/programmers.png)

# [Programmers] 2021 KAKAO BLIND RECRUITMENT - 순위 검색

## 아이디어

- 주어진 info와 query를 각각 String[][] 배열로 재정리합니다.
- 각 질문 별로, 해당하는 인원을 셉니다.
  - 일치 하지 않으면 다음 사람으로 넘어가면서 가지치기를 해줍니다.

## 효율성에 관해서

접근 자체는 어렵지 않게 했는데 역시나 효율성에서 0점이 나왔습니다.
어떻게 하면 효율성을 높일 수 있을까 고민을 좀 해봤는데, 아래 두 가지 방법이 떠올랐습니다.

1. 각 사람별 데이터를 비트마스킹으로 표시한 다음 쿼리 자체도 비트마스킹 인트로 바꿔줘서 일치하는 사람을 바로 찾을 수 있게 하는 방법
2. 집합..? 서로소..? 개념으로 접근하는 방법

2번 같은 경우는 어렴풋이 이렇게 문제를 풀었던 게 기억이 나긴 하는데, 구현 다시 할 수 있을지 모르겠습니다.

## 정답(효율성 0)

![](https://images.velog.io/images/mulgyeol/post/826ea5b9-c881-44e9-b895-47affb195c6e/image.png)

## Java Code

```Java
import java.util.StringTokenizer;

class Solution {
    public int[] solution(String[] info, String[] query) {
        StringTokenizer st;
		int people = info.length;
		String[][] reInfo = new String[people][5];
		for(int i=0; i<people; i++) {
			st = new StringTokenizer(info[i], " ");
			for(int j=0; j<5; j++) {
				reInfo[i][j] = st.nextToken();
			}
		}

		int queryNum = query.length;
		String[][] reQuery = new String[queryNum][5];
		for(int i=0; i<queryNum; i++) {
			st = new StringTokenizer(query[i], " ");
			for(int j=0; j<4; j++) {
				if(j != 3) {
					reQuery[i][j] = st.nextToken();
					st.nextToken();
				}
				else {
					reQuery[i][j] = st.nextToken();
					reQuery[i][j+1] = st.nextToken();
				}
			}
		}

		int[] answer = new int[queryNum];
		for(int i=0; i<queryNum; i++) {//질문 수
			int cnt = 0;
			for(int j=0; j<people; j++) {// 사람 수
				boolean flag = true;
				for(int k=0; k<4; k++) {//질문 가짓수
					if(reQuery[i][k].equals("-")) continue;
					if(!reQuery[i][k].equals(reInfo[j][k])) {
						flag = false;
						break;
					}
				}
				if(flag == true && Integer.valueOf(reQuery[i][4]) <= Integer.valueOf(reInfo[j][4])) {
					cnt++;
				}
			}
			answer[i] = cnt;
		}

        return answer;


    }
}

```
