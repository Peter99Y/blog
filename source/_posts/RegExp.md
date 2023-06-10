---
title: 正则
---

-   字面量
    var reg = /\w+/;

-   构造函数
    用双引号创建正则时，如\w 需要添加转义符；

```
    var reg = new RegExp("^\\w+$");     /^\w+$/
    var reg = new RegExp(/^\w+$/);      /^\w+$/

    var reg = new RegExp("\d");         /d/ 转义了字母d仍然是字母d;
    reg.test(1)                         false; 等价于 /d/.test(1)
    reg.test('ddd')                     true

    var reg = new RegExp("\\d");        /\d/
    reg.test(1)                         true; 等价于 /\d/.test(1)

    var reg2 = new RegExp("\\d+\\.\\d+")
    var reg2 = new RegExp(/\d+\.\d+/);
    console.log( reg2.test(22.33) );          true
```

`^  边界符：以谁开头`
`$  边界符：以谁结尾`

```

/^[^a-zA-Z0-9\-_,\/!]$/.test('!'); false 中括号里面的^表示取反
/abc/.test('wabcw'); true 匹配连着的 abc 字符
/^ab/.test('abcddd'); true

/^\d$/.test('33');			            false
/^abc$/.test('abcabc'); false

```

`\	   	转义符：将下一个字符标记为原义字符; 如		\+		\.		\?		\*        \^`

```

var str = "这是\"转义符\""; 这是"转义符"
/a|\+/.test('@com+'); true

```

`|  匹配两个中其中一个`
`[]	原子表： 匹配括号内其中一个；原子表自动解析特殊字符`

```

/[abc]/.test('dad'); true 只匹配 abc 之中一个字符
/^[a-zA-Z0-9]$/.test('M'); true

"(sz).+".match(/[().+]/g); ["(", ")", ".", "+"]

```

`.		除换行符外任何字符`

```

    /\d+.\d+/.test(22033);		        true
    /\d+\.\d+/.test(22.33);		        true

    let hd = `
    	ddd
    	mm
    `;
    var res = hd.match(/.+/g);          ['\tddd', '\tmm']

```

`() 原子组： 标记一个子表达式的开始和结束位置`

```

    /^abc{3}$/.test('abccc');		          true
    /^(abc){3}$/.test('abcabcabc');	          true     整个原子组重复3次

    /(^\d{3}$)|(^\d{5}(\d|X|x)$)/.test('123')                 t
    /(^\d{3}$)|(^\d{5}(\d|X|x)$)/.test('123456')              t
    /(^\d{3}$)|(^\d{5}(\d|X|x)$)/.test('12345X')              t

```

---

`\d     等价[0-9]`
`\D     非数字`

```

    /^\D$/.test('!');			              true

```

`\w     等价[a-zA-Z0-9_]`
`\W     非字母`

```

    /^\w$/.test('_');			              true

```

`\s  匹配任何空白符号; 等价[\t\r\n\v\f]`
`\S  非空白符号`
空白符：包括空格 \t 制表符 \r 回车符 \n 换行符 \v 垂直制表符 \f 换页符

```

    /^\s$/.test(' ')			              true
    /\s/.test('\nkk')			              true

    let hd = `
    	ddd
    	mm
    `;
    var res = hd.match(/.+/g);              ['\tddd', '\tmm']
    var res = hd.match(/[\s\S]+/);          ["\n\tddd\n\tmm\n"]

```

`\b  匹配边界`

```

    /^hello\b/.test('hello world')            t

```

---

###### 元字符

`* >= 0 次`
`+ >= 1 次`
`? 要么 1 次 or 要么 0 次`

```

    /^a*$/.test('');				    true
    "hds".match(/[a-z]+/);		        ['hds']
    /^a?$/.test('');				    true

```

`{n} 重复必须 n 次`
`{n,} 重复必须 >= n 次`
`{n,m} n次 <= 必须重复 且 <= m次`

```

/\d{3}/.test("12a903n"); true
/^\d{3}$/.test(1290); false
/[0-9]{3}/.test(1293); true

/^a{3,}$/.test('aaaa');					true
/^a{3,16}$/.test('aaa'); true

```

贪婪模式：匹配多的要求；
禁止贪婪：只匹配少的要求；

```

    'hddd'.match(/hd*?/);					  h
    'hddd'.match(/hd+/);				      hddd
    'hddd'.match(/hd+?/);					  hd
    'hddd'.match(/hd??/);					  h

    'hddd'.match(/hd{2,100}?/);				  hdd
    'hddd'.match(/hd{2,}?/);			      hdd

```

---

###### 修正符

`i 	忽略大小写`
`g 	单行的全局匹配,否则只匹配上第一个就停止`

```

    "hds".match(/[a-z]/g);		        ['h','d','s']
    "123789".match(/\d{3}/g);	        ['123','789']

    "hok 2021".match( /\d+/ );		    ['2021']
    "hok 2021".match( /\d/g );		['2','0','2','1']

```

