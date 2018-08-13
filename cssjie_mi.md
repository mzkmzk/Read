# CSS揭秘

# 1. 引言

能猜想到收货不大,但是因为强迫症而读完了

# 2. 背景与边框

## 2.1 半透明边框

http://demo.404mzk.com/cssjiemi/01-translucent-borders.html

```css
body{
    background: url('./images/stone-art.jpg')
}
div{
    border: 10px solid hsla(0, 0%, 100%, .5);;
    background: white;
    

    max-width: 20em;
    padding: 2em;
    margin: 2em auto 0;
    font: 100%/1.5 sans-serif;
}
.success{
    background-clip: padding-box;
}

.error{
    /* 默认border-box  所以背景background 的白在底部 看不出透明的感觉 */
    /*background-clip: padding-box;*/
}
```

![](/assets/QQ20180731-231629.png)

## 2.2 多重边框

http://demo.404mzk.com/cssjiemi/02-multiple-borders.html

```css
.box-shadow{
    width: 100px;
    height: 60px;
    margin: 25px;
    background: yellow;
            /* offset-x offset-y blur-radius spread-radius color*/
    box-shadow: 0 0 0 10px #655,
                0 0 0 15px deeppink,
                0 2px 5px 15px rgba(0,0,0.6);
}
```

需要注意的是

1. box-shadow是一层层的 像#655 在最里扩张半径为10 而deeplink设为15 其实只有5px的扩张半径
2. box-shadow属于外圈 即不在点击范围内 设置内嵌 需要在属性前加inset

![](/assets/QQ20180731-234152.png)

# DEMO

http://play.csssecrets.io/


# 5. 字体排印

