---
title: '[Programmers] 2021 KAKAO BLIND RECRUITMENT - 신규 아이디 추천'
date: 2021-05-01 14:04:57
category: Problem Solving
thumbnail: { thumbnailSrc }
draft: false
---

![img](./images/programmers.png)

# [Programmers] 2021 KAKAO BLIND RECRUITMENT - 신규 아이디 추천

## 들어가기 전에

[2021 KAKAO BLIND RECRUITMENT 신규 아이디 추천](https://programmers.co.kr/learn/courses/30/lessons/72410)

알고리즘 스터디에서 선정한 위 문제를 풀어보고,
Java로 문자열을 다루는 방법에 대해 정리한다.

> - 1단계 new_id의 모든 대문자를 대응되는 소문자로 치환합니다.
> - 2단계 new*id에서 알파벳 소문자, 숫자, 빼기(-), 밑줄(*), 마침표(.)를 제외한 모든 문자를 제거합니다.
> - 3단계 new_id에서 마침표(.)가 2번 이상 연속된 부분을 하나의 마침표(.)로 치환합니다.
> - 4단계 new_id에서 마침표(.)가 처음이나 끝에 위치한다면 제거합니다.
> - 5단계 new_id가 빈 문자열이라면, new_id에 "a"를 대입합니다.
> - 6단계 new_id의 길이가 16자 이상이면, new_id의 첫 15개의 문자를 제외한 나머지 문자들을 모두 제거합니다. - 만약 제거 후 마침표(.)가 new_id의 끝에 위치한다면 끝에 위치한 마침표(.) 문자를 제거합니다.
> - 7단계 new_id의 길이가 2자 이하라면, new_id의 마지막 문자를 new_id의 길이가 3이 될 때까지 반복해서 끝에 붙입니다.

파이썬으로 문자열 처리하는 과정은 인덱싱 슬라이싱을 이용해서 꽤 쉬웠는데, Java로 풀어보려니 꽤 귀찮았다. StringBuilder를 이용하면 조금 더 쉽겠다 싶어서 풀어봤는데 처음 푼 코드는 다음과 같다.

```Java
public class Kakao_2021_신규아이디추천 {

	public String solution(String new_id) {
		String answer = "";
		StringBuilder sb = new StringBuilder();
		int len = new_id.length();

		//1단계
		new_id = new_id.toLowerCase();

		//2단계
		for(int i=0; i<len; i++) {
			char c = new_id.charAt(i);
			if(c >= 'a' && c <= 'z') {
				sb.append(c);
			}else if(Character.isDigit(c)) {
				sb.append(c);
			}else if(c == '-' || c=='_' || c =='.') {
				sb.append(c);
			}else {
				continue;
			}
		}

 		//3단계
		new_id = sb.toString();
		len = new_id.length();
		sb.setLength(0);
		sb.append(new_id.charAt(0));
		int back = 0, front = 1;
		while(front < len) {
			if(new_id.charAt(back)=='.' && new_id.charAt(front)=='.') {
				front++;
			}else{
				sb.append(new_id.charAt(front));
				back = front;
				front++;
			}
		}

		//4단계
		if(sb.length() >= 1 && sb.charAt(0) == '.') {
			sb = new StringBuilder(sb.substring(1, sb.length()));
		}

		if(sb.length() >= 1 && sb.charAt(sb.length()-1) == '.') {
			sb = new StringBuilder(sb.substring(0, sb.length()-1));
		}


		//5단계
		if(sb.length() == 0) {
			sb.append('a');
		}

		//6단계
		if(sb.length() >= 16) {
			sb = new StringBuilder(sb.substring(0, 15));
		}

		while(sb.length() != 0 && sb.charAt(sb.length()-1) == '.'){
    		sb = new StringBuilder(sb.substring(0, sb.length()-1));
		}

		//7단계
		while(sb.length() <= 2) {
			sb.append(sb.charAt(sb.length()-1));
		}

		answer = sb.toString();

		return answer;
    }
}

```

## 1단계 - 소문자화, 대문자화

> new_id의 모든 대문자를 대응되는 소문자로 치환합니다.

![](https://images.velog.io/images/mulgyeol/post/e4a78d8d-e8e3-4a89-989e-6ccf21777f1f/image.png)

![](https://images.velog.io/images/mulgyeol/post/903ee761-2da6-4bc9-b9ed-a78732395780/image.png)

[https://docs.oracle.com/javase/8/docs/api/](https://docs.oracle.com/javase/8/docs/api/)

`toLowerCase()` 함수를 이용해 소문자화 시켰다.
문자열이 알파벳으로만 이루어져 있지 않아도, 대문자에 해당되는 글자만 소문자로 바꿔준다.

다른 사람들의 풀이를 보니 대부분 이렇게 처리했다. 제일 간편한 방법인 듯 하다.

## 2단계 - 필터링

> new*id에서 알파벳 소문자, 숫자, 빼기(-), 밑줄(*), 마침표(.)를 제외한 모든 문자를 제거합니다.

문자열을 처음부터 탐색하면서 포함된 문자만 StringBuilder에 넣어줬다.

다풀고나서 꽤 충격적인 풀이들을 봤는데,

```Java
new_id = new_id.replaceAll("[^-_.a-z0-9]","");
```

일단 replaceAll 함수에 대해 알게 됐다.
![](https://images.velog.io/images/mulgyeol/post/3936b6c7-c7dd-4122-b132-b8881bb9518b/image.png)

지정된 `정규식`과 일치하는 이 문자열의 각 부분 문자열을 지정된 대체 문자열로 바꾸는 함수이다.

그리고 뒤의 `[^-_.a-z0-9]`이 정규식(또는 정규표현식)이라고 한다.
이게 뭔지 몰라서 찾아봤는데,

`정규표현식(Regular Expression)`이란 컴퓨터 과학의 정규언어로부터 유래한 것으로 특정한 규칙을 가진 문자열의 집합을 표현하기 위해 쓰이는 형식언어라고 한다.

아래 출처에서 정규표현식에 대해 자세히 다루어져있다. 유용하게 잘 활용하면 좋을 것 같으나, 이번 포스팅은 문자열 처리방법에 초점을 둔 글이므로 정규표현식에 대해서는 다음에 더 알아보고 정리해야겠다.

아무튼,
정리하자면 해당 문자열에서 `-`,`_`,`.`,`a-z`,`0-9`가 아닌 문자들은 모두 `""`로 바꿔주는 코드이다.

![](https://images.velog.io/images/mulgyeol/post/45586527-338e-4c15-889b-0ea972332111/image.png)
출처 : [https://www.nextree.co.kr/p4327/](https://www.nextree.co.kr/p4327/)

## 3단계

> new_id에서 마침표(.)가 2번 이상 연속된 부분을 하나의 마침표(.)로 치환합니다.
