---
title: '[백준 - 21608] 상어 초등학교 - Java'
date: 2021-04-27 22:04:54
category: problem solving
thumbnail: { thumbnailSrc }
draft: false
---

![img](./images/BAEKJOON.png)

# [백준 - 21608] 상어 초등학교 - Java

[문제 바로가기](https://www.acmicpc.net/problem/21608)

## 들어가기전에

삼성SW역량테스트에 출제된 문제로, 온라인 모각코원들의 추천을 받아 풀었다.

## 아이디어

### 클래스 선언

좌표 정보와 친한 친구 목록을 담은 Student 클래스를 만들어두어야 풀이가 수월할 것이라고 판단했다.

한 사람의 좌석을 선택할 때 친한 친구들의 위치를 먼저 파악하기도 해야하고,
마지막에 정답을 출력할 때 만족도 계산을 위해서도 필요하다.

```Java
static class Student{
		int x;
		int y;
		int[] flist;

		public Student(int x, int y, int[] flist) {
			this.x = x;
			this.y = y;
			this.flist = flist;
		}
	}
```

### 정보를 담을 리스트

번호를 가지고, 학생들을 다루기 위해 Map을 사용해보기로 했다.

학생들의 리스트라는 뜻으로 변수명을 `list`로 지었는데, 괜찮은 건지 잘 모르겠다.

아무튼,
key값으로 학생번호가 들어가고, Student 객체를 value로 집어넣는다.
한 학생이 자리를 고르고 나면, `list`에 집어넣는다.

```Java
static Map<Integer, Student> list = new HashMap<>();
```

### nearEmptySeatCnt

본 격적으로 학생들의 자리를 선정하기 전에, 미리 [N][n]의 크기의 배열로
주변에 비어있는 자리가 얼마나 있는지를 저장해두었다.

```Java
private static void fillNearEmptySeat() {
		nearEmptySeatCnt = new int[N][N];

		for(int i=0; i<N; i++) {
			for(int j=0; j<N; j++) {
				int cnt = 4;
				if(i==0 || i==N-1) cnt--;
				if(j==0 || j==N-1) cnt--;
				nearEmptySeatCnt[i][j] = cnt;
			}
		}
	}
```

### 정보입력

StringTokenizer로 한 줄을 읽어들인다. 그 다음.
본격적으로 자리를 찾아갈 findSeat함수의 파라미터로 num과 int[] 를 넘겨준다.

int 배열은 자리찾기에서 이미 자리를 찾은 친한 친구가 있는지 찾는데 사용되고,
자리를 찾고나서 친구 목록을 담아 student 객체를 list에 넣을때도 사용된다.

```Java
for(int i=0; i<N2; i++) {
			st = new StringTokenizer(br.readLine(), " ");
			int num = Integer.valueOf(st.nextToken());
			int s1 = Integer.valueOf(st.nextToken());
			int s2 = Integer.valueOf(st.nextToken());
			int s3 = Integer.valueOf(st.nextToken());
			int s4 = Integer.valueOf(st.nextToken());

			findSeat(num, new int[] {s1,s2,s3,s4});
		}
```

### findSeat

2차원 int배열을 nearScore를 선언한다.

이 배열은 친한친구 자리가 이미 선정 되었다면 친구와 인접한 곳의 점수를 높여 우선적으로 선택할 수 있게 체크하는 배열이다.

먼저 넘겨받은 int배열을 가지고 list에 등록된(이미 자리를 찾은) 친구들이 있는지 확인한다.
그 친구들의 4방 중 빈곳의 점수를 높인다.

classroom은 교실을 의미하고 배열의 한칸은 책상을 나타낸다.

(0,0)부터 탐색하면서 nearScoreMax(nearScore 중 점수가 높은 곳)를 1순위로, 그 다음은 emptyCntMax(nearEmptySeatCnt 중 점수가 높은 곳)를 2순위로 적당한 자리를 찾아나간다.

자리를 찾은 이후에는 list에 집어넣고,
선정된 자리의 nearEmptySeatCnt 4방을 1씩 낮춰준다.

```Java
private static void findSeat(int num, int[] friends) {
		int[][] nearScore = new int[N][N]; //주변에 친한 친구가 많은 위치를 찾기 위한 배열
		for(int friend : friends) {
			if(list.containsKey(friend)) {
				Student student = list.get(friend);
				int x = student.x;
				int y = student.y;

				for(int i=0; i<4; i++) {
					int nx = x+dx[i];
					int ny = y+dy[i];
					if(nx>=0 && nx <N && ny >=0 && ny < N && classroom[nx][ny] == 0) {
						nearScore[nx][ny]++;
					}
				}
			}
		}

		int emptyCntMax = -1;
		int nearScoreMax = -1;
		int choiceX = -1;
		int choiceY = -1;

		for(int i=0; i<N; i++) {
			for(int j=0; j<N; j++) {
				if(classroom[i][j] != 0) continue;
				if(nearScoreMax < nearScore[i][j]) {
					choiceX = i;
					choiceY = j;
					nearScoreMax = nearScore[i][j];
					emptyCntMax = nearEmptySeatCnt[i][j];
				} else if(nearScoreMax == nearScore[i][j] && emptyCntMax < nearEmptySeatCnt[i][j]) {
					emptyCntMax = nearEmptySeatCnt[i][j];
					choiceX = i;
					choiceY = j;
				}
			}
		}

		classroom[choiceX][choiceY] = num;
		list.put(num, new Student(choiceX,choiceY, friends));

		for(int i=0; i<4; i++) {
			int nx = choiceX+dx[i];
			int ny = choiceY+dy[i];
			if(nx>=0 && nx <N && ny >=0 && ny < N && classroom[nx][ny] == 0) {
				nearEmptySeatCnt[nx][ny]--;
			}
		}
	}

```

### 만족도 검사

list에서 1번 학생부터 꺼내서 친한 친구들과의 거리를 잰다.
거리가 1이면 인접하므로 cnt를 1씩 증가시켜준다.
4명을 다돌아서 cnt값이 정해지면, 해당되는 만족도를 answer에 더해준다.

```Java
for(int i=1; i<=N2; i++) {
			Student student = list.get(i);
			int cnt = 0;
			for(int friend : student.flist) {
				if(Math.abs(list.get(friend).x -student.x) + Math.abs(list.get(friend).y - student.y) == 1) {
					cnt++;
				}
			}

			if(cnt==1) answer+=1;
			else if(cnt==2) answer+=10;
			else if(cnt==3) answer+=100;
			else if(cnt==4) answer+=1000;
		}


```

### 디버깅

테케는 맞는데, 제출하면 indexOutOfRange가 떴다.
classroom에 학생들이 제 자리로 들어갔는지 잘 확인하기 위해, 돌려보니
![](https://images.velog.io/images/mulgyeol/post/ffa6dd2f-107e-4ec9-9ac9-384e38e00162/image.png)

이렇게 나와야하는데,

![](https://images.velog.io/images/mulgyeol/post/ca8b6314-5d9c-45df-ba39-e27d5e8879b2/image.png)

이렇게 나왔다.

우선 순위대로 고르는 과정에서 nearScoreMax가 갱신된 시점에서 emptyCntMax도 그 값으로 갱신해줘야 했는데, 생각하지 못했다.
다행히 발견하는데 오래걸리진 않았고 제대로 넣어주니 바로 나왔다.

![](https://images.velog.io/images/mulgyeol/post/2198a3eb-3fd5-4f8f-9da8-da94407bcb05/image.png)

### 정답

![](https://images.velog.io/images/mulgyeol/post/8912af9c-70e6-4d0a-ada2-a6b6214cb60d/image.png)

### Java Code

```Java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.HashMap;
import java.util.Map;
import java.util.StringTokenizer;

public class acmicpc_21608_SharkElementarySchool {

	static int[][] classroom, nearEmptySeatCnt;
	static int N;
	static int[] dx = {-1,1,0,0};
	static int[] dy = {0,0,-1,1};

	static class Student{
		int x;
		int y;
		int[] flist;

		public Student(int x, int y, int[] flist) {
			this.x = x;
			this.y = y;
			this.flist = flist;
		}
	}

	static Map<Integer, Student> list = new HashMap<>();

	public static void main(String[] args) throws NumberFormatException, IOException {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		StringTokenizer st;

		N = Integer.valueOf(br.readLine());
		int N2 = N*N;
		int answer = 0;
		classroom = new int[N][N];
		fillNearEmptySeat();

		for(int i=0; i<N2; i++) {
			st = new StringTokenizer(br.readLine(), " ");
			int num = Integer.valueOf(st.nextToken());
			int s1 = Integer.valueOf(st.nextToken());
			int s2 = Integer.valueOf(st.nextToken());
			int s3 = Integer.valueOf(st.nextToken());
			int s4 = Integer.valueOf(st.nextToken());

			findSeat(num, new int[] {s1,s2,s3,s4});
		}

		for(int i=1; i<=N2; i++) {
			Student student = list.get(i);
			int cnt = 0;
			for(int friend : student.flist) {
				if(Math.abs(list.get(friend).x -student.x) + Math.abs(list.get(friend).y - student.y) == 1) {
					cnt++;
				}
			}

			if(cnt==1) answer+=1;
			else if(cnt==2) answer+=10;
			else if(cnt==3) answer+=100;
			else if(cnt==4) answer+=1000;
		}

		System.out.println(answer);

	}

	private static void findSeat(int num, int[] friends) {
		int[][] nearScore = new int[N][N]; //주변에 친한 친구가 많은 위치를 찾기 위한 배열
		for(int friend : friends) {
			if(list.containsKey(friend)) {
				Student student = list.get(friend);
				int x = student.x;
				int y = student.y;

				for(int i=0; i<4; i++) {
					int nx = x+dx[i];
					int ny = y+dy[i];
					if(nx>=0 && nx <N && ny >=0 && ny < N && classroom[nx][ny] == 0) {
						nearScore[nx][ny]++;
					}
				}
			}
		}

		int emptyCntMax = -1;
		int nearScoreMax = -1;
		int choiceX = -1;
		int choiceY = -1;

		for(int i=0; i<N; i++) {
			for(int j=0; j<N; j++) {
				if(classroom[i][j] != 0) continue;
				if(nearScoreMax < nearScore[i][j]) {
					choiceX = i;
					choiceY = j;
					nearScoreMax = nearScore[i][j];
					emptyCntMax = nearEmptySeatCnt[i][j];
				} else if(nearScoreMax == nearScore[i][j] && emptyCntMax < nearEmptySeatCnt[i][j]) {
					emptyCntMax = nearEmptySeatCnt[i][j];
					choiceX = i;
					choiceY = j;
				}
			}
		}

		classroom[choiceX][choiceY] = num;
		list.put(num, new Student(choiceX,choiceY, friends));

		for(int i=0; i<4; i++) {
			int nx = choiceX+dx[i];
			int ny = choiceY+dy[i];
			if(nx>=0 && nx <N && ny >=0 && ny < N && classroom[nx][ny] == 0) {
				nearEmptySeatCnt[nx][ny]--;
			}
		}
	}

	private static void fillNearEmptySeat() {
		nearEmptySeatCnt = new int[N][N];

		for(int i=0; i<N; i++) {
			for(int j=0; j<N; j++) {
				int cnt = 4;
				if(i==0 || i==N-1) cnt--;
				if(j==0 || j==N-1) cnt--;
				nearEmptySeatCnt[i][j] = cnt;
			}
		}
	}

}

```
