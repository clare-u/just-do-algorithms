# 15684 - 사다리 조작

[문제 링크](https://www.acmicpc.net/problem/15684)

## 로직

FOR 반복문과 재귀함수를 사용한 DFS 형식으로 문제를 풀었다.

또한, 사용할 수 있는 사다리의 개수를 미리 지정하여 dfs 함수를 호출하도록 하였다.

이러한 목적지에 도달하기 위한 최소 횟수를 구하는 방식은 이전에 코딩테스트 문제에서 최소 점프 횟수를 구하는 것과 비슷하다.

### 사용할 수 있는 사다리의 개수를 이용하는 방식.

```java
// 0부터 3까지 허용하는 가로 막대의 갯수를 지정하고 dfs 함수를 호출한다.
		// 0을 포함하는 이유는, 이미 조건이 충족되어있는 경우가 존재하기 때문이다.
		for(int i = 0; i <= 3; i++) {
			ans = i;
			// 첫 번째 행부터 탐색하기 위해, dfs의 첫 번째 매개변수로 1을 넣었다.
			// 사용하는 가로 막대의 갯수를 저장하기 위해 두 번째 매개변수로 0을 넣었다.
			dfs(1, 0);
			
			// 만일 dfs를 시행한 이후, 조건이 충족되었다면,
			// flag 변수를 통해 반복문을 빠져나온다.
			if(flag) {
				break;
			}
		}
```

반복문을 통해, 사용할 수 있는 사다리의 갯수를 직접적으로 지정해주고, dfs 함수에 해당 사다리의 갯수를 매개변수로 사용하였다.

이러한 사용할 수 있는 사다리의 갯수를 적은 숫자부터 하나씩 할당하며 확인하는 방식은,

그렇지 않고 아무것도 놓지 않았을 때, 하나를 놓았을 때, 두 개를 놓았을 때, 세 개를 놓았을 때를 한 번에 확인하는 방식보다

'세 배' 정도 빠른 속도로 문제를 해결했다.

```
사다리의 갯수를 지정했을 때의 속도는 대략 360ms,
사다리의 갯수를 지정하지 않았을 때의 속도는 대략 1080ms이 나왔다.
```

### 가로 막대를 만났을 때 이동하기 위한 아이디어

사다리를 2차원 배열로 구현한다는 것이 잘 와닿지 않았기에, 세로 막대를 타고 내려가다가 가로 막대를 만난 경우, 해당 가로 막대를 타고 이동하기 위한 구현을 스스로 생각해내지 못했다.

해당 문제를 구현하기 위한 아이디어를 다른 곳에서 참조해본 결과,

가로막대의 왼쪽 부분에 해당하는 곳은 2차원 배열에서 1로 표시해주고, 가로막대의 오른쪽 부분에 해당하는 곳은 2차원 배열에서 2로 표시해주는 것이다.

이후, i번째 사다리에서 출발하여 i번째 사다리에서 끝나는지 확인할 때는 2차원 배열에서 1이라는 수치를 만났을 때, i + 1로 이동시키고,
1이 아닌 2라는 수치를 만났을 때는 i - 1로 이동시키게 되는 것이다.

```java
// 조건을 충족하고 있는지 확인하기 위한 check 메서드.
	public static boolean check() {
		// 모든 세로 막대에 대한 탐색을 하는 for문이다.
		for(int i = 1; i < length + 1; i++) {
			
			// 세로 막대의 번호를 column 변수에 할당하고,
			// 가로 막대를 놓을 수 있는 위치를 처음부터 탐색하기 위해,
			// row 변수에 1을 할당하였다.
			int row = 1;
			int column = i;
			
			// 가로 막대를 놓을 수 있는 위치들을 전부 탐색하는 for문이다.
			for(int j = 0; j < place; j++) {
				
				// 만약 탐색한 곳에 1이 있다면 오른쪽으로 가고,
				if(ladder[row][column] == 1) {
					column++;
				// 탐색한 곳에 2가 있다면 왼쪽으로 간다.
				}else if(ladder[row][column] == 2) {
					column--;
				}
				
				// 갔다가 돌아오는 것을 방지하고 또한,
				// 다음 가로 막대가 가능한 곳을 탐지하기 위해 row 변수에 1을 더해준다.
				row++;
			}
			
			// 만약 출발지점을 나타내는 i와 도착지점을 나타내는 column이 같지 않다면,
			// check 함수를 끝내고 false를 반환한다.
			if(i != column) {
				return false;
			}
		}
		// 모든 탐색을 성공적으로 마치면 true를 반환한다.
		return true;
	}
```

### for 반복문과 재귀함수를 이용한 dfs

사다리에서 가로 막대 부분을 1과 2로 구현한다는 아이디어를 알게 되었으니, 이를 적용하여 일반적인 형태의 dfs를 구현하면 끝이다.

가로 우선 탐색으로 사다리를 탐색하도록 하는 2중 for문 안에서, 만약 사다리가 없다면, 해당 위치에 사다리를 놓고 재귀함수를 호출한다.

재귀함수를 타고 들어갔을 때, 지정한 사다리의 갯수만큼 사용했다면 바로 i번 사다리에서 출발하여 i번 사다리로 끝나는 지를 체크하게 된다.

이 때, 주의해야할 점은, 가로 막대를 둘 때, 마지막 세로 막대에서는 오른쪽으로 이어서 건너갈 다른 세로 막대가 존재하지 않기 때문에,

for문을 구성할 때, 열 탐색 범위를 최대 길이보다 하나 적은 범위로 만들어주어야 한다.


<br>