---
title: '[Programmers] 2021 KAKAO BLIND RECRUITMENT - 합승 택시 요금'
date: 2021-05-04 14:04:57
category: Problem Solving
thumbnail: { thumbnailSrc }
draft: false
---

![img](./images/programmers.png)

# [Programmers] 2021 KAKAO BLIND RECRUITMENT - 합승 택시 요금

## 아이디어

- 플루이드-와샬 알고리즘을 이용해서 각 구간별 요금을 미리 계산해둡니다.
- 시작점에서 각각의 목적지까지 가는 요금을 기본값으로 두고,
- 경유지를 다 돌면서 출발지에서 경유지 + 경유지에서 목적지 a + 경유지에서 목적지 b 이렇게 각자 가는 요금과 비교해서 더 저렴한 요금있다면 답으로 갱신합니다.

## 정답(효율성 100)

![](https://images.velog.io/images/mulgyeol/post/dbc99e72-918b-44fe-ae68-759376cd5bdd/image.png)
![](https://images.velog.io/images/mulgyeol/post/7436103a-abae-4853-8ad3-1e1acf0921d2/image.png)
![](https://images.velog.io/images/mulgyeol/post/c4ef4552-5fac-4064-9278-d1926f9755c9/image.png)

## Java Code

```Java
import java.util.Arrays;

class Solution {
    public int solution(int n, int s, int a, int b, int[][] fares) {
        int road = fares.length;
		int[][] reFares = new int[n+1][n+1];
		for(int i=0; i<n+1; i++) {
			Arrays.fill(reFares[i], 1000001);
		}
		for(int i=1; i<n+1; i++) {
			reFares[i][i] = 0;
		}

		for(int i=0; i<road; i++) {
			reFares[fares[i][0]][fares[i][1]] = fares[i][2];
			reFares[fares[i][1]][fares[i][0]] = fares[i][2];
		}

		for(int i=1; i<n+1; i++) {//경
			for(int j=1; j<n+1; j++) {//출
				for(int k=1; k<n+1; k++) {//도
					if(reFares[j][k] > reFares[j][i]+reFares[i][k]) {
						reFares[j][k] = reFares[j][i]+reFares[i][k];
					}
				}
			}
		}

		int answer = reFares[s][a]+reFares[s][b];
		for(int i=1; i<n+1; i++) {//경
			if(answer > reFares[s][i] + reFares[i][a] + reFares[i][b]) {
				answer = reFares[s][i] + reFares[i][a] + reFares[i][b];
			}
		}

        return answer;
    }
}

```
