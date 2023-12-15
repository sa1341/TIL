# 문제 설명
매운 것을 좋아하는 Leo는 모든 음식의 스코빌 지수를 K 이상으로 만들고 싶습니다. 모든 음식의 스코빌 지수를 K 이상으로 만들기 위해 Leo는 스코빌 지수가 가장 낮은 두 개의 음식을 아래와 같이 특별한 방법으로 섞어 새로운 음식을 만듭니다.

> 섞은 음식의 스코빌 지수 = 가장 맵지 않은 음식의 스코빌 지수 + (두 번째로 맵지 않은 음식의 스코빌 지수 * 2)


Leo는 모든 음식의 스코빌 지수가 K 이상이 될 때까지 반복하여 섞습니다.
Leo가 가진 음식의 스코빌 지수를 담은 배열 scoville과 원하는 스코빌 지수 K가 주어질 때, 모든 음식의 스코빌 지수를 K 이상으로 만들기 위해 섞어야 하는 최소 횟수를 return 하도록 solution 함수를 작성해주세요.

## 제한 사항
- scoville의 길이는 1 이상 1,000,000 이하입니다.
- K는 0 이상 1,000,000,000 이하입니다.
- scoville의 원소는 각각 0 이상 1,000,000 이하입니다.
- 모든 음식의 스코빌 지수를 K 이상으로 만들 수 없는 경우에는 -1을 return 합니다.


## 입출력 예


