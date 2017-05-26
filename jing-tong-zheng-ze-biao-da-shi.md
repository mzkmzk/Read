# 精通正则表达式

# 1. 正则表达式入门

`^`在字符组中,表示的是要匹配`一个未列出的字符`

而不是`不要匹配列出的字符`

在字符组里的连字符`-`不在字符组的开头,都是用来表示范围的

神奇的转义

转义字符在字符组内无效

要想在复杂性和完整性求得平衡

一个重要的因素是了解待搜索文本

# 4. 表达式的匹配原理

正则引擎分类

1. DFA(符合或不符合POSIX标准的都属此类) (不支持捕获醒括号和回溯)
2. 传统型NFA (支持忽略优先量词)
3. POSIX NFA

```javascript
//忽略优先量词是否支持,支付则是传统NFA
if ( 'nfa not'.match(/nfa|nfa not/).length === 1 ) { //只匹配了nfa 没有批核nfa not
    console.log('传统NFA')
}else{
    var start_time = new Date().getTime(),
        end_time;
    "==XX=============================".match(/X(.+)+X/);
    end_time = new Date().getTime();
    if (end_time - start_time > 3000) {
        console.log('POSIX NFA');
    }else {
        console.log('DNFA');
    }
}
```

## 规则

### 优先选择最左端的匹配结果

### 匹配量词是匹配优先的

匹配量词(? * + 以及{min, max})



                                                                                                                                                                                             