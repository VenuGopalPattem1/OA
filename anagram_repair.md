# Anagram Repair

## Problem

You are given two strings `S` and `T` of equal length.

`T` is the target string, and you can modify characters in `S` to obtain an anagram of `T`.

Characters that occur the same number of times in both strings can be matched directly and require `0` replacements.

For the remaining unmatched characters:

- Vowel -> consonant or consonant -> vowel costs `1`
- Vowel -> different vowel costs `2`
- Consonant -> different consonant costs `2`

Find the minimum number of replacements required.

## Example 1

```text
S = "abc"
T = "def"

Output = 4
```

One optimal pairing is:

```text
a -> d = 1
b -> e = 1
c -> f = 2
```

Therefore:

```text
Answer = 4
```

## Example 2

```text
S = "abcde"
T = "aefgh"

Output = 5
```

`a` occurs in both strings, so it costs `0`.

Remaining characters:

```text
S = "bcd"
T = "efg"
```

Pair:

```text
b -> e = 1
c -> f = 2
d -> g = 2
```

Therefore:

```text
Answer = 5
```

## Simple Idea

First count the frequency of every character in both strings.

Remove characters that occur in both strings.

Then count the remaining vowels and consonants:

```text
sv = vowels remaining in S
sc = consonants remaining in S

tv = vowels remaining in T
tc = consonants remaining in T
```

A vowel and consonant can be paired with cost `1`.

So:

```text
vowel -> consonant = min(sv, tc)
consonant -> vowel = min(sc, tv)
```

All remaining characters cost `2`.

## Java Solution

```java
class Main {

    public static int fun(String s, String t) {

        int[] a = new int[26];
        int[] b = new int[26];

        for(char c : s.toCharArray())
            a[c - 'a']++;

        for(char c : t.toCharArray())
            b[c - 'a']++;

        int sv = 0, sc = 0;
        int tv = 0, tc = 0;

        for(int i = 0; i < 26; i++) {

            int common = Math.min(a[i], b[i]);

            a[i] -= common;
            b[i] -= common;

            char c = (char)('a' + i);

            if(a[i] > 0) {
                if(isVowel(c))
                    sv += a[i];
                else
                    sc += a[i];
            }

            if(b[i] > 0) {
                if(isVowel(c))
                    tv += b[i];
                else
                    tc += b[i];
            }
        }

        // Cost 1: vowel <-> consonant
        int one = Math.min(sv, tc)
                + Math.min(sc, tv);

        int total = sv + sc;

        // Remaining replacements cost 2
        return one + (total - one) * 2;
    }

    static boolean isVowel(char c) {
        return "aeiou".indexOf(c) != -1;
    }
}
```

## Complexity

```text
Time  : O(N + 26)
Space : O(26)
```

## Key Point

Because the result only needs to be an **anagram**, we are free to choose how the remaining characters are paired.

Therefore, use as many:

```text
vowel <-> consonant
```

pairs as possible because they cost only `1`.

All remaining pairs cost `2`.
