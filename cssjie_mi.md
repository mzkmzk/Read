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



# DEMO

http://play.csssecrets.io/


# 5. 字体排印

