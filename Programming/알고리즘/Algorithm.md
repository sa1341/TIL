# 완전탐색 알고리즘

# 카펫

![스크린샷 2019-10-24 오후 11 18 36](https://user-images.githubusercontent.com/22395934/67497018-4c896980-f6b8-11e9-869d-f05ddc292222.png)




해당 문제는 완전탐색 알고리즘의 문제로.. 사실 완전 탐색에 대해서 공부를 한적이 없어서 처음에 어떻게 접근할까 고민하다가 이틀정도 고민 끝에 해답을 찾을 수 있었습니다. 난이도는 그렇게 어려운 편이 아니라고 하지만.. 역시 수포자한테는 힘들었던 문제였습니다.... 풀이를 봐도 전혀 이해를 못하다가 결국 그림을 그리면서 패턴을 찾아서 풀었습니다.

위의 문제에서 접근법을 찾을 수 있는 힌트는 가로의 길이가 세로의 길이보다 크거나 같다는 전제 조건을 캐치하는것과 갈색 벽돌이 빨간색 벽돌을 둘러쌓야하는 것을 이해하였다면 접근방법을 이해하는데 시간이 단축 될 것 입니다.

먼저, 갈색 벽돌의 개수는 카펫의 위, 아래 가로 길이가 같고, 세로 길이 역시 좌, 우가 같기 때문에 (2 * y) + (2 * x) - 4라는 방정식으로 갈색 벽돌의 개수를 구할 수 있습니다. 여기서 - 4는 가로, 세로 길이가 아무리 변해도 카펫의 꼭지점의 개수는 4개로 변하지 않는 불변의 값을 가집니다. 

그리고 빨간색 벽돌의 세로 길이는 갈색 벽돌의 세로 길이보다 -2만큼 이고, 가로 길이 역시 갈색 벽돌 -2 정도이기 때문에 (y - 2) * (x - 2) 식을 도출할 수 가 있습니다.

갈색 벽돌과 빨간색 벽돌의 개수를 이용하여 카펫의 가로, 세로의 길이를 구할 수 있었습니다.

JAVA 코드로 다음과 같이 작성 할 수 있습니다.


```java
package jungja_study;

import java.util.Arrays;

public class Carpet {

    public int[] solution(int brown, int red){

        if(brown < 8 ||brown >5000 && red < 1 || red >2000000) {
            throw new IllegalStateException();
        }

        int[] answer = new int[2];
        // 빨간색 벽돌은 갈색벽돌에 둘러 쌓여 있기 때문에 세로의 길이는 무조건 3이상부터 시작한다.
        // 가로 길이도 마찬가지로 갈색 벽돌이 빨간색 벽돌을 둘러쌓기 위해서는 3이상이여 됩니다.
        A:for (int y = 3; y < 5000; y++) {
            for (int x = y; x < 5000; x++) {
                if(isSameAsBrown(y, x, brown)){
                    if(isSameAsRed(y, x, red)){
                        answer[0] = x;
                        answer[1] = y;

                        break A;
                    }
                }

            }
        }
        return answer;
    }

    // 세로의 길이 : y, 가로의 길이 : x, 갈색 벽돌 개수 : brown 을 argument로 받아서 가로 세로의 길이를 구하기 위한 로직
    public static boolean isSameAsBrown(int y, int x, int brown){

        //위 아래로 가로 길이는 같고, 좌 우 가로 길이는 같기 때문에 갈색 벽돌의 개수는 아래의 식으로 구할 수 있습니다.
        //가로 세로의 길이에 따라서 변하지 않는 점은 결국 꼭지점은 항상 4개이기 때문에 -4를 해주어야 합니다.
       if((((y * 2) + (x * 2)) - 4) == brown){

            return true;
        }

        return false;
    }

    // 마찬가지로 빨산색 벽돌의 수를 가지고 가로 세로의 길이를 구합니다.
    public static boolean isSameAsRed(int y, int x, int red){
        //가로 -2 * 세로 - 2를 하면 빨간색 벽돌의 넓이가 나옵니다.
        if(((y - 2) * (x - 2)) == red){
            return true;
        }
        return false;
    }

    public static void main(String[] args) {

        Carpet carpet = new Carpet();
        System.out.println(Arrays.toString(carpet.solution(10,2)));
        System.out.println(Arrays.toString(carpet.solution(8,1)));
        System.out.println(Arrays.toString(carpet.solution(24,24)));
    }
}

```

위의 소스코드에서 이중 포문에서 첫번째 포문을 



# Java Stack 

A string containing only parentheses is balanced if the following is true: 1. if it is an empty string 2. if A and B are correct, AB is correct, 3. if A is correct, (A) and {A} and [A] are also correct.

Examples of some correctly balanced strings are: `"{}()", "[{()}]", "({()})"` 

Examples of some unbalanced strings are: `"{}(", "({)}", "[[", "}{"` etc.

Given a string, determine if it is balanced or not.


## Input Format

There will be multiple lines in the input file, each having a single non-empty string. You should read input till end-of-file.
The part of the code that handles input operation is already provided in the editor.

## Output Format
For each case, print 'true' if the string is balanced, 'false' otherwise.

## Sample Input

![스크린샷 2019-11-21 오전 2 15 40](https://user-images.githubusercontent.com/22395934/69261430-e0d9e400-0c04-11ea-8386-20fc43762e9c.png)


문제는 열린 괄호와 닫힌 괄호의 수를 체크해야 하지만 괄호의 종류에 따라서 순서도 체크해야 하기 때문에 이 부분에서 고민을 하였습니다. 

예를들어서 `[{()}]` 이 경우에는 정상적인 균형잡힌 괄호들이지만 `([)]` 이 경우에는 균형잡힌 괄호라고 볼 수 없습니다. 따라서 아래코드에서 첫 부분에 닫힌 괄호가 들어오면 false를 리턴하고 반복문을 종료하도록 구현하였습니다.

```java
if (c == '{' || c == '(' || c == '[') {
    str.push(c);
}else{
    // 스택의 크기가 0이면서 닫힌 괄호일 경우에는 바로 false로 리턴합니다.
    if (str.size() < 1) {
        result = false;
        break;
    }
```

input 값에 문자들을 순회하면서 열린 괄호를 만날 경우에는 스택에 넣어주고 그 다음에 닫힌 괄호가 오면 스택에 맨 위에 있는 값을 꺼내어 그 괄호가 닫힌 괄호와 같은 종류인 열린 괄호라면 false 값을 넣지 않고 그 다음 값을 순회하거나 그 다음 문자가 없다면 처음에  
