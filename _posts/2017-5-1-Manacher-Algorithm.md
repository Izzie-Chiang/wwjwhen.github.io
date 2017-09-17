layout: post
title: Manacher's Algorithm
---

<p class="meta">1 May 2017 - Beijing</p>

回文串的问题比较经典，记得当年在蓝桥杯的赛场上做过，今天又在Leetcode上面遇见了，感觉有必要清算一下。从Brute Force的算法开始，到稍显机巧的算法再到最后的
相当专业的Manacher's Algorithm，每一步都体现出了一个问题，那就是*算法是人类智慧的结晶*。


~~~~
string Manacher(string s) {
    // Insert '#'：预处理的技巧，通过这样的填充无论是偶数串还是奇数串都变成了偶数长度，
    //但是前面需要加上一个搜索的界限，后边是'\0'
    string t = "$#";
    for (int i = 0; i < s.size(); ++i) {
        t += s[i];
        t += "#";
    }
    // Process t
    vector<int> p(t.size(), 0);
    int mx = 0, id = 0, resLen = 0, resCenter = 0;
    for (int i = 1; i < t.size(); ++i) {
        p[i] = mx > i ? min(p[2 * id - i], mx - i) : 1; // 这边的故事就是一个利用对称点省去
        //繁琐的计算，尽最大可能利用已有的结果，但是这个要求记录最大的右边界
        while (t[i + p[i]] == t[i - p[i]]) ++p[i];
        if (mx < i + p[i]) {
            mx = i + p[i];
            id = i;
        }
        if (resLen < p[i]) {
            resLen = p[i];
            resCenter = i;
        }
    }
    return s.substr((resCenter - resLen) / 2, resLen - 1);
}
~~~
{: .language-c++}

