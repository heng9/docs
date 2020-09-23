* 子div设定为行内块元素(display:inline-block)
* 父div设置行高(line-height)使子div上下居中
* 父div设置文本居中(text-align:center)使子div左右居中


* 父div外层配置一个div，同时设置为表格元素 (display: table)，宽度为100%
* 父div配置为表格单元格元素 (display: table-cell)
* 父div配置居中属性 (vertical-align: middle)，使子div上下居中
* 子div通过margin配置左右居中 (margin: 0 auto)


* 父div标记下定位 (position:relative|absolute|fixed);子div绝对定位 (position:absolute)
* 子div上下居中：top:50%; margin-top:-h/2; 或是 bottom:50%; margin-bottom:-h/2;
* 子div左右居中: left:50%; margin-left:-w/2; 或是 right:50%; margin-right:-w/2;


* 父div标记下定位 (position:relative|absolute|fixed|sticky);子div绝对定位 (position:absolute)
* 子div上下居中：top:0; bottom:0; margin-top:auto; margin-bottom:auto
* 子div左右居中：left:0; right:0; margin-left:auto; margin-right:auto