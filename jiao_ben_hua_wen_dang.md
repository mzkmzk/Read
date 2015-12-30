# 脚本化文档

`Document Object Model`

![文档节点的部分层次结构](QQ20151230-0.png)

获取文档元素
```javascript
//ID
document.getElementsById('ID_K');

//Name 返回NodeList
document.getElementsByName("name_K");

//根据标签 
//返回NodeList
//因为HTML不区分大小写,所以这个也不区分大小写.
var first_p = document.getElementTagName("p")[0];
first_p_span = first_p.getElementsTagName("span");


```
