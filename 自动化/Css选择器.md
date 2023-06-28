# 属性定位

```html
<div id="css_id">
#css_id
<div class="css_id">
.css_id
<div rel="css_id">
[rel=stylesheet] 
[rel]
```


# 属性值定位

```html
<h2 id="999919999">
标签[属性=属性值]
包含
h2[id*="1"]
```

# css层级关系定位

```html
div,p	选择所有 <div> 元素和所有 <p> 元素。
div p	选择 <div> 元素内部的所有 <p> 元素。包括子孙后代。
div>p	选择父元素为 <div> 元素的所有 <p> 元素。只包括子代。
div+p	选择紧接在 <div> 元素之后的所有 <p> 元素。同辈元素。
```


# css索引定位

```html
p:only-child	选择属于其父元素的唯一子元素的每个 <p> 元素。
p:nth-child(2)	选择属于其父元素的第二个子元素的每个 <p> 元素。
p:nth-last-child(2)	同上，从最后一个子元素开始计数。
p:nth-of-type(2)	选择属于其父元素第二个 <p> 元素的每个 <p> 元素。
p:nth-last-of-type(2)	同上，但是从最后一个子元素开始计数。
```

# css元素状态定位

```html
p:empty	选择没有子元素的每个 <p> 元素（包括文本节点）。
#news:target	选择当前活动的 #news 元素。
input:enabled	选择每个启用的 <input> 元素。
input:disabled	选择每个禁用的 <input> 元素
input:checked	选择每个被选中的 <input> 元素。
:not(p)	选择非 <p> 元素的每个元素。
```


[(31条消息) Selenium基础 — CSS选择器定位大全_selenium css定位_测试-八戒的博客-CSDN博客](https://blog.csdn.net/m0_59868866/article/details/127209950)