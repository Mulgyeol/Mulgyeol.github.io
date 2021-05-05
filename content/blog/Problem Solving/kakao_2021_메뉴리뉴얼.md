---
title: '[Programmers] 2021 KAKAO BLIND RECRUITMENT - 메뉴 리뉴얼'
date: 2021-05-02 14:04:57
category: Problem Solving
thumbnail: { thumbnailSrc }
draft: false
---

![img](./images/programmers.png)

# [Programmers] 2021 KAKAO BLIND RECRUITMENT - 메뉴 리뉴얼

## 내 아이디어

- 21개의 Map<String, Integer>[] map = new HashMap[21] 배열을 생성합니다.
- 각 사람의 주문메뉴를 오름차순으로 일단 정렬시킵니다.
- 각 사람의 주문메뉴를 가지고 부분집합을 만듭니다.
- N개(2개 이상) 주문한 메뉴는 map[N]에 넣는다.
  - 이미 있다면 갯수 1증가 시킵니다.
  - 없다면 value를 1로 추가합니다.
- course에 있는 값들을 map배열의 인덱스로 탐색합니다.
  - key들을 Set 자료구조로 받아옵니다.
  - key의 value를 탐색하면서 최대값을 찾습니다.
  - 최대값과 같은 value를 가진 key들을 PriorityQueue pq에 넣습니다.
- pq의 사이즈만큼 String 배열을 만듭니다.
  - poll을 해주면서 String 배열을 채웁니다.
- String 배열을 return 합니다.

## 정답

![](https://images.velog.io/images/mulgyeol/post/5efaeab2-1a28-41a0-8641-8b3bdceea5a4/image.png)

## Java Code

```Java
import java.util.HashMap;
import java.util.Map;
import java.util.PriorityQueue;
import java.util.Set;
import java.util.Arrays;

class Solution {

    static Map<String, Integer>[] map;

    public String[] solution(String[] orders, int[] course) {
        PriorityQueue<String> pq = new PriorityQueue<String>();

		int people = orders.length;
        for(int i=0; i<people; i++) {
			char[] stringToChar = orders[i].toCharArray();
			Arrays.sort(stringToChar);
			orders[i] = new String(stringToChar);
		}

		map = new HashMap[21];

		for(int i=2; i<21; i++) {
			map[i] = new HashMap<>();
		}

		for(int i=0; i<people; i++) {
			int menuCnt = orders[i].length();
			boolean[] check = new boolean[menuCnt];
			SubSet(orders[i], 0, 0, menuCnt, check);
		}

		for(int i : course) {
            if(map[i].isEmpty()) continue;

			Set<String> keys = map[i].keySet();
			int max = 0;
			for(String s : keys) {
				if(map[i].get(s) > max) {
					max = map[i].get(s);
				}
			}

			if(max < 2) continue;

			for(String s : keys) {
				if(map[i].get(s) == max) {
					pq.offer(s);
				}
			}
		}

		int size = pq.size();
		String[] answer = new String[size];
		for(int i=0; i<size; i++) {
			answer[i] = pq.poll();
		}

		return answer;
    }

    private static void SubSet(String menu, int start, int cnt, int menuCnt, boolean[] check) {
		if(start == menuCnt) {
			if (cnt < 2) return;

			StringBuilder sb = new StringBuilder();
			for(int i=0; i<menuCnt; i++) {
				if(check[i]) {
					sb.append(menu.charAt(i));
				}
			}

			String combMenu = sb.toString();
			if(map[cnt].containsKey(combMenu)) {
				map[cnt].replace(combMenu, map[cnt].get(combMenu)+1);
			}else {
				map[cnt].put(combMenu, 1);
			}
			return;
		}

		check[start] = true;
		SubSet(menu, start+1, cnt+1, menuCnt,check);
		check[start] = false;
		SubSet(menu, start+1, cnt, menuCnt,check);

	}
}
```
