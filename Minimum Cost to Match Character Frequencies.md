# Minimum Cost to Match Character Frequencies

---

## 1. Problem Statement

Given two strings `s` and `t` of equal length, calculate the **minimum total cost** to make the character composition and frequency count of `s` equal to `t`.

### Conversion Rules:
1. **Rule 1 (Opposite Type Conversion):** Changing a vowel in `s` using a consonant from `t` (or vice-versa) costs **`1`**.
2. **Rule 2 (Same Type Conversion):** Changing a character to the same character type (vowel $\leftrightarrow$ vowel or consonant $\leftrightarrow$ consonant) costs **`2`**.
3. Identical characters present in both strings cancel each other out at **`0`** cost.

---

## 2. Mathematical Intuition & Greedy Strategy

1. **Cancellation:** 
   If both `s` and `t` share identical characters, they do not need any transformation. We can calculate net frequency differences using a single array of size 26:
   $$\text{freq}[c] = \text{count}_s(c) - \text{count}_t(c)$$

2. **Classification:**
   - If $\text{freq}[i] > 0$: these characters are surplus in `s`. Categorize them into unmatched vowels ($V_s$) or unmatched consonants ($C_s$).
   - If $\text{freq}[i] < 0$: these characters are needed in `t`. Categorize them into missing vowels ($V_t$) or missing consonants ($C_t$).

3. **Greedy Pairing:**
   - To minimize total cost, maximize the conversions that cost **1** by pairing opposite types:
     $$\text{cheapPairs} = \min(V_s, C_t) + \min(C_s, V_t)$$
   - The remaining unmatched characters cannot be paired across opposite types and must be converted to the same type at cost **2**:
     $$\text{expensivePairs} = (V_s + C_s) - \text{cheapPairs}$$
   - **Total Minimum Cost:**
     $$\text{Total Cost} = (\text{cheapPairs} \times 1) + (\text{expensivePairs} \times 2)$$

---

## 3. Java Implementation

```java
public class Solution {

    public static int minCostToMatch(String s, String t) {
        if (s == null || t == null || s.length() != t.length()) {
            return -1;
        }

        // Net character frequency: s increments, t decrements
        int[] freq = new int[26];
        int n = s.length();

        for (int i = 0; i < n; i++) {
            freq[s.charAt(i) - 'a']++;
            freq[t.charAt(i) - 'a']--;
        }

        int vS = 0, cS = 0; // Unmatched vowels & consonants in s
        int vT = 0, cT = 0; // Unmatched vowels & consonants in t

        for (int i = 0; i < 26; i++) {
            if (freq[i] == 0) continue;

            boolean isVowel = isVowel((char) ('a' + i));
            int count = Math.abs(freq[i]);

            if (freq[i] > 0) {
                if (isVowel) vS += count;
                else cS += count;
            } else {
                if (isVowel) vT += count;
                else cT += count;
            }
        }

        // 1. Pair opposite types first (Cost = 1)
        int cheapPairs = Math.min(vS, cT) + Math.min(cS, vT);

        // 2. Remaining characters must match same types (Cost = 2)
        int totalUnmatched = vS + cS;
        int expensivePairs = totalUnmatched - cheapPairs;

        return (cheapPairs * 1) + (expensivePairs * 2);
    }

    private static boolean isVowel(char ch) {
        return ch == 'a' || ch == 'e' || ch == 'i' || ch == 'o' || ch == 'u';
    }

    public static void main(String[] args) {
        // Sample Tests
        System.out.println(minCostToMatch("aba", "bab"));     // Output: 1
        System.out.println(minCostToMatch("aaa", "eee"));     // Output: 6
        System.out.println(minCostToMatch("hello", "world")); // Output: 5
    }
}

```



