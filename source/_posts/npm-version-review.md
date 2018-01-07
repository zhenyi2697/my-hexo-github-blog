---
title: NPM Version 管理总结
date: 2016-11-20 12:26:52
tags:
- tech
- npm
- Node.js
---
## Semantic Version 版本格式：

    major.minor.fix

其中:

* major: 不向下兼容API修改
* minor: 向下兼容的功能性新增
* fix: 向下兼容的问题修正

当major为0时，可以认为版本不稳定。即使minor的修改，也会引入兼容性问题，这一设计主要是为了开发者可以尽快的进行迭代。但是fix版本依然应该保持向下兼容。

关于Semantic Version的更多信息，参见： [http://semver.org/](http://semver.org/)

## npm 的version管理

### Primitive Comparators：

* <
* <=
* \>
* \>=
* =

这些很好理解，就不做过多解释了。另外，可以用`||`表示"或"的关系`空格`表示"且"的关系，例如：

    1.2.7 || >=1.2.9 <2.0.0

满足的区间为 1.2.7, 1.2.9, and 1.4.6, 但是 1.2.8 or 2.0.0 不满足以上区间

### 高级区间运算符

#### hyphen(-): X.Y.Z - A.B.C

表示一个区间，包括两头，不足的version用0补足

#### X Range(X,x,\*): 1.2.x 1.X 1.2.* *

代表任意版本

#### Tilde Range(~): ~1.2.3 ~1.2 ~1

  如果有minor version，那么久允许fix level的version。否则，只允许minor level的version。
    例如：

      ~1.2.3 := >=1.2.3 <1.(2+1).0 := >=1.2.3 <1.3.0
      ~1.2 := >=1.2.0 <1.(2+1).0 := >=1.2.0 <1.3.0 (Same as 1.2.x)
      ~1 := >=1.0.0 <(1+1).0.0 := >=1.0.0 <2.0.0 (Same as 1.x)

#### Caret Ranges(^): ^1.2.3 ^0.2.5 ^0.0.4

允许[major, minor, patch]从左起第一个不是0的部分之后的部分的范围变动。虽然在semver的spec里，只有major版本的改变会产生兼容性问题。但是在实际上，当主版本号为0时，minor版本经常会引入行为上的变化。例如：

      ^1.2.3 := >=1.2.3 <2.0.0
      ^0.2.3 := >=0.2.3 <0.3.0
      ^0.0.3 := >=0.0.3 <0.0.4
      ^1.2.3-beta.2   >=1.2.3-beta.2 <2.0.0
      ^0.0.3-beta.2   >=0.0.3-beta.2 <0.0.4

一句话概括tilde和caret的区别：
* tilde match most recent minor version (the middle number):

  ~1.2.3 := >=1.2.3-0 <1.3.0-0 (“Reasonably close to 1.2.3”)
* caret updates to the most recent major version:

  ^1.2.3 := >=1.2.3-0 <2.0.0-0 (“Compatible with 1.2.3”)

caret其实是认为只要major版本不改变，那么兼容性就可以保证。然而tilde更保守一点，只接受minor或者fix的更新。另外，对于正在开发中的初始版本，tilde和caret的功能是一样的。

参考资料：
* http://semver.org/lang/zh-CN/
* https://docs.npmjs.com/misc/semver
* http://stackoverflow.com/questions/22343224/difference-between-tilde-and-caret-in-package-json
* http://fredkschott.com/post/2014/02/npm-no-longer-defaults-to-tildes/
* http://bytearcher.com/articles/semver-explained-why-theres-a-caret-in-my-package-json/