![스크린샷 2020-01-04 오후 7 05 07](https://user-images.githubusercontent.com/22395934/71763911-250b1480-2f25-11ea-9e48-131f4fdb16d2.png)


## 입출력 예 설명
1. 스코빌 지수가 1인 음식과 2인 음식을 섞으면 음식의 스코빌 지수가 아래와 같이 됩니다.
새로운 음식의 스코빌 지수 = 1 + (2 * 2) = 5
가진 음식의 스코빌 지수 = [5, 3, 9, 10, 12]

2. 스코빌 지수가 3인 음식과 5인 음식을 섞으면 음식의 스코빌 지수가 아래와 같이 됩니다.
새로운 음식의 스코빌 지수 = 3 + (5 * 2) = 13
가진 음식의 스코빌 지수 = [13, 9, 10, 12]

모든 음식의 스코빌 지수가 7 이상이 되었고 이때 섞은 횟수는 2회입니다.

## 문제 풀이
```java
import java.util.PriorityQueue;

public class ScovillExample {
    public int solution(int[] scoville, int K) {

        int answer = 0;

        PriorityQueue<Integer> priorityQueue = new PriorityQueue<>();

        //우선순위 큐에 저장 시 오름차순으로 정렬됩니다.
        for (int i_scoville :scoville){
            priorityQueue.offer(i_scoville);
        }
        // peek() 메소드를 통해 순차적으로 scoville 값을 충족하는지 검증
        while(priorityQueue.peek() < K){
            if(priorityQueue.size() == 1){
                return -1;
            }
            // 첫 번째, 두 번째로 작은 값을 구하기
            int first = priorityQueue.poll();
            int second = priorityQueue.poll();

            int result = first + (second * 2);

            priorityQueue.offer(result);
            answer++;
        }

        return answer;
    }

    public static void main(String[] args) {

        ScovillExample scovillExample = new ScovillExample();

        int[] scoville = {1,2,3,9,10,12};
        int K = 7;

        System.out.println(scovillExample.solution(scoville, K));
    }
}
```

이 문제는 생각보다 효율성 테스트에서 게속 실패가 떠서 풀지 못했다가... 질문하기에서 우선순위(priorityQueue) 큐라는 자료 구조 중 하나인 클래스에 대해서 알게 되었고 이것을 적용하여 풀었습니다.

하지만 기존의 우리가 알고 있는 큐와는 조금 다릅니다. Queue라는 자료구조는 `선입선출(First-In, First-Out)`의 대기열 규칙을 가지고 있씁니다. 먼저 들어오는 데이터가 먼저 나가는 구조이지만... 자바에서 제공하는 우선순위 큐는 들어온 순서와 상관없이 그 우선순위가 높은 엘리먼트가 나가게 됩니다. 우선순위의 기본적으로 정수 값을 기준으로 하면 값이 작을 수록 우선순위가 높습니다. 쉽게 생각해서 여러개의 값들을 이 우선순위 큐에 저장 후 값을 차례대로 꺼내면 값이 낮을 수록 우선순위가 높기 때문에 가장 낮은 값부터 차례대로 출력해서 보여준다고 생각하면 이해하기 쉽습니다.


 
while(priorityQueue.peek() <= K) 문을 통해서 우선순위 큐에 저장되어 있는 이미 정렬되어 있는 값들을 순차적으로 검증하여 스코빌의 값의 이하의 숫자를 찾습니다.
peek() 메소드는 큐의 선두를 취득하지만, 삭제하지 않습니다. 반대로 poll() 메소드는 큐의 선두를 취득 후 삭제합니다. 따라서 우선순위 큐에서 가장 작은 값과 두번 째로 가장 작은 값을 찾기 위해서 poll() 메소드를 사용하면 스코빌에 충족되는 값을 구하면서 우선순위 큐에서 삭제도 한번에 가능하기 때문에 유용한 메소드입니다.




# 프린터
## 문제설명
일반적인 프린터는 인쇄 요청이 들어온 순서대로 인쇄합니다. 그렇기 때문에 중요한 문서가 나중에 인쇄될 수 있습니다. 이런 문제를 보완하기 위해 중요도가 높은 문서를 먼저 인쇄하는 프린터를 개발했습니다. 이 새롭게 개발한 프린터는 아래와 같은 방식으로 인쇄 작업을 수행합니다.


1. 인쇄 대기목록의 가장 앞에 있는 문서(J)를 대기목록에서 꺼냅니다.
2. 나머지 인쇄 대기목록에서 J보다 중요도가 높은 문서가 한 개라도      존재하면 J를 대기목록의 가장 마지막에 넣습니다.
3. 그렇지 않으면 J를 인쇄합니다.


예를 들어, 4개의 문서(A, B, C, D)가 순서대로 인쇄 대기목록에 있고 중요도가 2 1 3 2 라면 C D A B 순으로 인쇄하게 됩니다.

내가 인쇄를 요청한 문서가 몇 번째로 인쇄되는지 알고 싶습니다. 위의 예에서 C는 1번째로, A는 3번째로 인쇄됩니다.

현재 대기목록에 있는 문서의 중요도가 순서대로 담긴 배열 priorities와 내가 인쇄를 요청한 문서가 현재 대기목록의 어떤 위치에 있는지를 알려주는 location이 매개변수로 주어질 때, 내가 인쇄를 요청한 문서가 몇 번째로 인쇄되는지 return 하도록 solution 함수를 작성해주세요.

## 제한사항

- 현재 대기목록에는 1개 이상 100개 이하의 문서가 있습니다.
- 인쇄 작업의 중요도는 1~9로 표현하며 숫자가 클수록 중요하다는 뜻입니다.
- location은 0 이상 (현재 대기목록에 있는 작업 수 - 1) 이하의 값을 가지며 대기목록의 가장 앞에 있으면 0, 두 번째에 있으면 1로 표현합니다.

## 입출력 예
![스크린샷 2020-01-12 오후 7 21 06](https://user-images.githubusercontent.com/22395934/72217418-b5bea180-3570-11ea-9bb5-11b94c9b6069.png)


## 입출력 예 설명

예제 #1

문제에 나온 예와 같습니다.

예제 #2

6개의 문서(A, B, C, D, E, F)가 인쇄 대기목록에 있고 중요도가 1 1 9 1 1 1 이므로 C D E F A B 순으로 인쇄합니다.



## 문제 풀이
```java
class Document{

    public int index;
    public int priority;

    public Document(int index, int priority) {
        this.index = index;
        this.priority = priority;
    }
}


public class Printer {
    public int solution(int[] priorities, int location) {

        int answer = 0;
        LinkedList<Document> queue = new LinkedList<>();
        // 결과 값을 가지고 있는 Queue 타입의 객체 
        LinkedList<Document> result = new LinkedList<>();

        for (int i = 0; i < priorities.length; i++) {
            queue.add(new Document(i, priorities[i]));
        }

        while(!queue.isEmpty()) {
            Document firstDoc = queue.poll();
            if (isMaximum(firstDoc, queue)) {
                result.add(firstDoc);
            } else {
                queue.add(firstDoc);
            }
        }

        // 정렬된 우선순위 값을 기준으로
        int order = 0;

        for (Document tempDoc : result){
            int value = tempDoc.index;
            if(location == value){
                answer = order + 1;
            }
            order++;
        }
        return answer;
    }

    public boolean isMaximum(Document firstDoc, LinkedList<Document> queue){

        if(queue.size() == 0){
            return  true;
        }

        for (Document temp : queue){
            if(firstDoc.priority < temp.priority){
               return false;
            }
        }
        return true;
    }
}
```

처음 이 문제를 보았을때 생각보다 쉬운거 같았는데... 막상 코드를 짜보니 자꾸 테스트케이스 실패가 났다... 이 알고리즘은 큐나 스택을 써야될거 같다고 위에 대놓고 명시를 해서 큐를 썼고 그 중에서 가장 많이 쓰인다는 LinkedList 큐를 사용하여 풀기로 하였다. 풀이 과정에서 우선 순위 값이 높은 순서대로 결과 Queue에 넣을 수는 있었지만 location에 해당하는 값이 우선 순위 기준으로 몇번째 인덱스에 있는지 찾는데 상당히 애를 먹었다. 그러다가.. 현재 내가 푸는 언어가 자바라는 것을 뒤늦게 깨닫고... 객체를 이용해서 인덱스와 우선 순위 값을 같이 관리하도록 Document 객체를 큐로 관리하도록 코드를 작성하였다. 이렇게 구현하니 직관적이면서 인덱스를 관리하는게 확실히 편해졌습니다. 프로그래머스에서 빡고수분들이 작성한 것을 보고는.. 도저희 저렇게 코드를 작성할 자신이 없었기 때문에 오래 걸리고 지저분하더라도 전 이게 더 직관적이라고 생각합니다.