4. Step-by-Step Dry Run ExampleInput: s = "hello", t = "world"Step 1: Frequency Array UpdateIterate from i = 0 to 4:Index is[i]t[i]Operation on s[i]Operation on t[i]Updated freq State0'h''w'freq['h' - 'a']++freq['w' - 'a']--freq['h'] = +1, freq['w'] = -11'e''o'freq['e' - 'a']++freq['o' - 'a']--freq['e'] = +1, freq['o'] = -12'l''r'freq['l' - 'a']++freq['r' - 'a']--freq['l'] = +1, freq['r'] = -13'l''l'freq['l' - 'a']++freq['l' - 'a']--freq['l'] = +1 (net: $+2 - 1 = +1$)4'o''d'freq['o' - 'a']++freq['d' - 'a']--freq['o'] = 0, freq['d'] = -1Non-zero values in freq:freq['d'] = -1freq['e'] = +1freq['h'] = +1freq['l'] = +1freq['r'] = -1freq['w'] = -1Step 2: Categorization (26-letter scan)Charfreq[c]ConditionTypeVariable ModifiedFinal Counts'd'-1< 0ConsonantcT += 1cT = 1'e'+1> 0VowelvS += 1vS = 1'h'+1> 0ConsonantcS += 1cS = 1'l'+1> 0ConsonantcS += 1cS = 2'r'-1< 0ConsonantcT += 1cT = 2'w'-1< 0ConsonantcT += 1cT = 3Summary:In s: $V_s = 1$ ('e'), $C_s = 2$ ('h', 'l')In t: $V_t = 0$, $C_t = 3$ ('d', 'r', 'w')Step 3: Greedy Pairing & Cost ComputationOpposite Pairs (Cost 1):$$\text{cheapPairs} = \min(V_s, C_t) + \min(C_s, V_t) = \min(1, 3) + \min(2, 0) = 1 + 0 = 1$$(1 swap transforms vowel 'e' in $s$ using consonant 'd' in $t$)Total Unmatched Characters in $s$:$$\text{totalUnmatched} = V_s + C_s = 1 + 2 = 3$$Same Type Pairs (Cost 2):$$\text{expensivePairs} = \text{totalUnmatched} - \text{cheapPairs} = 3 - 1 = 2$$(2 swaps transform consonants 'h' and 'l' into consonants 'r' and 'w')Total Cost:$$\text{Total Cost} = (1 \times 1) + (2 \times 2) = 1 + 4 = \mathbf{5}$$5. Detailed Test CasesTest Case 1: s = "aba", t = "bab"Frequency Breakdown:Matching: one 'a' and one 'b' cancel out.Remaining in s: one 'a' $\implies V_s = 1, C_s = 0$Remaining in t: one 'b' $\implies V_t = 0, C_t = 1$Pairing:$\text{cheapPairs} = \min(1, 1) + \min(0, 0) = 1$$\text{expensivePairs} = 1 - 1 = 0$Total Cost: $(1 \times 1) + (0 \times 2) = \mathbf{1}$Test Case 2: s = "aaa", t = "eee"Frequency Breakdown:Matching: None.Remaining in s: three 'a' $\implies V_s = 3, C_s = 0$Remaining in t: three 'e' $\implies V_t = 3, C_t = 0$Pairing:$\text{cheapPairs} = \min(3, 0) + \min(0, 3) = 0$$\text{expensivePairs} = 3 - 0 = 3$ (All vowel $\to$ vowel transitions)Total Cost: $(0 \times 1) + (3 \times 2) = \mathbf{6}$Test Case 3: s = "aei", t = "bcd"Frequency Breakdown:Matching: None.Remaining in s: 'a', 'e', 'i' $\implies V_s = 3, C_s = 0$Remaining in t: 'b', 'c', 'd' $\implies V_t = 0, C_t = 3$Pairing:$\text{cheapPairs} = \min(3, 3) + \min(0, 0) = 3$ (All vowel $\leftrightarrow$ consonant transitions)$\text{expensivePairs} = 3 - 3 = 0$Total Cost: $(3 \times 1) + (0 \times 2) = \mathbf{3}$6. Test Cases Summary TableTest Casest(Vs​,Cs​)(Vt​,Ct​)Cheap Pairs (Cost 1)Expensive Pairs (Cost 2)OutputCase 1"aba""bab"$(1, 0)$$(0, 1)$101Case 2"aaa""eee"$(3, 0)$$(3, 0)$036Case 3"aei""bcd"$(3, 0)$$(0, 3)$3037. Complexity AnalysisTime Complexity: $\mathcal{O}(N)$ — A single linear scan through strings of length $N$, followed by a constant $\mathcal{O}(26)$ loop.Space Complexity: $\mathcal{O}(1)$ — Fixed-size frequency array of 26 integers.
