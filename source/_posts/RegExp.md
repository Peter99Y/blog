---
title: 正则
---

正则表达式可以实现对字符串的匹配，替换和字符提取。

-   var reg = new RegExp("表达式"); 构造函数;
-   var reg = /表达式/; 字面量;

`^  		边界符 - 以谁开头`
`$  		边界符 - 以谁结尾`
`\	   	转义符 - 将下一个字符标记为原义字符; 如		\+		\.		\?		\*        \^`  
`|              匹配两个中其中一个`  
`[]		原子表 - 匹配括号内其中一个；原子表自动解析特殊字符`
`.		除换行符外任何字符`
`()  	        原子组 - 标记一个子表达式的开始和结束位置`

---

`\d 	等价[0-9]; \D;`
`\w  等价[a-zA-Z0-9_]; \W;`
`\s  匹配任何空白符号; 等价[\t\r\n\v\f], \S;  `
`空白符：包括空格 \t制表符  \r回车符  \n换行符  \v垂直制表符  \f换页符`  
`\b  匹配边界`

---

###### 元字符
`*  >= 0次`
`+  >= 1次`
`?  要么1次  or  要么0次`
`{n}   重复必须 n次`
`{n,}  重复必须 >= n次`
`{n,m} n次 <=  必须重复 且 <= m次`

---

###### 修正符
`i 	忽略大小写`
`g 	单行的全局匹配,否则只匹配上第一个就停止`
`m     多行匹配`
`y 	(罕)匹配上就进行匹配下一个，否则返回null停止匹配(大文本时效率比g高)`
`u 	表示按unicode(utf-8)匹配(主要针对多字节比如汉字)`
`s 	匹配换行符`

```
console.log("这\"m\"是");            这"m"是

"umu".replace(/u/,'@');	             @mu
"umu".replace(/u/g,'@');	         @m@
```

---

    /abc/.test('dabcd');  	             t	匹配连着的abc字符
    /[abc]/.test('dad');  		         t	匹配abc其中一个字符
    /^ab/.test('abcddd');  	             t

