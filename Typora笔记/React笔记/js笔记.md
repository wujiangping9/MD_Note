1.对象数组按某个属性排序

```react
//定义数组 
let arr = [
    {name:'zopp',age:0},
    {name:'gpp',age:18},
    {name:'yjj',age:8}
];
//比较方法
 compare = (property) => {
    return function(a,b){
        let value1 = a[property];
        let value2 = b[property];
        return value1 - value2;
   	}
}
 //调用方法排序
 arr.sort(this.compare('age'))
```

2.input标签内部数据格式化

```react
//方法    
tuitionKeyup = e => {
        e.target.value = e.target.value.replace(/[^\d+ /]/g,""); //清除"数字"和"+ /"以外的字符
        e.target.value = e.target.value.replace(/^\./g,""); //验证第一个字符是数字
        e.target.value = e.target.value.replace(/\.{2,}/g,"."); //只保留第一个".", 清除多余的
        e.target.value = e.target.value.replace(".","$#$").replace(/\./g,"").replace("$#$",".");
        e.target.value = e.target.value.replace(/^(\-)*(\d+)\.(\d\d).*$/,'$1$2.$3');
    }
---------------------------------------------------------------------------------------------------
//调用的标签
    <Input onInput={this.tuitionKeyup}  value={text}/>
```

3.