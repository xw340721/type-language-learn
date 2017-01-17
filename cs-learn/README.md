# 自己的前端css部分集合



##响应式部分



### font-size响应式


* 设置根元素的font-size为10px是好计算其rem的值
* 计算公式为 最小font-size+(最大font-size - 最小font-size)/(最大屏幕-最小屏幕) * (当前屏幕 - 最小的屏幕)


```css

:root{
  font-size: 10px;
}


$min-font-size: 1.4rem;
$max-font-size: 1.8rem;
$min-screen: 600px;
$max-screen: 1200px;

article {
  font-size: $min-font-size;
}

@media (min-width: $min-screen) and (max-width: $max-screen) {
  article {
	font-size: calc(1.4rem + (2 - 1.4) * ((100vw - $min-screen) / (1200 - 800)));
  }
}

@media (min-width: $max-screen) {
  article {
	font-size: $max-font-size;
  }
}
```


### 响应式media


```html
<figure>
  <iframe src="https://www.youtube.com/embed/4Fqg43ozz7A"></iframe>
</figure>
```


```css

figure {
  height: 0;
  padding-bottom: 56.25%; /* 16:9 */ 
  position: relative;
  width: 100%;
}
iframe {
  height: 100%;
  left: 0;
  position: absolute;
  top: 0;
  width: 100%;
}
```

* 16/9 = 100%/h  =》h = 9/16*100   



##下拉菜单处理

* 问题 当菜单隐藏为0 不隐藏为100%时候 其transistion 无用
采用方式为 将height 改写成max-height

```css

.div{
	max-height:0;
	transistion:max-height 1s;
}

.div.on{
	max-height:1000px;
}