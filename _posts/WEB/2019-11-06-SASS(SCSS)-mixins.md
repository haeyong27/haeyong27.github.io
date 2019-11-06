---
layout: post
title:  "SCSS(SASS) - mixin, extend, import, if"
date:   2019-11-06
categories: WEB
tag: [CSS, WEB]
---
- [SASS(SCSS) - mixin, extend, import](#sassscss---mixin-extend-import)
  - [mixin](#mixin)
  - [extend](#extend)
  - [import](#import)
  - [if](#if)
# SASS(SCSS) - mixin, extend, import

## mixin 
```scss
@mixin fontSizeBgColor($size, $color) {
    font-size: $size;
    background-color: $color;
}

#box1 {
    // font-size: 40px;
    // background-color: #ffcccc;
    @include fontSizeBgColor(40px, #ffcccc)
}
```

## extend 
```scss
%boxshape { //#뒤에 붙이고 싶은 이름 
    border-radius: 20px;
    border: 3px solid #f00;
    box-shadow: 0px 3px 11px 0px rgba(0, 0, 0, 0.75);
}

#box1 {
	@extend #boxshape;
}
```

## import 
_filename -> compile안됨 
underscore로 시작하는 파일은 컴파일이 안됨. 

```scss
// _mixins.scss

@import “mixins” // 불러들일 때는 "_"를 안쓴다. 
```

## if 


```scss
body {
    margin: 50px;
}

@mixin textandbgcolor($textcolor, $bgcolor) {
    color: $textcolor;
    background-color: $bgcolor;
}

@mixin theme($mood) {
    @if $mood == 'light' {
        @include textandbgcolor(#333333, #ffff00)
    }
    @else if $mood == 'dark' {
        @include textandbgcolor(#fff, #000000)
    }
    @else {
        @include textandbgcolor(#ff0000, #aaa)
    }
}

#box1 {
    @include theme('light')
}

#box2 {
    @include theme('dark')
}

#box3 {
    @include theme('light2')
}
```





