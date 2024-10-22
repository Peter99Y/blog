---
title: RegExp
---

#### 正则创建方式

- 字面量
  var reg = /\w+/;

- 构造函数
  用双引号创建正则时需要添加转义符；

```
    new RegExp("^\\w+$");                       /^\w+$/
    new RegExp(/^\w+$/);                        /^\w+$/

    var reg = new RegExp("\d");                 /d/ 转义了d仍然是字母d;
    reg.test(1)                                 false; 等价于 /d/.test(1);
    reg.test('d')                               true;

    new RegExp("\\d").test(1);                  true; 等价于 /\d/.test(1);
    new RegExp(/\d+\.\d+/).test(22.33);         true; 等价于 /\d+\.\d+/.test(22.33);
```

###### string.replace()

```
'china是美丽的国家'.replace('china', '中国');

'CHINA是美丽的国家'.replace(/china/gi, '中国');

"2022-22-22".replace(/[-/]/g, '/');                     2022/22/22

"2022-22-22".replace(/[-/]/g, ()=> '/');                2022/22/22

"/h123h/".replace(/\d+/g, (f)=> '-'+ f +'-');           '/h-123-h/'

main.innerHTML.replace( /教育/g, `<a href="https://www.baidu.com">$&</a>`);
```

###### string.match()

```
'hello'.match(/\d/g);                       null; 有返回数组;无返回null;
'中国美丽的国家'.match('中国');                ['中国']
```

---

#### 修正符

| 修饰符 |                                描述                                 |
| :----: | :-----------------------------------------------------------------: |
|   i    |                             忽略大小写                              |
|   g    |                  全局匹配,否则只匹配上第一个就停止                  |
|   m    |                              多行匹配                               |
| s (少) |                             匹配换行符                              |
| u (少) |          表示按 unicode(utf-8)匹配(主要针对多字节比如汉字)          |
| y (罕) | 匹配上就进行匹配下一个，否则返回 null 停止匹配(大文本时效率比 g 高) |

```
"hds".match(/[a-z]/g);                      ['h','d','s']

"123789".match(/\d{3}/g);                   ['123','789']

"2020 2021".match(/\d+/g);                  ['2020', '2021']

"2020 2021".match(/\d/g);                   [... '2','0','2','1']
```

```
"run\rrun\nrun".match(/^run/g)                ['run']

"run\rrun\nrun".match(/^run/gm)               ['run','run','run']
```

---

#### 元字符

| 元字符 |          描述          |
| :----: | :--------------------: |
|   \*   |        >= 0 次         |
|   ?    |        >= 1 次         |
|   m    | 要么 1 次 or 要么 0 次 |

```
/^a*$/.test('');                                true
"hds".match(/[a-z]+/);                          ['hds']
/^a?$/.test('a');                               true
```

| 元字符 |            描述             |
| :----: | :-------------------------: |
|  {n}   |        重复必须 n 次        |
|  {n,}  |      n 次 =< 重复必须       |
| {n,m}  | n 次 <= 必须重复 且 <= m 次 |

```
/\d{3}/.test("12a903n");                    true
/^\d{3}$/.test(1290);                       false

/^a{3,}$/.test('aaaa');		     true
/^a{3,16}$/.test('aaa');                    true
```

###### 贪婪模式

贪婪模式：匹配多的要求；
禁止贪婪：只匹配少的要求；

```
'hddd'.match(/hd*?/);					    ['h']

'hddd'.match(/hd+/);				        ['hddd']
'hddd'.match(/hd+?/);					    ['hd']

'hddd'.match(/hd??/);					    ['h']

'hddd'.match(/hd{2,100}?/);				    ['hdd']
'hddd'.match(/hd{2,}?/);			        ['hdd']
```

---

#### 边界符

| 边界符 |                                     描述                                      |
| :----: | :---------------------------------------------------------------------------: |
|   ^    |                    以谁开头; 原子表(中括号)里面的^表示取反                    |
|   $    |                                   以谁结尾                                    |
|   \b   |                                 匹配单词边界                                  |
|  [ ]   | 匹配中括号所有的其中一个字符 (其中特殊字符可不必用转义符，中括号内会自动解析特殊字符) |
|  [^]   |                           匹配中括号没有出现的字符                            |

```
/^[^a-zA-Z0-9-_,!]$/.test('!');             false 中括号^取反
/abc/.test('wabcw');                        true 匹配连着的 abc 字符
/^ab/.test('abcddd');                       true

/^\d$/.test('33');                          false
/^abc$/.test('abcabc');                     false
```

```
/^hello\b/.test('hello world')              true
```

```
/[abc]/.test('dad');                        true abc 其中一个字符出现在字符串中即可;
/^[abc]$/.test('dad');                      false

/^[a-zA-Z0-9]$/.test('M');                  true

"(sz).+".match(/[().+]/g);                  ["(", ")", ".", "+"]

/^[a-zA-Z0-9-_,/!]$/.test('-');             true
/^[a-zA-Z0-9\-_,/!]$/.test('-');            true
```

##### 案例-网址

```
/https?:\/\/\w+\.\w+\.\w+/.test('http://www.cccd.com');
"https://www!hou!com".match(/^https?:\/\/\w+.\w+.\w+$/);    ['https://www!hou!com']
```

#### 其他字符

---

| 字符 |                         描述                          |
| :--: | :---------------------------------------------------: |
|  \   | 转义符，将下一个字符标记为原义字符; 如 \+ \. \? \* \^ |
|  .   |                除换行符(\n)外任何字符                 |
|  \|  |  匹配多个选择项其中一个，范围从｜到开始，从｜到结束   |

```
var str = "这是\"转义符\"";                   这是"转义符"
/a|\+/.test('com+');                        true
```

