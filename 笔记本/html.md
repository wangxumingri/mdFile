布局问题：

div居中：

```
div {
     margin: 0 auto;
     width: 80%;
     height: 80%;
}
```



表格在div中居中：

```
div {
     margin: 0 auto;
     width: 80%;
     height: 80%;
}
table{
    align="center";
}
<div>
        <table width="80%" border="1px" cellpadding="0"  cellspacing="0" >
        </table>
</div>
```



<https://blog.csdn.net/xujing19920814/article/details/66970601/> 

​	单元格水平垂直居中：

```
td{
     vertical-align: middle;
    text-align: center;  
}
```

<https://baijiahao.baidu.com/s?id=1622460301039180175&wfr=spider&for=pc> 

单行文本水平垂直居中：

```
div{
    width : 500px;
    height:400px;
    text-align = center; // 使内部文本水平居中
}

div p {
    line-height:400px // 垂直居中：值与父标签的高度一致
}
```

