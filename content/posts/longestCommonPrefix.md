---
title: "最长公共前缀"
date: 2022-05-19T19:05:49+08:00
#description: <descriptive text here>

# weight: 1

# aliases: ["/first"]

tags: ["最长公共前缀"]
categories: ["leetCode"]
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: true
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: false
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
cover:
image: "<image path/url>" # image path/url
alt: "<alt text>" # alt text
caption: "<text>" # display caption under cover
relative: false # when using page bundles set this to true
hidden: true # only hide on current single page
---

## leetcode-14-最长公共前缀

[题目地址](https://leetcode.cn/problems/longest-common-prefix/)

#### 题目描述

编写一个函数来查找字符串数组中过的最长公共前缀。

如果不存在公共前缀，返回空字符串“”。

**示例 1:**

```javascript
输入：strs = ["flower","flow","flight"]
输出："fl"
```

**示例 2:**

```
输入：strs = ["dog","racecar","car"]
输出：""
解释：输入不存在公共前缀。
```

**提示：**

- `1 <= strs.length <= 200`
- `0 <= strs[i].length <= 200`
- `strs[i]` 仅由小写英文字母组成

#### 解题思路

获取字符串数组中的最长公共前缀，就是取出字符串中的每一个子字符一一进行对比，如果都相同，则比较下一位子字符，否则终止对比。

具体思路如下：

1. let curIndex = 0; 记录当前对比字符的下标
2. let curStr = ""; 记录当前比较的字符的值
3. let flag = true; 判断本次字符串比对是否全部相同
4. 使用while循环，当flag为假时推出循环。
5. 如果当前处理的是字符串数组中的第一个字符串，说明开始了一次新的循环，根据下标`curIndex`更新`curStr`。
6. 否则通过`curIndex`获取当前的字符串和`cueStr`对比，如果不同，则`flag = false`推出循环。
7. 如果处理到了字符串数组中的最后一个字符串，则说明本次循环成功，更新下标向后移一位。
8. 当while循环结束时，说明已经获取到了第一个不满足条件的下标位置，根据`curIndex`截取第一个字符串。



#### 代码实现

```javascript
function longestComminPrefix(strs) {
  if(strs.length === 0) return "";
  let len = strs.length;
  let curIndex = 0;
  let curStr = "";
  let flag = true;
  
  while(flag) {
    for(let i = 0 ;i < len;i ++) {
      if(i === 0) {
        	if(strs[i][curIndex]) {
            curStr = strs[i][curIndex];
          }else {
            flag = false
          }   
      }else if(strs[i][curIndex] !== curStr){
        flag = false;
        break;
      }
      if(i === len - 1) {
        curIndex ++;
      }
    }
  }
  return strs[0].substr(0,curIndex);
}
```



