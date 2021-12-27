#### 5.8 JSFuck混淆

`JSFuck`（或为了避讳脏话Fuck写作JSF*ck）是一种深奥的JavaScript 程式设计风格。以这种风格写成的代码中仅使用`[`、`]`、`(`、`)`、`!`和 + 六种字元。

|     值      |                        经JSFuck转化后                        |
| :---------: | :----------------------------------------------------------: |
|   `false`   |                            `![]`                             |
|   `true`    |                        `!![]`或`!+[]`                        |
|    `NaN`    |                     `+[![]]`或`+[][[]]`                      |
| `undefined` |                           `[][[]]`                           |
| `Infinity`  | `+(+!+[]+(!+[]+[])[!+[]+!+[]+!+[]]+[+!+[]]+[+[]]+[+[]]+[+[]])` |

-------

##### 1. 水一贴JSFuck

*示例为猿人学练习14题*

打上XHR断点，通过fiddler抓包分析发现：uc参数是变化的。

![](https://dosrui.oss-cn-guangzhou.aliyuncs.com/img/Snipaste_2021-12-21_22-46-15.png)

于是找堆栈发现uc的加密逻辑如下，其中有一段JSFuck代码

![](https://dosrui.oss-cn-guangzhou.aliyuncs.com/img/Snipaste_2021-12-21_22-55-56.png)

在控制台运行这段JS_Fuck加密代码

发现直接运行报错，须将最后一个括号中的内容删除，得到如下结果：

![](https://dosrui.oss-cn-guangzhou.aliyuncs.com/img/Snipaste_2021-12-21_23-11-47.png)

将代码扣下来：

```js
window = global
num = 1
window.num = num;
CryptoJS = require('crypto-js')
window.k = 'wdf2ff*TG@*(F4)*YH)g430HWR(*)' + 'wse';
window.t = Date.parse(new Date()) / 1000;
window.m = CryptoJS.enc.Utf8.parse(window.k);
window.a = function (word) {
    var srcs = CryptoJS.enc.Utf8.parse(word);
    var encrypted = CryptoJS.AES.encrypt(srcs, window.m, {
        mode: CryptoJS.mode.ECB,
        padding: CryptoJS.pad.Pkcs7
    });
    return encrypted.toString();
};
window.s = window.a(window.t + '|' + window.num);
window.a = window.s;
uc = window.a
console.log(uc)
```

这样一来就得到uc的值了，接下来用py来代入这段js代码获得我们所要的值。

------

##### 2. 吾爱破解JS_Fuck混淆解密

在控制台上运行该代码，得到结果如下

```js
(function anonymous() {
    return function _52pojie_MMXXI_(t) {
        let e = [];
        for (let f of t) {
            let t = f.codePointAt(0)
              , h = 0;
            t -= 0x1d4 << 0x1c8,
            "bkd0egfw002whccadqf6gm0q3mi2".match(/../g).forEach(f=>{
                f = parseInt(f, 0x24),
                t >= f && t < f + 0x1A && e.push(h % 7),
                h++
            }
            )
        }
        let f = [];
        for (; e.length >= 3; ) {
            let t = 0x31 * e.shift();
            if (t += 7 * e.shift(),
            (t += e.shift()) > 0xff)
                break;
            f.push(t)
        }
        return new Uint8Array(f);
        const flag1 = 'n0w_wh3r3_1s_th3_c1ph3rt3xt?'
    }
}
)
```

在它最上方打上debugger，运行后出现如下图的代码

![](https://dosrui.oss-cn-guangzhou.aliyuncs.com/img/Snipaste_2021-12-21_23-42-36.png)

一直f11跟进代码，会看到`btoa`和`atob`，卡住后按Shift+f11回跳，恢复后再继续f11，最后运行出一段很奇怪的代码

![](https://dosrui.oss-cn-guangzhou.aliyuncs.com/img/Snipaste_2021-12-21_23-46-59.png)

```
`𝗋𝐞𝚝𝐮𝚛𝘯 𝙛𝘂𝙣𝙘𝗍𝘪𝙤𝐧 _52𝐩𝐨𝐣𝘪𝘦_𝐌𝙈𝙓𝐗𝘐_(𝐭){𝚕𝘦𝙩 𝗲=[];𝙛𝙤𝗋(𝙡𝘦𝒕 𝐟 𝐨𝚏 𝘵){𝙡𝗲𝙩 𝙩=𝖿.𝙘𝘰𝒅𝒆𝐏𝚘𝘪𝙣𝘁𝘼𝙩(0),𝗁=0;𝗍-=0𝙭1𝖽4<<0𝒙1𝐜8,"𝐛𝘬𝘥0𝐞𝐠𝙛𝗐002𝚠𝐡𝐜𝗰𝙖𝐝𝚚𝘧6𝐠𝙢0𝐪3𝚖𝘪2".𝙢𝗮𝙩𝙘𝗁(/../𝗴).𝙛𝙤𝙧𝐄𝐚𝘤𝘩(𝒇=>{𝖿=𝘱𝒂𝙧𝐬𝐞𝗜𝙣𝒕(𝖿,0𝙭24),𝘁>=𝘧&&𝐭<𝚏+0𝘹1𝘼&&𝗲.𝙥𝙪𝗌𝙝(𝙝%7),𝗁++})}𝗹𝐞𝚝 𝘧=[];𝙛𝗼𝙧(;𝙚.𝗅𝙚𝘯𝒈𝐭𝐡>=3;){𝐥𝘦𝘵 𝒕=0𝗑31*𝘦.𝒔𝘩𝗂𝐟𝚝();𝗂𝚏(𝐭+=7*𝘦.𝘀𝒉𝘪𝗳𝚝(),(𝘵+=𝘦.𝘴𝘩𝗶𝐟𝘁())>0𝚡𝚏𝘧)𝗯𝙧𝘦𝗮𝗄;𝘧.𝙥𝙪𝘴𝗁(𝙩)}𝗋𝒆𝒕𝗎𝒓𝐧 𝗻𝐞𝘄 𝗨𝙞𝗇𝘁8𝘼𝒓𝗿𝘢𝒚(𝖿);𝒄𝐨𝗇𝒔𝚝 𝚏𝒍𝐚𝗀1='𝐧0𝗐_𝙬𝗁3𝚛3_1𝘴_𝒕𝐡3_𝙘1𝙥𝗵3𝘳𝒕3𝙭𝘵?'}`
```

copy下来先；继续f11调试；直到出现如下代码停下，当然继续也行，不过会运行到最后；

```js
(function anonymous() {
    return function _52pojie_MMXXI_(t) {
        let e = [];
        for (let f of t) {
            let t = f.codePointAt(0)
              , h = 0;
            t -= 0x1d4 << 0x1c8,
            "bkd0egfw002whccadqf6gm0q3mi2".match(/../g).forEach(f=>{
                f = parseInt(f, 0x24),
                t >= f && t < f + 0x1A && e.push(h % 7),
                h++
            }
            )
        }
        let f = [];
        for (; e.length >= 3; ) {
            let t = 0x31 * e.shift();
            if (t += 7 * e.shift(),
            (t += e.shift()) > 0xff)
                break;
            f.push(t)
        }
        return new Uint8Array(f);
        const flag1 = 'n0w_wh3r3_1s_th3_c1ph3rt3xt?'
    }
}
)
```

至此就已经把JSFuck的壳脱完了。可以看出这个函数是对t的操作，明显的是后面那段`let t = 0x31 * e.shift();`这是数组偏移操作；之前的壳是重复的btoa，atob操作。

```js
a = `𝗋𝐞𝚝𝐮𝚛𝘯 𝙛𝘂𝙣𝙘𝗍𝘪𝙤𝐧 _52𝐩𝐨𝐣𝘪𝘦_𝐌𝙈𝙓𝐗𝘐_(𝐭){𝚕𝘦𝙩 𝗲=[];𝙛𝙤𝗋(𝙡𝘦𝒕 𝐟 𝐨𝚏 𝘵){𝙡𝗲𝙩 𝙩=𝖿.𝙘𝘰𝒅𝒆𝐏𝚘𝘪𝙣𝘁𝘼𝙩(0),𝗁=0;𝗍-=0𝙭1𝖽4<<0𝒙1𝐜8,"𝐛𝘬𝘥0𝐞𝐠𝙛𝗐002𝚠𝐡𝐜𝗰𝙖𝐝𝚚𝘧6𝐠𝙢0𝐪3𝚖𝘪2".𝙢𝗮𝙩𝙘𝗁(/../𝗴).𝙛𝙤𝙧𝐄𝐚𝘤𝘩(𝒇=>{𝖿=𝘱𝒂𝙧𝐬𝐞𝗜𝙣𝒕(𝖿,0𝙭24),𝘁>=𝘧&&𝐭<𝚏+0𝘹1𝘼&&𝗲.𝙥𝙪𝗌𝙝(𝙝%7),𝗁++})}𝗹𝐞𝚝 𝘧=[];𝙛𝗼𝙧(;𝙚.𝗅𝙚𝘯𝒈𝐭𝐡>=3;){𝐥𝘦𝘵 𝒕=0𝗑31*𝘦.𝒔𝘩𝗂𝐟𝚝();𝗂𝚏(𝐭+=7*𝘦.𝘀𝒉𝘪𝗳𝚝(),(𝘵+=𝘦.𝘴𝘩𝗶𝐟𝘁())>0𝚡𝚏𝘧)𝗯𝙧𝘦𝗮𝗄;𝘧.𝙥𝙪𝘴𝗁(𝙩)}𝗋𝒆𝒕𝗎𝒓𝐧 𝗻𝐞𝘄 𝗨𝙞𝗇𝘁8𝘼𝒓𝗿𝘢𝒚(𝖿);𝒄𝐨𝗇𝒔𝚝 𝚏𝒍𝐚𝗀1='𝐧0𝗐_𝙬𝗁3𝚛3_1𝘴_𝒕𝐡3_𝙘1𝙥𝗵3𝘳𝒕3𝙭𝘵?'}`

_52pojie_MMXXI_(a)

// 输出为8位无符号整型数组
Uint8Array(66) [34, 240, 157, 149, 179, 226, 129, 177, 240, 157, 150, 137, 240, 157, 150, 138, 240, 157, 147, 152, 226, 130, 153, 225, 181, 129, 240, 157, 148, 171, 226, 133, 136, 225, 182, 156, 240, 157, 150, 148, 240, 157, 150, 137, 226, 133, 135, 34, 46, 110, 111, 114, 109, 97, 108, 105, 122, 101, 40, 39, 78, 70, 75, 68, 39, 41]

function Decodeunit8arr(uint8array){
    return new TextDecoder("utf-8").decode(uint8array);
}

Decodeunit8arr(_52pojie_MMXXI_(a))

// 输出
""𝕳ⁱ𝖉𝖊𝓘ₙᵁ𝔫ⅈᶜ𝖔𝖉ⅇ".normalize('NFKD')"

// 再将里边的值执行一遍
"𝕳ⁱ𝖉𝖊𝓘ₙᵁ𝔫ⅈᶜ𝖔𝖉ⅇ".normalize('NFKD')

// 结果
"HideInUnicode"
```

----

##### 3. 网洛者第四题JSFuck 加密

![](https://dosrui.oss-cn-guangzhou.aliyuncs.com/img/c06e33ecec96e2c8d73c9ab80577af73.png)

通过抓包软件发现加密参数是`_signature`

![](https://dosrui.oss-cn-guangzhou.aliyuncs.com/img/Snipaste_2021-12-23_00-23-29.png)

因为返回的是json数据，其实就是Json数据的映射到网站上。

![](https://dosrui.oss-cn-guangzhou.aliyuncs.com/img/cab22ac02d069babd9ea74af10caec9d.png)

继续跟进会发现一个JSFuck混淆

![](https://dosrui.oss-cn-guangzhou.aliyuncs.com/img/1377958b8c380fb139665e28278ec77b.png)

我们将这段代码复制出来，放到编辑器里面，这里以 PyCharm 为例，由于我们要选中匹配括号里的内容，所以我们可以设置一下 PyCharm 括号匹配高亮为红色，便于我们查找，依次点击 File - Settings - Editor - Color Scheme - General - Code - Matched brace，设置 Background 为显眼的颜色：

![](https://dosrui.oss-cn-guangzhou.aliyuncs.com/img/443e3f1973c668534cda9ca70fbe6fd1.png)

此时我们选中最后一个括号，往上找，就可以非常明显地看到与之匹配的另一个括号，如下图所示：

![](https://dosrui.oss-cn-guangzhou.aliyuncs.com/img/da963e526e8b1c1cbe2d611c71075fb0.png)

我们将括号里面的内容复制出来（可以包含括号，也可以不包含），放到浏览器控制台运行一下，就可以看到源码了

除了这种方法以外，我们还可以使用 Hook 的方式，直接捕获源码然后打印输出，注意到这段混淆代码最后没有 `()` 括号，那就是 eval 的方式执行的，我们编写 Hook eval 代码如下：

```js
eval_ = eval;
eval = function (a){
    debugger;
    return eval_()
}


// 另外提供一个 Hook Function 的代码
// Function.prototype.constructor_ = Function.prototype.constructor;
// Function.prototype.constructor = function (a) {
//     debugger;
//     return Function.prototype.constructor_(a);
// };
```

刷新网页，直接断下，此时 a 的值就是源码：

![](https://dosrui.oss-cn-guangzhou.aliyuncs.com/img/0e4544effcbad5ca040a4677d829cad6.png)

```js
(function () {
    let time_tmp = Date.now();
    let date = Date.parse(new Date());
    window = {};
    let click = window.document.onclick;
    let key_tmp;
    let iv_tmp;
    if (!click) {
        key_tmp = date * 1234;
    } else {
        key_tmp = date * 1244;
    }
    if (time_tmp - window.time < 1000) {
        iv_tmp = date * 4321;
    } else {
        iv_tmp = date * 4311;
    }
    const key = CryptoJS.enc.Utf8.parse(key_tmp);
    var iv = CryptoJS.enc.Utf8.parse(iv_tmp);
    (function tmp(date, key, iv) {
        function Encrypt(word) {
            let srcs = CryptoJS.enc.Utf8.parse(word);
            let encrypted = CryptoJS.AES.encrypt(srcs, key, {
                iv: iv,
                mode: CryptoJS.mode.CBC,
                padding: CryptoJS.pad.Pkcs7
            });
            return encrypted.ciphertext.toString().toUpperCase();
        }

        window.sign = Encrypt(date);
    })(date, key, iv);
})();
```

可以看到就是一个 AES 加密，这里主要注意有两个 if-else 语句，第一个判断是否存在 `window.document.onclick`，第二个是时间差的判断，我们可以在控制台去尝试取一下 `window.document.onclick` 和 `window.time`，看一下到底走的是 if 还是 else，在本地把这两个值也补全即可，实际上经过K哥测试 `window.document.onclick` 为 null，然后不管是走 if 还是 else 都是可以拿到结果的，所以对于本题来说，两个 window 对象都无所谓，直接去掉，`key_tmp` 和 `iv_tmp` 任意取值都可以。

自此本题分析完毕，本地改写之后，配合 Python 代码携带 _signature 挨个计算每一页的数据，最终提交成功