`m  多行匹配`

```

    "run\rrun\nrun".match(/^run/g)                ['run']
    "run\rrun\nrun".match(/^run/gm)               ['run','run','run']

```

`y 	(罕)匹配上就进行匹配下一个，否则返回null停止匹配(大文本时效率比g高)`
`u 	表示按unicode(utf-8)匹配(主要针对多字节比如汉字)`
`s 	匹配换行符`

###### replace

```

"umu".replace(/u/,'@'); @mu
"umu".replace(/u/g,'@'); @m@

```

---

###### 案例

```

"张三:010-9999,李四:020-9999".match( /\d{3}-\d{4}/g ); ['010-9999','020-9999']

```

```

let type = 'pdf';
let reg = new RegExp(`\\.${type}$`, 'i'); /\.pdf$/i

```

`网址`

```

/https?:\/\/\w+\.\w+\.\w+/.test('http://www.cccd.com');
"https://www!hou!com".match(/^https?:\/\/\w+.\w+.\w+$/); ['https://www!hou!com']

```

`日期`

```

var date = "2022-12/23".match( /^\d{4}[-\/]\d{2}[-\/]\d{2}$/ );		['2022-12/23']
var date = "2022-12-23".match( /^\d{4}([-\/])\d{2}\1\d{2}$/ ); ['2022-12-23'] \1 取前面原子组匹配的结果

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

'张三:101-33,李四:202-44'.match(/[^:\-,\d]+/g); ['张三','李四']
'张三:101-33,李四:202-44'.match(/\p{sc=Han}+/gu); ['张三','李四']

```

`替换`

```

"2022-22-22".replace(/[-/]/g, '/'); 2022/22/22
"2022-22-22".replace(/[-/]/g, ()=> '/'); 2022/22/22
"/h123h/".replace(/\d+/g, (f)=> '-'+ f +'-'); '/h-123-h/'
main.innerHTML.replace( /(教育)/g, `<a href="https://www.baidu.com">$1</a>`);
main.innerHTML.replace( /教育/g, `<a href="https://www.baidu.com">$&</a>`);

```

`原子组替换操作`

```

/<(h[1-6])>([\s\S]_)<\/\1>/i; 组编号\1，从左往右依次;
"/abc-h123/".replace(/h([\s\S]_)/, '$1'); '/abc-123/'

`<h1>k</h1>`.replace( <h1>k</p>
/<(h[1-6])>([\s\S]\*)<\/\1>/i,
`<$1>$2</p>`
);

"(010)84498434 (020)84323098".replace( 010-84498434 020-84323098
/\((\d{3,4})\)(\d{7,8})/g,
'$1-$2'
);

```

`批量正则验证`

```

document.querySelector('input').addEventListener('keyup', e => {
const val = e.target.value;
const regs = [
/^[a-zA-Z0-9]{3,6}$/, // 数字或字母 3-6 位
/[A-Z]/, // 这 6 位内需包含一个大写字母
/[0-9]/, // 这 6 位内需包含一个数字
];
let state = regs.every( it => it.test(val) );
console.log(state ? '正确' : '错误');
});

```

`手机号`

```

/^(13[0-9]|14[5|7]|15\d|18[0|1|2|3|5|6|7|8|9])\d{8}$/

"hdr20.爱世界".match(/\p{L}/gu); ['h','d','r','爱','世','界'];
"hdr20.爱世界，加油！".match(/\p{P}/gu); ['.', '，'，'！']
"hdr20.爱世界，加油！".match(/\p{sc=Han}/gu); ['爱', '世', '界', '加', '油']
{sc}表示语言系统, Han 表示汉语,Arabic 表示阿拉伯语等;

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
/<(h[1-6])>(?<m>.\*?)<\/\1>/,
"<p>$<m></p>"
);

```

`?= 后断言匹配`

```

main.innerHTML = main.innerHTML.replace(
/教育(?=兴国)/g, 【教育】后是【兴国】才匹配
`<a href="https://baidu.com">$&</a>`
);

```

`?<= 前断言匹配`

```

let users = `	mm: 18742129832,
	kk: 19932315894`
users = users.replace(/(?<=\d{7})\d{4}/gi, f=>{
return '\*'.repeat(4); 【4 位数字】前是【7 位数字】才匹配
});

```

`?! 不是...为条件匹配`

```

let reg = /[a-z]+$/i;
let reg2 = /[a-z]+(?!\d+)$/i;
'aaa20220215kk'.match(reg2) kk

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

let str = `	https: '//oss.baidu.com/1.jpg',
	https: '//baidu.com/2.jpg'`

const reg = /https:\/\/([a-z]+)?(?<!www)\..+?/gi;
str.replace(reg, f=>{
return 'https://www.baidu.com'
})
```