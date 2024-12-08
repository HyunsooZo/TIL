---
description: >-
  개인프로젝트에 사용할 라이브러리도 만들겸 하여 레디스로 연관검색어 기능을 구현해보았다. 비교를 위해 Trie 구조를 사용한 인메모리
  연관검색어 기능도 함께 구현했다.
---

# 🐎 Redis : Practice & Comparison

## Comparison 1 : Trie + InMemory

Trie는 문자열 검색에 특화된 자료구조로, 빠른 검색 성능을 제공한다. \
특히 자동완성 기능 구현에 자주 사용된다고 알고있다. \
Trie는 노드에 문자를 저장하고, 단어의 끝을 표시하여 검색과 삽입 작업을 효율적으로 수행한다.\
이 방식은 내가 직접 쓸 예정이 아니라 레디스 형식과 비교만 할 것이므로 한글을 제외한 영문만 동작하도록 작성했다.

```java
import java.util.ArrayList;
import java.util.List;

// Trie 노드 클래스
class TrieNode {
    TrieNode[] children = new TrieNode[26]; // 알파벳 크기
    boolean isEndOfWord; // 단어 끝 표시

    TrieNode() {
        this.isEndOfWord = false;
    }
}

// Trie 클래스
public class Trie {
    private final TrieNode root = new TrieNode();

    // 단어 삽입
    public void insert(String word) {
        TrieNode node = root;
        for (char c : word.toCharArray()) {
            int index = c - 'a';
            if (node.children[index] == null) {
                node.children[index] = new TrieNode();
            }
            node = node.children[index];
        }
        node.isEndOfWord = true;
    }

    // 접두사 검색
    public List<String> searchWordsWithPrefix(String prefix) {
        List<String> result = new ArrayList<>();
        TrieNode node = root;

        // 접두사 노드 탐색
        for (char c : prefix.toCharArray()) {
            int index = c - 'a';
            if (node.children[index] == null) {
                return result;
            }
            node = node.children[index];
        }

        // 접두사 아래 모든 단어 찾기
        findAllWords(node, prefix, result);
        return result;
    }

    // 재귀적으로 단어 탐색
    private void findAllWords(TrieNode node, String current, List<String> result) {
        if (node.isEndOfWord) {
            result.add(current);
        }
        for (char c = 'a'; c <= 'z'; c++) {
            int index = c - 'a';
            if (node.children[index] != null) {
                findAllWords(node.children[index], current + c, result);
            }
        }
    }
}
```

***

## Comparison 2 : Redis

이 코드는 실제 토이프로젝트에서 사용할 목적으로 자동완성을 구현 해보았다.\
ZSet자료구조를 사용해 한글과 영문, 초성을 커버하며 게시글 또는 검색대상이 insert될 때 \
등록 후 일정시간마다 자동완성을 호출하여 결과를 반환하는 형태를 예상하며 구현해봤다.

