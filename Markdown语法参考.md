# 一级标题
## 二级标题
### 三级标题
#### 四级标题
##### 五级标题
###### 六级标题

---
此为分割线


**MD语法-此为加粗文本**

*MD语法-此为斜体文本*


<u>MD语法-下划线</u>


~~MD语法，删除线~~

* * *
此为印象笔记的分割线语法


引用实例:
>近日，印象笔记宣布完成重组。作为Evernote已在中国独立运营近6年的品牌，印象笔记将成为由中方控股的中美合资独立运营实体，并获得红杉宽带跨境数字产业基金首轮数亿元人民币投资。
>>近日，印象笔记宣布完成重组。作为Evernote已在中国独立运营近6年的品牌，印象笔记将成为由中方控股的中美合资独立运营实体，并获得红杉宽带跨境数字产业基金首轮数亿元人民币投资。
>>
>>>多层级引用

#### 列表或数字列表

使用 iOS 版本印象笔记如何快速保存内容？
1. 启用印象笔记 Widget ——印象笔记·剪贴板
2. 复制粘贴任意内容
     * 微信
3. 滑动到 Widget 插件区域即可完成保存
印象笔记·剪贴板有什么特点？
* 快：开启自动模式，可以自动保存剪贴板的任意内容
* 一切：只要可以复制粘贴就可以保存
* 有序：全部保存在「我的剪贴板」笔记本并以时间来命名

#### 待办事项

三只青蛙
* [x] 第一只青蛙
* [ ] 第二只青蛙
* [ ] 第三只青蛙

#### 链接

[印象笔记官网](https://www.yinxiang.com/)

#### 图片
![0d5f468e0865e193a5e38461c2fd2db3.png](en-resource://database/570:1)

![image](https://www.yinxiang.com/blog/wp-content/uploads/2018/07/%E5%94%AE%E2020/6/29 15:532020/6/29 15:537%A5%A8%E5%BE%AE%E4%BF%A1%E5%B0%81%E9%9D%A22.png)

在图片后面，接上如下标记，可以控制图片的显示样式
@w=300
@h=150
@w=200h=100
@h=100w=200


#### 表格

| 帐户类型 | 免费帐户 | 标准帐户  | 高级帐户   |
| -------- | -------- | --------- | ---------- |
| 帐户流量 | 60M      | 1GB       | 10GB       |
| 设备数目 | 2台      | 无限制    | 无限制     |
| 当前价格 | 免费     | ￥8.17/月 | ￥12.33/月 |

 #### 插入图表


```chart
,预算,收入,花费,债务
June,5000,8000,4000,6000
July,3000,1000,4000,3000
Aug,5000,7000,6000,3000
Sep,7000,2000,3000,1000
Oct,6000,5000,4000,2000
Nov,4000,3000,5000,

type: pie
title: 每月收益
x.title: Amount
y.title: Month
y.suffix: $
```

#### 插入代码

```python
#!/usr/bin/python
import re

line = "Cats are smarter than dogs"

matchObj = re.match( r'(.*) are (.*?) .*', line, re.M|re.I)

if matchObj:
    print "matchObj.group() : ", matchObj.group()
    print "matchObj.group(1) : ", matchObj.group(1)
    print "matchObj.group(2) : ", matchObj.group(2)
else:
    print "No match!!"
```

#### 数学公式

```math
e^{i\pi} + 1 = 0
```

#### 插入流程图

```mermaid
graph TD
A[模块A] -->|A1| B(模块B)
B --> C{判断条件C}
C -->|条件C1| D[模块D]
C -->|条件C2| E[模块E]
C -->|条件C3| F[模块F]
```

#### 插入时序图

```mermaid
sequenceDiagram
A->>B: 是否已收到消息？
B-->>A: 已收到消息
```

#### 插入甘特图

```mermaid
gantt
title 甘特图
dateFormat YYYY-MM-DD
section 项目A
任务1 :a1, 2018-06-06, 30d
任务2 :after a1 , 20d
section 项目B
任务3 :2018-06-12 , 12d
任务4 : 24d
```

#### 生成目录
[TOC]