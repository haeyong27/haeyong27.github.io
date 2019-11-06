---
layout: post
title:  "SCSS(SASS) - variable, nesting"
date:   2019-11-06
categories: WEB
tag: [CSS, WEB]
---


- [SASS(SCSS)](#sassscss)
  - [variable](#variable)
  - [nested](#nested)
    - [첫번 째](#%ec%b2%ab%eb%b2%88-%ec%a7%b8)
    - [두번 째](#%eb%91%90%eb%b2%88-%ec%a7%b8)
    - [세번 째(중복되는 코드)](#%ec%84%b8%eb%b2%88-%ec%a7%b8%ec%a4%91%eb%b3%b5%eb%90%98%eb%8a%94-%ec%bd%94%eb%93%9c)


# SASS(SCSS)
[Sass: Syntactically Awesome Style Sheets](https://sass-lang.com/)
[Ch02-01  Sass (SCSS) - 변수 (Variables) - YouTube](https://www.youtube.com/watch?v=Y_ThMpMyb4I&list=PLjQHn5jzATks_cfXVS56j21rdX4FMdQaa&index=9)

## variable
```scss
$bg-color: red;


body {
    background-color: blanchedalmond;
}


#box1 {
    color: yellow;
    background-color: $bg-color;
    width: 100px;
}

```

## nested
### 첫번 째

```html
    <div id="box1">
        box1<br>
        <a href="#">button1</a>
        <div id="box2">
            box2 <br>
            <a href="#">buttton2</a>
        </div>
    </div>
```
```scss
  
  #box1 {
    font-size: 40px;
    background-color: #ffcccc;
    
    border-radius: 20px;
    border: 3px solid #f00;
    box-shadow: 0px 3px 11px 0px rgba(0, 0, 0, 0.75);

    & > a {
        color:blue;
        text-decoration: none;

        &:hover {
            color: #000;
            text-decoration: underline;
          }
      }
    &:hover {
        background-color: #ccc;
      } 

  }
  
  #box1 #box2 {
    font-size: 20px;
    background-color: #e9e9e9;
    
    border-radius: 20px;
    border: 3px solid #f00;
    box-shadow: 0px 3px 11px 0px rgba(0, 0, 0, 0.75);

    & > a {
        color: #ee6633;
        text-decoration: none;
        
        &:hover {
            color: #a22;
            text-decoration: underline;
          }
      }
  }
  

```

box2를 box1에 집어넣지 않은 이유는 가독성 

### 두번 째
```html
    <div id="box1">
        <div id="box1-title">box1 title</div>
        <a href="#">button1</a>
        <div id="box2">
            box2 <br>
            <a href="#">buttton2</a>
        </div>
    </div>
```
```scss

  #box1 {

	  &-title {
  	font-style: italic;
	    text-decoration: underline;
}
```

### 세번 째(중복되는 코드)
```scss
#box1 {
  border-radius: 20px;
  border: 3px solid #f00;

 &-title {
    border-radius: 20px;
    border: 3px solid #f00;
  }
}
```

```scss
#box1 {
  &, &-title {
    border-radius: 20px;
    border: 3px solid #f00;
  }
}
```



