---
layout: post
title:  "SCSS(SASS) - mixin, extend, import"
date:   2019-11-06
categories: WEB
tag: [CSS, WEB]
---
- [SASS(SCSS) - mixin, extend, import](#sassscss---mixin-extend-import)
  - [mixin](#mixin)
  - [extend](#extend)
  - [import](#import)
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






