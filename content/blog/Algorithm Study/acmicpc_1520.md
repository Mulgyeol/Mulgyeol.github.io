---
title: '[백준 - 1520] 내리막길 - Java'
date: 2020-04-21 14:02:00
category: 'Algorithm Study'
draft: false
---

![img](./images/BAEKJOON.png)

# [백준 - 1520] 내리막길 - (Algorithm Study - 9주차)

[문제 바로가기](https://www.acmicpc.net/problem/1520)

## 아이디어

### 해결과정 - DFS (실패)

단순하게 그냥 내리막 구간으로 DFS를 하면 되는 것 아닌가 하고 빠르게 코드를 짜봤다.

```Java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class acmicpc_1520_DownhillPath {

	static int R, C, cnt;
	static int[] dx = {-1,1,0,0};
	static int[] dy = {0,0,-1,1};

	static int[][] map;
	public static void main(String[] args) throws IOException {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		StringTokenizer st = new StringTokenizer(br.readLine(), " ");
		R = Integer.parseInt(st.nextToken());
		C = Integer.parseInt(st.nextToken());

		map = new int[R][C];
		boolean[][] visited = new boolean[R][C];

		for(int i=0; i<R; i++) {
			st = new StringTokenizer(br.readLine(), " ");
			for(int j=0; j<C; j++) {
				map[i][j] = Integer.parseInt(st.nextToken());
			}
		}

		visited[0][0] = true;
		dfs(0,0,visited);

		System.out.println(cnt);


	}

	private static void dfs(int i, int j, boolean[][] visited) {

		if(i == R-1 && j == C-1) {
			cnt ++;
			return;
		}


		for(int k=0; k<4; k++) {
			int nx = i + dx[k];
			int ny = j + dy[k];

			if(nx >=0 && nx <R && ny >=0 && ny <C && !visited[nx][ny] && map[i][j] > map[nx][ny]) {
				visited[nx][ny] = true;
				dfs(nx,ny,visited);
				visited[nx][ny] = false;
			}
		}

	}

}


```

![](https://images.velog.io/images/mulgyeol/post/74a50aee-f821-476b-956f-cb3c433947ab/image.png)

당연하게 시간초과가 났고, 더 효율적으로 풀 수 있는 방법을 찾아야했다.

시간 초과는 DP로 풀면 해결이 가능할것 같아서 시도했다.

### 아이디어 2. DP + DFS

- Bottom-Up

처음 생각한 아이디어는 map과 같은 크기의 dp 2차원 배열을 만들어두고, dp배열의 각 값은 해당 위치까지 이동하는 방법의 가짓수를 저장하는 것이었다. 그리고 나서는 도착지점에 해당하는 dp배열에 저장된 값이 경로의 수가 될테니 정답으로 출력하면 될 것 같았다.

하지만, 이렇게 하면 갱신된 이동경로부터 도착경로까지의 갯수를 전부 갱신시켜줘야 한다는 단점이 있었고, 시간초과와 더불어 구현도 복잡하다는 판단이 섰다.

- Top-Down

최종적으로 짜낸 아이디어는, dfs를 이용한 TOP-DOWN방식이다. 도착 지점의 dp배열에 값을 1로 미리 준다.

그리고 시작점부터 dfs 재귀를 도는데, 다음 위치의 dp배열의 값이 0이 아니면 현재 dp배열의 값에 탐색한 위치의 dp값을 더해주고, 따로 이동하지 않는다.

만약 dp배열의 값이 0이면 바로 한 번 더 이동해서 탐색하는 과정을 반복한다.

dfs로 재귀를 돌다가, 도착지점을 만나면 이동루트부터 dp값이 갱신될 것이고, 최종적으로 출발점에는 모든 경로의 수가 저장된다.

### 디버깅

- 방문처리

처음에 Top-Down 방식으로 수정하면서, 따로 visited 배열을 만들지 않고 dp배열의 0값을 미방문으로 인식하고 구현했다.

그러나 0일 경우에, 연결이 도착점과 연결이 안된 것인지 탐색을 안한것인지 구별하지못해 탐색을 했음에도 도착점과 연결이 안된 경로를 재탐색하기 때문에 시간초과가 난다.

결론적으로 다시 visited 배열로 방문을 체크를 했다.

## 정답

![](https://images.velog.io/images/mulgyeol/post/49d43e0e-8f58-4f4d-943f-755fcf065cf5/image.png)

## 코드

```Java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.StringTokenizer;

public class acmicpc_1520_DownhillPath_DP {

	static int R, C, cnt;
	static int[] dx = {1,0,0,-1};
	static int[] dy = {0,1,-1,0};

	static int[][] map;
	static int[][] dp;
	static boolean[][] visited;


	public static void main(String[] args) throws IOException {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		StringTokenizer st = new StringTokenizer(br.readLine(), " ");
		R = Integer.parseInt(st.nextToken());
		C = Integer.parseInt(st.nextToken());

		map = new int[R][C];
		dp = new int[R][C];
		visited = new boolean[R][C];

		for(int i=0; i<R; i++) {
			st = new StringTokenizer(br.readLine(), " ");
			for(int j=0; j<C; j++) {
				map[i][j] = Integer.parseInt(st.nextToken());
			}
		}

		dp[R-1][C-1] = 1;
		dfs(0,0);

		System.out.println(dp[0][0]);
	}


	private static void dfs(int i, int j) {
		if(i==R-1 && j==C-1) {
			return;
		}

		if(visited[i][j]) {
			return;
		}

		visited[i][j] = true;

		for(int k=0; k<4; k++) {
			int nx = i + dx[k];
			int ny = j + dy[k];

			if(nx >=0 && nx <R && ny >=0 && ny <C && map[i][j] > map[nx][ny]) {
				if(dp[nx][ny] != 0) {
					dp[i][j] += dp[nx][ny];
					continue;
				}else {
					dfs(nx,ny);
					dp[i][j] += dp[nx][ny];
				}
			}
		}
	}
}


```
