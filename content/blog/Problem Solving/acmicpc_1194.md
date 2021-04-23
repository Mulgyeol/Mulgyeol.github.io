---
title: '[백준 1194] 달이 차오른다, 가자. - Java'
date: 2021-04-23 14:04:57
category: Problem Solving
thumbnail: { thumbnailSrc }
draft: false
---

![img](./images/BAEKJOON.png)

# [백준 1194] 달이 차오른다, 가자 - Java

[문제 바로가기](https://www.acmicpc.net/problem/1194)

## 아이디어

### 이동

현재 위치에서 탈출구까지 이동해야하므로 **BFS**로 구현합니다.

### 열쇠와 문

열쇠를 가지지 않은 상태에서는 문으로 이동할 수 없습니다.
민식이가 가지고 있는 열쇠에 따라서 이동 경로가 달라지고,
어떤 열쇠를 가졌을 때와, 가지지 않았을 때의 방문체크 또한 달라져야합니다.

따라서 민식이가 가지고 있는 열쇠 상태도 계속해서 담고,
미로의 칸마다 민식이가 어떤 상태로 방문한 적이 있는지 저장해둡니다.

구체적으로
미로 배열에는 각 위치에 있는 문자가 들어가고,
BFS 큐에 들어갈 데이터에는 행, 열, 현재 민식이가 가지고 있는 열쇠정보가 들어갑니다.
방문처리를 해 줄 배열은 3차원으로 구성합니다. 각 행과 열마다 가질 수 있는 64개의 열쇠 정보 중, 어떤 상태로 민식이가 방문했었는지를 체크해 줍니다.

민식이가 문을 방문했을 때, 민식이의 열쇠 정보에서 해당 열쇠를 가지고 있는지는 비트마스킹을 통해서 확인할 수 있습니다.

## 정답

![](https://images.velog.io/images/mulgyeol/post/fcb151d4-4ef5-4834-8619-a88c42165014/image.png)

## Java Code

```Java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.LinkedList;
import java.util.Queue;
import java.util.StringTokenizer;

public class acmicpc_1194_MoonFullGo {

	static int N, M;
	static char[][] map;
	static int[] dx = {-1,1,0,0};
	static int[] dy = {0,0,-1,1};
	static int answer = -1;
	public static void main(String[] args) throws IOException {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		StringTokenizer st = new StringTokenizer(br.readLine(), " ");

		N = Integer.parseInt(st.nextToken());
		M = Integer.parseInt(st.nextToken());
		map = new  char[N][M];
		int x=0, y=0;

		for(int i=0; i<N; i++) {
			String line = br.readLine();
			for(int j=0; j<M; j++) {
				map[i][j] = line.charAt(j);
				if(map[i][j] == '0') {
					x = i;
					y = j;
					map[i][j] = '.';
				}
			}
		}

		boolean[][][] visited = new boolean[N][M][64];
		bfs(x,y,visited);

		System.out.print(answer);

	}
	private static void bfs(int x, int y, boolean[][][] visited) {
		Queue<int[]> queue = new LinkedList<>();
		int cnt = 0;
		queue.offer(new int[] {x,y,0});
		visited[x][y][0] = true;

		while(!queue.isEmpty()) {
			int size = queue.size();
			cnt ++;
			for(int s =0; s<size; s++) {
				int[] current = queue.poll();
				int curX = current[0];
				int curY = current[1];
				int curK = current[2];

				for(int i=0; i<4; i++) {
					int nx = curX+dx[i];
					int ny = curY+dy[i];

					if(nx >=0 && nx <N && ny >= 0 && ny <M && map[nx][ny] != '#' && !visited[nx][ny][curK]) {
						if(map[nx][ny] >= 'A' && map[nx][ny] <= 'F') {
							//A=0, B=1, C=2 ... F=5;
							int doorInfo = map[nx][ny] - 'A';
							if ((curK & 1<<doorInfo) != 0) {
								queue.offer(new int[] {nx,ny,curK});
								visited[nx][ny][curK] = true;
							}
						}else if(map[nx][ny] >= 'a' && map[nx][ny] <= 'f'){
							//a=0, b=1, c=3 ... f=5;
							int keyInfo = map[nx][ny] - 'a';
							int newCurK = curK | 1<<keyInfo;
							queue.offer(new int[] {nx,ny,newCurK});
							visited[nx][ny][newCurK] = true;
						}else if(map[nx][ny] == '.') {
							queue.offer(new int[] {nx,ny,curK});
							visited[nx][ny][curK] = true;
						}else if(map[nx][ny] == '.') {
							queue.offer(new int[] {nx,ny,curK});
							visited[nx][ny][curK] = true;
						}else if(map[nx][ny] == '1') {
							answer = cnt;
							return;
						}
					}// 범위 제한 설정

				}// 4방 탐색
			}//size

		}//while
	}

}


```
