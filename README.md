# 推荐在博客观看教程：https://encaik.top/blog/canvas.html

# 学习canvas制作一个代码美化截图页面

## 使用canvas

在HTML中插入canvas标签,并添加id为唯一标识方便通过DOM获取。

```html
<canvas id="canvas"></canvas>
```

用js操作DOM获取canvas标签，并设置宽高，然后拿到canvas上下文。

```js
const canvas = document.getElementById('canvas');
const width = window.innerWidth;
const height = window.innerHeight;
canvas.width = width;
canvas.height = height;
const ctx = canvas.getContext('2d');
```

完成这一步就可以在页面上使用canvas了，接下来开始绘制图形。

## 绘制图形

以下为已完成图示效果：

![canvas图示](/img/blog/canvas-1.png)

### 定义需要的常量及变量

```js
let content =
`
/* 绘制圆角矩形 */
function roundRect(ctx, x, y, width, height, radius, fillColor) {
    if (2 * radius > width || 2 * radius > height) { return width = height = 2 * radius; }
    ctx.save();
    ctx.translate(x, y);
    ctx.beginPath(0);
    ctx.arc(width - radius, height - radius, radius, 0, Math.PI / 2);
    ctx.lineTo(radius, height);
    ctx.arc(radius, height - radius, radius, Math.PI / 2, Math.PI);
    ctx.lineTo(0, radius);
    ctx.arc(radius, radius, radius, Math.PI, Math.PI * 3 / 2);
    ctx.lineTo(width - radius, 0);
    ctx.arc(width - radius, radius, radius, Math.PI * 3 / 2, Math.PI * 2);
    ctx.lineTo(width, height - radius);
    ctx.closePath();
    ctx.shadowBlur = 20;
    ctx.shadowColor = fillColor || "#000";
    ctx.fillStyle = fillColor || "#000";
    ctx.fill();
    ctx.restore();
}
`;
let contentArray = content.split("\n");
let lineHeight = 25;
let fontWidth = 10;
let rectHeight = contentArray.length * lineHeight + 20;
let rectWidth = 0;
for (let index = 0; index < contentArray.length; index++) {
    if (contentArray[index].split("").length * fontWidth > rectWidth) {
        rectWidth = contentArray[index].split("").length * fontWidth
    }
}
```

这些变量的作用分别是：

- content：显示在框里的文本
- contentArray：通过换行符拆分的由行组成的数组
- lineHeight：显示文本的行高
- fontWidth：显示文本的字宽
- rectHeight：框的高度，由行数乘以行高加上20的头部计算得来
- rectWidth：框的宽度，由每行的字符数乘以字宽计算得来

### 绘制背景

背景为一个与画布相同大小的填充矩形，代码如下：

```js
/* 绘制背景色 */
function background(ctx, x, y, width, height, fillColor) {
    ctx.fillStyle=fillColor || "#fff";
    ctx.fillRect(x,y,width,height);
}
background(ctx,0,0,width,height,"#d2d2d2")
```

### 绘制圆角矩形框

圆角矩形右四个1/4圆弧和四段线段组成，从右下角开始顺时针画，代码如下：

```js
/* 绘制圆角矩形 */
function roundRect(ctx, x, y, width, height, radius, fillColor) {
    if (2 * radius > width || 2 * radius > height) { return width = height = 2 * radius; }
    ctx.save();
    ctx.translate(x, y);
    ctx.beginPath(0);
    ctx.arc(width - radius, height - radius, radius, 0, Math.PI / 2);
    ctx.lineTo(radius, height);
    ctx.arc(radius, height - radius, radius, Math.PI / 2, Math.PI);
    ctx.lineTo(0, radius);
    ctx.arc(radius, radius, radius, Math.PI, Math.PI * 3 / 2);
    ctx.lineTo(width - radius, 0);
    ctx.arc(width - radius, radius, radius, Math.PI * 3 / 2, Math.PI * 2);
    ctx.lineTo(width, height - radius);
    ctx.closePath();
    ctx.shadowBlur = 20;
    ctx.shadowColor = fillColor || "#000";
    ctx.fillStyle = fillColor || "#000";
    ctx.fill();
    ctx.restore();
}
roundRect(ctx, width/2-rectWidth/2, height/2-rectHeight/2, rectWidth, rectHeight, 8, 'rgb(50,50,50)');
```

### 绘制左上角小按钮

小按钮为三个填充圆形，代码如下：

```js
/* 绘制左上角三个小按钮 */
function circle(ctx, x, y, radius, fillColor) {
    ctx.beginPath();
    ctx.arc(x, y, radius, 0, Math.PI * 2);
    ctx.closePath();
    ctx.fillStyle = fillColor || "#fff";
    ctx.fill();
}
circle(ctx, width/2-rectWidth/2+20, height/2-rectHeight/2+20, 8, 'rgb(197,79,103)');
circle(ctx, width/2-rectWidth/2+45, height/2-rectHeight/2+20, 8, 'rgb(210,191,77)');
circle(ctx, width/2-rectWidth/2+70, height/2-rectHeight/2+20, 8, 'rgb(103,212,98)');
```

### 绘制文本

文本为每绘制一行就移动位置，否则会重叠在一起，代码如下：

```js
/* 绘制文本 */
function text(ctx, x, y, lineHeight, contentArray, fillColor) {
    ctx.font = "20px bold 微软雅黑";
    ctx.fillStyle = fillColor || "#fff";
    ctx.textAlign = "left";
    ctx.textBaseline = "middle";
    for (let index = 0; index < contentArray.length; index++) {
        if(contentArray[index].trim() === ""){
            contentArray.splice(index,1)
            index --;
        }else{
            ctx.fillText(contentArray[index], x, y + index * lineHeight);
        }
    }
}
text(ctx, width/2-rectWidth/2+20, height/2-rectHeight/2+60, lineHeight, contentArray, 'rgb(97,175,239)');
```