---

    "hds".match(/[a-z]+/);		        ['hds']
    "hds".match(/[a-z]/g);		        ['h','d','s']
    "123789".match(/\d{3}/g);	        [123','789']
    /\d+.\d+/.test(22033);		        true
    /\d+\.\d+/.test(22.33);		        true

---

    /^\d$/.test('33');			                f
    /^abc$/.test('abcabc');                   f
    /\d{3}/.test("12a903n");	                t  	结论: 没加^$就是模糊匹配，加了就是精确匹配
    /^\d{3}$/.test(1290);		              f
    /a|\+/.test('111.@com+');	                t
    /^abc{3}$/.test('abccc');		          t
    /^(abc){3}$/.test('abcabcabc');	            t   整个原子组重复3次
    /^[a-zA-Z0-9]$/.test('M');		          t
    /[0-9]{3}/.test(1293);		                t
    /^[^a-zA-Z0-9\-_,\/!]$/.test('!'); 	        f   中括号里面的^表示取反
    /^\w$/.test('_');			               t
    /^\D$/.test('!');			              t
    /^\s$/.test(' ')			                t
    /\s/.test('\nkk')			              t
    "hok 2021".match( /\d/g );				     ['2','0','2','1']
    "hok 2021".match( /\d+/ );		          ['2021']
    "张三:010-9999,李四:020-9999".match( /\d{3}-\d{4}/g );     ['010-9999','020-9999']
    /^a{3,}$/.test('aaaa');					  t
    /^a{3,16}$/.test('aaa');				  t
    /^a*$/.test('');						  t
    /^a?$/.test('');						  t
    "(sz).+".match(/[().+]/g);                ["(", ")", ".", "+"]

    /^hello\b/.test('hello world')            t

    'hddd'.match(/hd+/);				      hddd		贪婪模式匹配多的要求
    'hddd'.match(/hd+?/);					  hd  		禁止贪婪模式只会匹配少的要求
    'hddd'.match(/hd*?/);					  h
    'hddd'.match(/hd??/);					  h
    'hddd'.match(/hd{2,100}?/);				  hdd
    'hddd'.match(/hd{2,}?/);			      hdd

    /(^\d{3}$)|(^\d{5}(\d|X|x)$)/.test('123')                 t
    /(^\d{3}$)|(^\d{5}(\d|X|x)$)/.test('123456')              t
    /(^\d{3}$)|(^\d{5}(\d|X|x)$)/.test('12345X')              t

    "run\rrun\nrun".match(/^run/g)                ['run']
    "run\rrun\nrun".match(/^run/gm)               ['run','run','run']

---

    let hd = `
    	ddd
    	mm
    `;
    console.log( hd.match(/.+/g) );           ['\tddd', '\tmm']
    console.log( hd.match(/[\s\S]+/) )        ["\n\tddd\n\tmm\n"]

    var reg = new RegExp("\d+\.\d+");
              传的是字符串, 实际是 new RegExp("d+.d+");
    console.log( "\d" == "d" );		          true
    reg.test("d.ddd")			              true

    var reg2 = new RegExp("\\d+\\.\\d+")
              得到 RegExp("\d+\.\d+");
    console.log( reg2.test(22.33) );          true

---

###### 案例

```
let contentType = 'pdf';
let reg = new RegExp(`\\.${contentType}$`, 'i');             /\.pdf$/i
```

`网址`

```
/https?:\/\/\w+\.\w+\.\w+/.test('http://www.cccd.com');
"https://www!hou!com".match(/^https?:\/\/\w+.\w+.\w+$/);	['https://www!hou!com']
```

`日期`

```
var date = "2022-12/23".match( /^\d{4}[-\/]\d{2}[-\/]\d{2}$/ );		['2022-12/23']
var date = "2022-12-23".match( /^\d{4}([-\/])\d{2}\1\d{2}$/ );	    ['2022-12-23'] \1取前面原子组匹配的结果
```

`用户名验证`

```
<input type="text" name="username">

document.querySelector("[name='username']")
.addEventListener("keyup",function(){
 	console.log(this.value.match(/^[a-zA-Z]\w{5,16}$/))
});
```

`获取名字`

```
'张三:101-33,李四:202-44'.match(/[^:\-,\d]+/g);				 ['张三','李四']
'张三:101-33,李四:202-44'.match(/\p{sc=Han}+/gu);			 ['张三','李四']
```

`替换`

```
"2022-22-22".replace(/[-/]/g, '/');                          2022/22/22
"2022-22-22".replace(/[-/]/g, ()=> '/');                     2022/22/22
"/h123h/".replace(/\d+/g, (f)=> '-'+ f +'-');                '/h-123-h/'
main.innerHTML.replace( /(教育)/g, `<a href="https://www.baidu.com">$1</a>`);
main.innerHTML.replace( /教育/g, `<a href="https://www.baidu.com">$&</a>`);
```

`原子组替换操作`

```
/<(h[1-6])>([\s\S]*)<\/\1>/i;                                组编号\1，从左往右依次;
"/abc-h123/".replace(/h([\s\S]*)/, '$1');                    '/abc-123/'

`<h1>k</h1>`.replace(                                         <h1>k</p>
    /<(h[1-6])>([\s\S]*)<\/\1>/i,
    `<$1>$2</p>`
);

"(010)84498434 (020)84323098".replace(                        010-84498434 020-84323098
	/\((\d{3,4})\)(\d{7,8})/g,
	'$1-$2'
);
```

`批量正则验证`

```
document.querySelector('input').addEventListener('keyup', e => {
	const val = e.target.value;
	const regs = [
		/^[a-zA-Z0-9]{3,6}$/,	// 数字或字母3-6位
		/[A-Z]/,				// 这6位内需包含一个大写字母
		/[0-9]/,				// 这6位内需包含一个数字
	];
	let state = regs.every( it => it.test(val) );
	console.log(state ? '正确' : '错误');
});
```

`手机号`

```
/^(13[0-9]|14[5|7]|15\d|18[0|1|2|3|5|6|7|8|9])\d{8}$/

"hdr20.爱世界".match(/\p{L}/gu);	 				  ['h','d','r','爱','世','界'];
"hdr20.爱世界，加油！".match(/\p{P}/gu);			  ['.', '，'，'！']
"hdr20.爱世界，加油！".match(/\p{sc=Han}/gu);		  ['爱', '世', '界', '加', '油']
{sc}表示语言系统, Han表示汉语,Arabic表示阿拉伯语等;
```

###### 原子组

```
let reg = /^[\w-]+@[\w-]+\.com|org$/i;                      注意:  [\w-]+@[\w-]+\.com整体; org整体
let reg2 = /^[\w-]+@[\w-]+\.(com|org)$/i;
                    102@(com.)+
let reg3 = /^[\w-]+@([\w-]+\.)+(com|org|cc|cn|net)$/i;

let mail = document.querySelector('[name="mail"]');
let content = document.querySelector('.mail-span');
mail.addEventListener('keyup',function(){
	content.innerHTML = reg3.test(this.value) ?'邮箱正确':'邮箱错误';
});
```

`?:不记录组`

```
/https?:\/\/(\w+\.\w+\.(?:com|cn))/i;

const main = document.querySelector("main");
main.innerHTML = main.innerHTML.replace(
	/<span>([\s\S]+?)<\/span>/gi,
	`<h4 style="color:red">$1</h4>`
);
```

`?<>组别名`

```
"<h1>k</h1>".replace(
	/<(h[1-6])>(?<m>.*?)<\/\1>/,
	"<p>$<m></p>"
);
```

`?= 后断言匹配`

```
main.innerHTML = main.innerHTML.replace(
	/教育(?=兴国)/g,                                           【教育】后是【兴国】才匹配
	`<a href="https://baidu.com">$&</a>`
);

```

`?<= 前断言匹配`

```
let users = `
	mm: 18742129832,
	kk: 19932315894
`
users = users.replace(/(?<=\d{7})\d{4}/gi, f=>{
	return '*'.repeat(4);                                       【4位数字】前是【7位数字】才匹配
});
```

`?! 不是...为条件匹配`

```
let reg = /[a-z]+$/i;
let reg2 = /[a-z]+(?!\d+)$/i;
'aaa20220215kk'.match(reg2)                                     kk

const inp = document.querySelector(`[name="username"]`);
inp.addEventListener("keyup",function(){

	const reg = /^[a-z]{1,6}$/i;

	const reg2 = /^(?!sb)[a-z]{1,6}$/i;                         不能是sb开头

	const reg3 = /^(?!.*sb)[a-z]{1,6}$/i;
	              ^$修饰[a-z],长度1-6位,且里面不含sb字符,并且sb字符前有或没有字符
	console.log(
		this.value.trim().match(reg3)
	)
});
```

`?<!`

```
let str = `
	https: '//oss.baidu.com/1.jpg',
	https: '//baidu.com/2.jpg'
`

const reg  = /https:\/\/([a-z]+)?(?<!www)\..+?/gi;
str.replace(reg, f=>{
	return 'https://www.baidu.com'
})
```