```
/^\d+.\d+$/.test(22033);                    true
/^\d+\.\d+$/.test(22.33);                   true

let hd = `
    ddd
    mm
`;

hd.match(/.+/g);                            ['\tddd', '\tmm']
```

`用()将多个选择项(子表达式)括起来限制范围`

```
/t|m/.test('ama')                           true
't2 m3'.match(/^t|m\d$/g)                   ['t', 'm3']

/(^t$)|(^m$)/.test('tt')                    false
'tokkkam'.match(/^t(o)|(a)m$/g)             ['to', 'am'] 等价于 /^to|am$/
'tom tam toam'.match(/t(o|a)m/g)            ['tom', 'tam']
```

---

| 字符 |                   描述                   |
| :--: | :--------------------------------------: |
|  \d  |                等价[0-9]                 |
|  \D  |                  非数字                  |
|  \w  |             等价[a-zA-Z0-9_]             |
|  \W  |                  非字母                  |
|  \s  |    匹配任何空白符号; 等价[\t\r\n\v\f]    |
|  \S  | 匹配任何非空白字符; 等价于 [^\f\n\r\t\v] |

```
    /^\D$/.test('!');			              true
```

```
    /^\w$/.test('_');			              true
```

**空白符**：包括空格 \t 制表符 \r 回车符 \n 换行符 \v 垂直制表符 \f 换页符

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

---

#### 原子组

`() 原子组，标记一个子表达式的开始和结束位置; ()会把每个分组里的匹配的值被缓存起来；`

```
/^abc{3}$/.test('abccc')                        true

/^(abc){3}$/.test('abcabcabc')                  true; 整个原子组重复3次

'1aa2bb'.match(/[0-9][a-z]+/g);                 ['1aa', '2bb'] 等价于
'1aa2bb'.match(/([0-9])([a-z]+)/g);             ['1aa', '2bb']

/(^\d{18}$)|(^\d{17}(\d|X|x)$)/                 身份证号

/^(0?[1-9]|1[0-2])$/                            月份

main.innerHTML.replace( /(教育)/g, `<a href="https://www.baidu.com">$1</a>`);

```

`$n 可使用缓存组内容`

```
const main = document.querySelector("main");

main.innerHTML = main.innerHTML.replace(
    /<span>([\s\S]+?)<\/span>/gi,
    `<h4 style="color:red">$1</h4>`
);
```

`\n 取前面匹配的相同规则`

```
"2022-12/23".match( /^\d{4}[-/]\d{2}[-/]\d{2}$/ );          ['2022-12/23']
"2022-12-23".match( /^\d{4}([-/])\d{2}\1\d{2}$/ );          ['2022-12-23']
```

`(?: ) 不记录组`
圆括号会使相关匹配会被缓存，此时可用 ?: 不做缓存；

```
/https?:\/\/(\w+\.\w+\.(?:com|cn))/i;
```

`?<> 组别名`

```
"<h1>k</h1>".replace(
    /<(h[1-6])>(?<m>.\*?)<\/\1>/,
    "<p>$<m></p>"
);
```

`?= 后断言匹配`

```
main.innerHTML = main.innerHTML.replace(
    /教育(?=兴国)/g,
    `<a href="https://baidu.com">$&</a>`                    //【教育】后是【兴国】才匹配
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

###### 案例-匹配中文

```
'12hr你好'.match(/[\u4e00-\u9fa5]/g)                        ['你', '好']

"hdr20.爱世界，加油！".match(/\p{sc=Han}/gu);                 ['爱', '世', '界', '加', '油']

{sc}表示语言系统, Han 表示汉语,Arabic 表示阿拉伯语等;
```

```
let type = 'pdf';
let reg = new RegExp(`\\.${type}$`, 'i');                   /\.pdf$/i
```

###### 案例-用户名验证

```
<input type="text" name="username">

document.querySelector("[name='username']").addEventListener(
    "blur",
    function(){
        console.log(
            this.value.match(/^[a-zA-Z]\w{6,16}$/)
        )
    }
);
```

###### 案例-获取名字

```
'张三:101-33,李四:202-44'.match( /[^:\-,\d]+/g );       ['张三','李四']
'张三:101-33,李四:202-44'.match( /\p{sc=Han}+/gu );     ['张三','李四']
```

###### 案例-原子组替换操作

```
/<(h[1-6])>([\s\S]_)<\/\1>/i;                           组编号\1，从左往右依次;
"/abc-h123/".replace(/h([\s\S]_)/, '$1');               '/abc-123/'

`<h1>k</h1>`.replace( <h1>k</p>
/<(h[1-6])>([\s\S]\*)<\/\1>/i,
`<$1>$2</p>`
);

"(010)84498434 (020)84323098".replace(                  010-84498434 020-84323098
    /\((\d{3,4})\)(\d{7,8})/g,
    '$1-$2'
);
```

###### 案例-批量正则验证

```
document.querySelector('input').addEventListener(
    'keyup',
    e => {
        const val = e.target.value;
        const regs = [
            /^[a-zA-Z0-9]{3,6}$/,                   // 数字或字母 3-6 位
            /[A-Z]/,                                // 这 6 位内需包含一个大写字母
            /[0-9]/,                                // 这 6 位内需包含一个数字
        ];

        let state = regs.every( it => it.test(val) );
        console.log(state ? '正确' : '错误');
    }
);
```

###### 案例-手机号

```
/^(13[0-9]|14[5|7]|15\d|18[0|1|2|3|5|6|7|8|9])\d{8}$/
```

###### 案例-标点符号

```
"hdr20.爱世界，加油！".match(/\p{P}/gu); ['.', '，'，'！']
```