```java
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.core.ZSetOperations;
import java.util.*;
import java.util.stream.Collectors;

@Service
@RequiresArgsConstructor
public class AutoCompleteService {

    private final RedisTemplate<String, String> redisTemplate;
    private static final String AUTOCOMPLETE_KEY = "autocomplete";
    private static final String CONSONANT_KEY = "autocomplete:consonant";

    private final String[] chs = {
            "ㄱ", "ㄲ", "ㄴ", "ㄷ", "ㄸ", "ㄹ", "ㅁ", "ㅂ", "ㅃ",
            "ㅅ", "ㅆ", "ㅇ", "ㅈ", "ㅉ", "ㅊ", "ㅋ", "ㅌ", "ㅍ", "ㅎ"
    };

    public AutoCompleteService(RedisTemplate<String, String> redisTemplate) {
        this.redisTemplate = redisTemplate;
    }

    // 검색어 등록
    public void addAutoCompleteWord(String keyword) {
        redisTemplate.opsForZSet().add(AUTOCOMPLETE_KEY, keyword, 0);
        String consonant = extractConsonant(keyword);
        redisTemplate.opsForZSet().add(CONSONANT_KEY, consonant + ":" + keyword, 0);
    }

    // 검색어 삭제
    public void deleteAutoCompleteWord(String keyword) {
        redisTemplate.opsForZSet().remove(AUTOCOMPLETE_KEY, keyword);
        String consonant = extractConsonant(keyword);
        redisTemplate.opsForZSet().remove(CONSONANT_KEY, consonant + ":" + keyword);
    }

    // 자동완성 검색
    public List<String> autoComplete(String input) {
        if (isKoreanConsonant(input.charAt(0))) {
            return autoCompleteByConsonant(input);
        } else {
            return autoCompleteByGeneral(input);
        }
    }

    // 한글 초성 기반 검색
    private List<String> autoCompleteByConsonant(String input) {
        Set<String> results = redisTemplate.opsForZSet().rangeByScore(CONSONANT_KEY, 0, 0);
        if (results == null) return Collections.emptyList();

        return results.stream()
                .filter(item -> item.startsWith(input))
                .map(item -> item.split(":", 2)[1]) // 초성 제거 후 실제 단어 반환
                .toList();
    }

    // 일반 검색
    private List<String> autoCompleteByGeneral(String input) {
        Set<ZSetOperations.TypedTuple<String>> rankedKeywords = redisTemplate.opsForZSet().rangeWithScores(AUTOCOMPLETE_KEY, 0, -1);
        if (rankedKeywords == null || rankedKeywords.isEmpty()) return Collections.emptyList();

        return rankedKeywords.stream()
                .sorted((a, b) -> Double.compare(b.getScore(), a.getScore())) // 내림차순 정렬
                .map(ZSetOperations.TypedTuple::getValue)
                .filter(keyword -> keyword.contains(removeSpecialCharacters(input)))
                .toList();
    }

    // 문자에서 초성을 추출
    private String extractConsonant(String text) {
        StringBuilder chosung = new StringBuilder();
        text.chars().forEach(unicode -> {
            if (isKoreanCharacter((char) unicode)) {
                int chosungIndex = (unicode - '가') / (28 * 21);
                chosung.append(chs[chosungIndex]);
            }
        });
        return chosung.toString();
    }

    // 한글 문자인지 확인
    private boolean isKoreanCharacter(char ch) {
        return ch >= 0xAC00 && ch <= 0xD7A3;
    }

    // 한글 초성인지 확인
    private boolean isKoreanConsonant(char ch) {
        return ch >= 'ㄱ' && ch <= 'ㅎ';
    }

    // 입력 문자열에서 특수문자 제거
    private String removeSpecialCharacters(String text) {
        return text.replaceAll("[^가-힣a-zA-Z0-9]+", "").toLowerCase();
    }
}
```

***

## Conclusion

사실 인메모리가 더 빠를것이라 예상했지만, 예상외로 Redis를 사용한 방식이 더빨랐다. \
메모리 유실 가능성 때문에 어쨌든 Redis를 써야 했지만 속도까지 잘 나오니 매우 만족스러웠다.

하지만! 캐싱이 아닌 예제와 같이 자동완성기능이 목적이고, 돈과 서버가 넉넉하다면 \
역시 그 유명한 엘라스틱 서치를 사용하는게 여러모로 효율적인 방법일것 같다.

### Speed Comparison

<table><thead><tr><th width="226">Implementation</th><th>Search Results</th><th>Time (seconds)</th></tr></thead><tbody><tr><td>Trie</td><td>apple, application</td><td>0.05657315254211426</td></tr><tr><td>Redis-like</td><td>apple, application</td><td>0.05295205116271973</td></tr></tbody></table>

### Detailed Comparison

<table><thead><tr><th width="226"></th><th>Trie</th><th>Redis</th></tr></thead><tbody><tr><td><strong>메모리 사용</strong></td><td>데이터 전부 메모리에 저장</td><td>메모리와 디스크 조합 가능</td></tr><tr><td><strong>검색 성능</strong></td><td>O(n)</td><td>O(1) ~ O(log(n))</td></tr><tr><td><strong>확장성</strong></td><td>분산 처리 어려움</td><td>분산 클러스터 구성 가능</td></tr><tr><td><strong>구현 난이도</strong></td><td>자료구조 직접 구현 필요</td><td>Redis 설정으로 간단히 시작 가능</td></tr></tbody></table>
