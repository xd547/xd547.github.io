---
layout: post
title: "Octopress中一个神奇的bug－无法选中代码"
date: 2014-10-18 21:05
comments: true
categories: ['bugs']
---

使用 OctoPress 作为 blog 的程序主要就是看中它的代码高亮的功能，但是它出现了一个神奇的bug。
无法选中代码（其实不是真的无法选中，后面说）。
描述：将代码高亮的主题从dark切换到light后，所有的代码高亮中的代码无法被选中。    
发现：将代码高亮的代码切换回dark，bug消失。于是搜索代码中的 `@if $solarized == light` 。
反复确认后发现将 ｀_solarized.scss｀ 中的第31行注释后bug消失（当然样式不正确）。
```scss _solarized.scss
$base03:          #002b36 !default; //darkest blue
$base02:          #073642 !default; //dark blue
$base01:          #586e75 !default; //darkest gray
$base00:          #657b83 !default; //dark gray
$base0:           #839496 !default; //medium gray
$base1:           #93a1a1 !default; //medium light gray
$base2:           #eee8d5 !default; //cream
$base3:           #fdf6e3 !default; //white
$solar-yellow:    #b58900 !default;
$solar-orange:    #cb4b16 !default;
$solar-red:       #dc322f !default;
$solar-magenta:   #d33682 !default;
$solar-violet:    #6c71c4 !default;
$solar-blue:      #268bd2 !default;
$solar-cyan:      #2aa198 !default;
$solar-green:     #859900 !default;

$solarized: dark !default;

@if $solarized == light {

  $_base03: $base03;
  $_base02: $base02;
  $_base01: $base01;
  $_base00: $base00;
  $_base0:  $base0;
  $_base1:  $base1;
  $_base2:  $base2;
  $_base3:  $base3;

  $base03:  $_base3;
  $base02:  $_base2;
  $base01:  $_base1;
  $base00:  $_base0;
  $base0:   $_base00;
  $base1:   $_base01;
  $base2:   $_base02;
  $base3:   $_base03;
}

/* non highlighted code colors */
$pre-bg: $base03 !default;
$pre-border: darken($base02, 5) !default;
$pre-color: $base1 !default;
```
查看代码发现 `$base03` 被设置为白色（#fdf6e3），于是搜索 `$base03`，发现 `_syntax.scss` 的第31行（又是31）
下面单独列出相关的行
```scss
.gist .highlight, figure.code .highlight {
  @include selection(adjust-color($base03, $lightness: 23%, $saturation: -65%), $text-shadow: $base03 0 1px);
}
```
看到这里就真相大白了，不是无法选中。而是选中颜色是 **白色** 而背景也是白色，于是 bug 就出现了，选中颜色和背景一样，
看起来就像没有选中。
暴力的将其注释掉。bug 解决。
