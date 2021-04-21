---
title: '[백준 - 19237] 어른상어 - Java'
date: 2020-04-21 14:03:00
category: 'Algorithm Study'
---

![img](./images/BAEKJOON.png)

# [백준 - 19237] 어른상어 - (Algorithm Study - 9주차)

[문제 바로가기](https://www.acmicpc.net/problem/19237)

## 들어가기전에

지난 스터디에서 [청소년 상어](https://www.acmicpc.net/problem/19236)에 참패를 당한 이후로 구현문제를 꽤 많이 풀었다. 정리를 제대로 안한 상태에서 구현에만 신경을 쓰다보니, 내가 짠 코드인데도 헷갈렸고 결국에 오류가 나는 원인을 찾을 수 없었다.

스터디원분 풀이를 참고해서 아이디어를 정리한 후 구현을 하고 나서는 구현문제를 꽤 많이 풀었다.

이번 어른 상어는 난이도가 더 낮긴 하지만 구현문제에 경험치가 쌓였는지 그렇게 오래 걸리지는 않았다.

## 아이디어

### 클래스 선언

경험적으로 상어는 각각 객체를 가지고 있어야 구현이 쉽다고 생각했다, 그리고 격자배열에는 냄새, 냄새를 뿜은 상어 정보를 가지고 있어야 했다.

처음엔 격자를 냄새 배열로 구현했고, 냄새와 냄새를 뿜은 상어만 담았다. 그러나, 그 위치에 상어가 있는지에 대한 정보가 있어야 상어가 겹쳤을 때 쫓겨나는 구현을 할 수 있으므로, 냄새 클래스에 상어 객체를 변수로 담았다.

- 상어

상어는 기본적으로 숫자와 방향, 상어마다 갖는 우선순위, 그리고 위치에 대한 정보를 담았다.

```Java
static class Shark{
		int num;
		int dir;
		int[][] sDir; // 상어 개인의 우선순위
	    int x;
	    int y;

		public Shark(int num, int dir) {
			this.num = num;
			this.dir = dir;
		}

		public Shark(int num, int x, int y) {
			this.num = num;
			this.sDir = new int[5][5];
			this.x = x;
			this.y = y;
		}

	}
```

- 냄새

정확히는 격자 한 칸에 대한 정보이지만 냄새가 있는 구간의 정보가 필요한 것이고, 상어가 존재하는 격자 칸에는 무조건 냄새가 있기 때문에 이름을 수정하지는 않았다.

냄새에는 위치와 남은 시간, 냄새의 주인을 의미하는 상어와 현 위치에 있는 상어 정보를 가지게끔 했다.

```java
static class Smell{
		Shark shark; // 현 위치에 있는 상어
		Shark smellOwner; // 냄새의 주인 상어
		int time;
		int x;
		int y;

		public Smell(Shark shark, Shark smellOwner, int time, int x, int y) {
			super();
			this.shark = shark;
			this.smellOwner = shark;
			this.time = time;
			this.x = x;
			this.y = y;
		}

```

### 정보를 담을 리스트

살아있는 상어와 쫓겨날 상어, 남아있는 냄새와 없어질 냄새를 담을 ArrayList를 만들어 두었다.

```Java
	static ArrayList<Shark> aliveShark = new ArrayList<>();
	static ArrayList<Shark> killList = new ArrayList<>();
	static ArrayList<Smell> aliveSmell = new ArrayList<>();
	static ArrayList<Smell> delSmell = new ArrayList<>();
```

또, 관리를 번호 별로 하기 위해서 1차원 상어 배열도 만들어 뒀다.

### 데이터 입력받기

격자를 map이라는 2차원 냄새 배열로 선언하고, 정보를 입력받았다.

입력받을 때, 상어가 존재하면 상어를 생성해 1차원 배열에 인덱스에 맞춰 넣어주고, 그 상어를 aliveShark에 넣어준다.

또, 해당 위치에서 상어가 냄새를 뿜기 때문에 바로 map에 냄새를 생성해서 추가해주고, 남아있는 냄새 aliveSmell에 넣어줬다. (냄새가 살아있다는 표현이 맞는건가?)

상어의 방향과 우선순위 데이터는 1차원 배열의 상어 인덱스를 가지고 넣어줬다.

### 구현

진행은 1000초 동안 반복되며, 상어가 한마리가 남으면 종료된다. (aliveShark의 크기가 1이면 break)

각 초마다 반복되는 과정은

1. DecreasedSmellTime() : 남아있는 냄새의 시간을 1초씩 줄인다.
2. move() : 상어들이 이동한다.
3. deleteSmell() : 냄새가 k만큼 시간이 지난 것들을 없앤다.
4. smell() : 이동한 곳에서 상어들이 냄새를 뿌린다.
5. kill() : 겹쳐진 상어들을 죽인다.

위 5가지 과정의 반복이므로 각각 함수를 구현했다.
1,4,5 의 함수는 간단하게 구현했지만, 2번과 4번은 생각해야할 부분이 좀 있었다.

move() 같은 경우는 상어별 우선순위를 고려해서 이동을 해야하고, 이동할만한 위치가 없으면 자기가 원래 있던 곳으로 이동하게끔 해주면서 방향도 바꿔줘야 한다.

smell()에서는 같은 위치에 여러 상어가 냄새를 뿌릴 때 여러 마리의 상어가 냄새를 뿌리면 번호가 낮은 상어의 냄새만 남기고 나머지는 상어들은 제거리스트에 넣어줘야한다.

## 정답

![](https://images.velog.io/images/mulgyeol/post/f8bf9eb0-c33c-4c4b-a1eb-3ab209a6b934/image.png)

## 아쉬운 점

굳이 제거리스트를 사용하지 않고도 바로 제거할 수 있듯이, 더 효율적으로 구현을 할 수 있는데 생각하기 쉽게 하는 것을 우선적으로 하지 않았나 싶다.

## 코드

```Java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.ArrayList;
import java.util.StringTokenizer;

public class acmicpc_19237_AdultShark {

	static class Shark{
		int num;
		int dir;
		int[][] sDir; // 상어 개인의 우선순위
	    int x;
	    int y;

		public Shark(int num, int dir) {
			this.num = num;
			this.dir = dir;
		}

		public Shark(int num, int x, int y) {
			this.num = num;
			this.sDir = new int[5][5];
			this.x = x;
			this.y = y;
		}

	}

	static class Smell{
		Shark shark;
		Shark smellOwner;
		int time;
		int x;
		int y;

		public Smell(Shark shark, Shark smellOwner, int time, int x, int y) {
			super();
			this.shark = shark;
			this.smellOwner = shark;
			this.time = time;
			this.x = x;
			this.y = y;
		}


	}

	static int N,M,k;
	static Smell[][] map;
	static Shark[] sharks;
	static int[] dx = {0,-1,1,0,0};
	static int[] dy = {0,0,0,-1,1};
	static ArrayList<Shark> aliveShark = new ArrayList<>();
	static ArrayList<Shark> killList = new ArrayList<>();
	static ArrayList<Smell> aliveSmell = new ArrayList<>();
	static ArrayList<Smell> delSmell = new ArrayList<>();

	public static void main(String[] args) throws IOException {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		StringTokenizer st = new StringTokenizer(br.readLine(), " ");

		int answer = -1;
		N = Integer.parseInt(st.nextToken()); // map의 크기
		M = Integer.parseInt(st.nextToken()); // 상어의 갯수
		k = Integer.parseInt(st.nextToken()); // 냄새가 사라지는데 걸리는 시간

		map = new Smell[N][N];
		sharks = new Shark[M+1];


		//map 데이터, 살아있는 상어 관리할 ArrayList, 상어 바로 접근할 배열 데이터 입력
		for(int i=0; i<N; i++) {
			st = new StringTokenizer(br.readLine(), " ");
			for(int j=0; j<N; j++) {
				int num = Integer.parseInt(st.nextToken());
				if(num == 0) {
					map[i][j] = null;
				}else {
					sharks[num] = new Shark(num,i,j);
					aliveShark.add(sharks[num]);
					map[i][j] = new Smell(sharks[num], sharks[num], k, i, j);
					aliveSmell.add(map[i][j]);
				}
			}
		}

		// 각 상어의 방향 입력
		st = new StringTokenizer(br.readLine(), " ");
		for(int i=1; i<=M; i++) {
			sharks[i].dir = Integer.parseInt(st.nextToken());
		}

		// 각 상어의 방향 우선순위 데이터 입력
		for(int i=1; i<=M; i++) {
			for(int j=1; j<=4; j++) {
				st = new StringTokenizer(br.readLine(), " ");
				for(int k=1; k<=4; k++) {
					sharks[i].sDir[j][k] = Integer.parseInt(st.nextToken());
				}

			}
		}

		for(int t=1; t<=1000; t++) {
			// 냄새 1초씩 까기
			DecreasedSmellTime();
			// 이동한다.
			move();
			// 냄새가 k만큼의 시간이 지난 것들은 없앤다.
			deleteSmell();
			// 냄새뿌린다.
			smell();
			// 죽일 상어들 죽인다.
			kill();

			if(aliveShark.size() == 1) {
				answer = t;
				break;
			}

		}

		System.out.println(answer);
	}

	private static void deleteSmell() {
		for(Smell smell : delSmell) {
			map[smell.x][smell.y] = null;
			aliveSmell.remove(smell);
		}

		delSmell.clear();
	}

	private static void DecreasedSmellTime() {
		for(Smell smell : aliveSmell) {
			smell.time--;
			if(smell.time == 0) {
				delSmell.add(smell);
			}
		}
	}

	private static void kill() {
		for(Shark shark : killList) {
			aliveShark.remove(shark);
		}
		killList.clear();
	}

	private static void smell() {
		for(Shark shark : aliveShark) {
			int x = shark.x;
			int y = shark.y;
			if(map[x][y] == null) {
				map[x][y] = new Smell(shark, shark, k, x, y);
				aliveSmell.add(map[x][y]);
			}else {
				if(map[x][y].shark != null) { //상어가 겹치는 경우
					if(map[x][y].shark.num >= shark.num){
						killList.add(map[x][y].shark);
						map[x][y].shark = shark;
						map[x][y].smellOwner = shark;
						map[x][y].time = k;
					}else {
						killList.add(shark);
					}
				}else { //자기가 있던 방향으로 온 경우
					map[x][y].shark = shark;
					//map[x][y].smellOwner = shark; // 필요없는데 확실히 하기 위해
					map[x][y].time = k;
				}
			}
		}
	}

	private static void move() {
		for(Shark shark : aliveShark) {
			boolean flag = false;
			boolean findMySmell = false;
			int x = shark.x;
			int y = shark.y;
			int dir = shark.dir;
			int backX = -1;
			int backY = -1;
			int backDir = -1;

			for(int i=1; i<=4; i++) {
				int nx = x + dx[shark.sDir[dir][i]];
				int ny = y + dy[shark.sDir[dir][i]];

				//범위 체크
				if(nx >=0 && nx <N && ny >=0 && ny <N) {
					if(map[nx][ny] == null) { // 우선순위부터 차례로 비어있을 경우.
						shark.dir = shark.sDir[dir][i];
						shark.x = nx;
						shark.y = ny;
						map[x][y].shark = null;
						flag = true; // 빈 곳에 잘 들어갔다.
						break;
					}else { //자기가 원래 있던곳의 위치와 그곳으로 갔을 때의 방향을 얻어 놓는다.
						if(map[nx][ny].smellOwner.equals(shark) && !findMySmell) {
							backX = nx;
							backY = ny;
							backDir = shark.sDir[dir][i];
							findMySmell = true;
						}
					}
				}
			}

			if(!flag) { //자기가 있던 방향으로 이동한다.
				shark.dir = backDir;
				shark.x = backX;
				shark.y = backY;
				map[x][y].shark = null;
			}
		}
	}

}
```
