# KMP思想
对于暴力算法，如果出现不匹配字符，需要同时回退 文本串 和 模式串 的指针。KMP的主要思想是永不回退文本串的指针，而是利用前缀表将模式串移动到正确位置继续匹配。

# 什么是前缀表
前缀表（prefix table），KMP算法中也经常被写为next数组。

## 前缀表的作用
利用前缀表，可以得知当前位置匹配失败时，新的模式串指针应该回退到哪里。文本串指针不变，从新的模式串指针继续匹配。（此时新模式串指针前的所有字符与文本串指针前的字符是匹配的，无需回退文本串指针）

## 前缀表的计算
前缀表要求的是最长相同前后缀，前缀是指以第一个字符开头的连续子串（不包含最后一个字符），后缀是指以最后一个字符结尾的连续子串（不包含第一个字符）。

# 代码实现

```c++
#include<iostream>
#include<string>
#include<algorithm>
using namespace std;

// 创建前缀表
inline void prefix_table(string pattern, int *prefix, int n) {
	prefix[0] = 0;
	int len = 0;
	int i = 1;
	while (i < n) {
        // 如abcabc, i=5时，此时len = 2，'c'=='c'，匹配成功，len+1
		if (pattern[i] == pattern[len]) {
			len++;
			prefix[i] = len;
			i++;
		}
		else {
            // abdabc, i=5时，此时len=2, 'd'!='c'
			if (len > 0) {
				len = prefix[len - 1];
			}
			else{
				prefix[i] = len;
				i++;
			}
		}
	}
}

// 前缀表右移，左补-1（方便使用）
inline void move_prefix(int *prefix , int n) {
	int i;
	for (i = n-1; i > 0; i--) {
		prefix[i] = prefix[i-1];
	}
	prefix[0] = -1;
}
void kmp_search(string text, string pattern) {
	int n = pattern.length(), m = text.length();
	int *prefix = (int *)malloc(sizeof(int) * n);
	prefix_table(pattern, prefix, n);
	move_prefix(prefix, n);
	int i = 0, j = 0;
	while (i < m) {
        // 成功找到了一个匹配串，继续找下一个可能的匹配串
		if (j == n - 1 && text[i] == pattern[j]) {
			cout << i - j + 1 << endl;
			j = prefix[j];
		}
		if (text[i] == pattern[j]) {
			i++; j++;
		}
        // 当前字符不匹配时，利用前缀表更新模式串的查找位置
		else{
			j = prefix[j];
			if (j == -1) {
				j++; i++;
			}
		}
	}
}
int main(){
	string text, pattern;
	cin >> text >> pattern;
	kmp_search(text, pattern);
	return 0;
}
```

# 时间复杂度
生成前缀表的时间复杂度是O(m)，匹配的过程是O(n)，所以整个KMP算法的时间复杂度是O(n+m)。

# 参考教程

- [https://www.bilibili.com/video/BV1Px411z7Yo](https://www.bilibili.com/video/BV1Px411z7Yo)

- [https://www.bilibili.com/video/BV1hW411a7ys](https://www.bilibili.com/video/BV1hW411a7ys)

