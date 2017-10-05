# Promise 深入研究

## 1. Promise 初探

```javascript
console.dir(Promise);
```

![console.dir(promise)](D:\individual\参考手册\Javascript\book\note\console.dir(promise).png)

一看就知道， Promise是一个构造函数， 自己身上有all、reject、resolve这几个眼熟的方法， 原型上有then、catch等同样眼熟的方法。这么说用Promise new 出来的对象肯定有 then、catch 方法。

```javascript
    var p = new Promise(function (resolve, reject) {
        // 做一些异步操作
        setTimeout(function () {
            console.log("执行完成");
            resolve("随便什么数据");
        }, 2000);
    });
```

Promise 的构造函数接收一个参数， 是函数， 并且传入两个参数： resolve， reject， 分别表示异步操作执行成功后的回调函数和异步操作执行失败之后的回调函数。 其实这里用“成功“和”失败“来描述并不准确， 按照标准来讲， resolve是将Promise的状态置为fullfilled， reject是将Promise的状态置位rejected。 不过在我们开始的阶段可以先这么理解， 后面在研究概念。

在上面的代码中， 会在2秒之后输出“执行完成”。 **注意！这里只是 new  了一个对象， 并没有调用它， 我们传进去的函数就已经执行了，这是一个需要注意的细节。**所以我们用Promise的时候一般是包在一个函数中， 在需要的时候去执行这个函数，如:

```javascript
function runAsync(){
  var p = new Promise(function (resolve, rejecct) {
    // 做一些异步操作
    setTimeout(function () {
      console.log("执行完成!");
      resolve("随便什么数据!");
    }, 2000);
  });
  return p;
}
```

这个时候很自然会有两个疑问：

* 包装这么一个函数有毛用?
* resolve("随便什么数据"); 这句话是为毛?

在我们包装好的函数最后， 会return出Promise对象， 也就是说， 执行函数我们得到一个Promise对象。 还记得Promise 对象上有then、catch 方法吧？ 这就是他们的强大之处了， 看下面的代码:

```javascript
    function runAsync() {
        var p = new Promise(function (resolve, reject) {
            // 做一些异步操作
            setTimeout(function () {
                console.log("执行完成!");
                resolve("随便什么数据!");
            }, 2000);
        });
        return p;
    }

    runAsync().then(function (data) {
        console.log(data);
        // 后面可以用传递过来的数据做些其他操作
        // ...
    });

```

在 runAsync() 的返回上直接调用then 方法， then接收一个参数， 是函数， 并且会拿到runAsync中调用resolve时传递的参数， 运行这段代码 ， 之后2秒会输出“执行完成”, 紧接着输出“随便什么.

## 2. 关健方法解析

### 2.1  then是什么鬼？

这个时候应该有所领悟， 原来then里面的函数就跟我们平时的回调函数一个意思， 能够在runAsync这个异步任务执行完成之后被执行。 这就是Promise的作用了， **简单来讲， 就是能把原来的回调函数分离出来， 在异步执行完后， 用链式调用的方式执行回调函数。**

你可能会不屑一顾， 那么牛逼轰轰的Promise就这么点能耐？ 我把回调函数封装一下， 给runAsync传进去不也一样吗， 就酱紫：

```javascript
    function runAsync(callback) {
        setTimeout(function () {
            console.log("执行完成!");
            callback("随便什么数据！");
        }, 2000);
    }

    runAsync(function (data) {
        console.log(data);
    });
```

效果是一样的，还费劲用Promise干嘛。 那么问题来了， 有多层回调怎么办？ 如果callback也是一个异步操作， 而且执行完后也需要有相应的回调函数， 该怎么办？ 总不能再定义一个callback2， 然后给callback传递进去吧。 而**Promise的优势在于， 可以在then方法中继续写Promise对象并返回， 然后继续调用then来进行回调操作。**

### 2.2  链式操作的用法

所以从表面上来看， Promise 只是能够简化回调的写法， 而实质上， **Promise的精髓是“状态”, 用维护状态、传递状态的方式来使得回调函数能够技术调用， 它比传递callback函数要简单、灵活的多**。 所有使用Promise的正确场景是酱紫的：

```javascript
runAsync1()
        .then(function (data) {
            console.log(data);
            return runAsync2();
        })
        .then(function (data) {
            console.log(data);
            return runAsync3();
        })
        .then(function (data) {
            console.log(data);
        })
```

酱紫的话就可以按照顺序， 每隔两秒输出异步回调中的内容， 在runAsync2中传给resolve的数据， 能在接下来的then方法中拿到。 运行结果如下：

![执行结果动图](D:\individual\参考手册\Javascript\book\note\执行结果动图.gif)

猜猜 runAsync1、runAsync2、runAsync3这三个函数都是如何定义的？ 没错就是下面酱紫：

```javascript
function runAsync1() {                                          // 做一些异步操作
        var p = new Promise(function (resolve, reject) {
            // 做一些异步操作
            setTimeout(function () {
                console.log("异步任务1执行完成!");
                resolve("随便什么数据1!");
            }, 2000);
        });
        return p;
    }

    function runAsync2() {                                          // 做一些异步操作
        var p = new Promise(function (resolve, reject) {
            setTimeout(function () {
                console.log("异步操作2执行完成！");
                resolve("随便什么数据2");
            }, 2000);
        });
        return p;
    }

    function runAsync3() {                                          // 做一些异步操作
        var p = new Promise(function (resolve, reject) {
           setTimeout(function () {
               console.log("一步操作3执行完成!");
               resolve("随便什么数据3");
           }, 2000);
        });
        return p;
    }
```

在then方法中， 你可以直接return数据而不是Promise对象， 在后面的then中就可以接受到数据了， 比如我们把上面的代码修改成酱紫：

```javascript
    runAsync1()
        .then(function (data) {
            console.log(data);
            return runAsync2();
        })
        .then(function (data) {                 // 这里直接返回数据
            console.log(data);
            return "直接返回数据"
        })
        .then(function (data) {
            console.log(data);
        });
```

那么输出结果就变成了酱紫：

![直接 返回数据](D:\individual\参考手册\Javascript\book\note\直接 返回数据.gif)

### 2.3 reject 的用法

到这里， 你应该对“Promise是神马东东”有了最基本的了解， 那么我们接着来看看ES6的Promise还有哪些功能呢？ 目前讲到了resolve， 还没用reject呢， 它是做什么的呢？ 事实上， 我们前面的例子都是只有“执行成功”的回调， 还没有“失败”的情况， reject的作用就是把Promise的状态置位rejected， 这样我们在then中就能捕捉到， 然后执行“失败”情况的回调。 看看下面的代码。

```javascript
    function getNumber() {
        var p = new Promise(function (resolve, reject) {
            // 做一些异步操作
            setTimeout(function () {
                var num = Math.ceil(Math.random() * 10);                // 生成 1~10的随机数
                if (num <= 5) {
                    resolve(num);
                } else {
                    reject(num + "数字太大了");
                }
            }, 2000);
        });
        return p;
    }

    getNumber()
        .then(
            function (data) {
                console.log("resolved");
                console.log(data);
            },
            function (reason, data) {
                console.log("rejected");
                console.log(reason);
            }
        );

```

getNumber函数用来异步获取一个数字， 2秒之后执行完成， 如果数字小于5， 我们认为“成功”了， **调用resolve修改Promise的状态**。 否则， 我们认为是“失败”了， 调用reject并传递一个参数， 作出失败的原因。

运行getNumber并且在then中传了两个参数， then方法可以接受两个参数， **第一个对应resolve的回调， 第二个对应reject的回调**。 所以我们能够分别拿到他们传递过来的数据。 多次运行这段代码， 会随机得到两种结果：![resolve&rejecte](D:\individual\参考手册\Javascript\book\note\resolve&rejecte.png)

### 2.4 catch的用法

我们知道Promise对象除了then方法， 还有一个catch方法， 它是做什么用的呢？其实它和then的第二个参数一样， **用来指定reject的回调**， 用法如下：

```javascript
    getNumber()
        .then(function (data) {
            console.log("resolved");
            console.log(data);
        })
        .catch(function (reason) {
            console.log("rejected");
            console.log(reason);
        });
```

效果和写在then的第二个参数里面一样。 不过它还有另外一个作用： **在执行resolve的回调（也就是上面的then中的第一个参数）时， 如果抛出异常了（代码出错了）， 那么并不会报错卡死js， 而是会进到这个catch方法中。** 看下面的代码：

```javascript
    getNumber()
        .then(function (data) {
            console.log("resolved");
            console.log(data);
            console.log(somedata);              // 此处的somedata为定义
        })
        .catch(function (reason) {
            console.log("rejected");
            console.log(reason);
        });
```

也就是说进到catch方法里面去了， 而且把错误原因传到了reason参数中， 即便有错误的代码也不会报错了， 这与我们的try/catch语句有相同的功能

### 2.5 all 的用法

Promise 的all方法提供了并且执行异步操作的能力， 并且在所有异步操作执行完成后才执行回调， 看下面的例子：

```javascript
    function runAsync1() {                                          // 做一些异步操作
        var p = new Promise(function (resolve, reject) {
            // 做一些异步操作
            setTimeout(function () {
                console.log("异步任务1执行完成!");
                resolve("随便什么数据1!");
            }, 1000);
        });
        return p;
    }

    function runAsync2() {                                          // 做一些异步操作
        var p = new Promise(function (resolve, reject) {
            setTimeout(function () {
                console.log("异步操作2执行完成！");
                resolve("随便什么数据2");
            }, 2000);
        });
        return p;
    }

    function runAsync3() {                                          // 做一些异步操作
        var p = new Promise(function (resolve, reject) {
           setTimeout(function () {
               console.log("一步操作3执行完成!");
               resolve("随便什么数据3");
           }, 3000);
        });
        return p;
    }

    Promise
        .all([runAsync1(), runAsync2(), runAsync3()])
        .then(function (results) {
            console.log(results);
        });
```

用Promise.all 来执行， all接收一个数组参数， 里面的值最终都算返回Promise对象。 酱紫， 三个异步操作是并行执行的， 等到他们都执行完后才进到then里面。 那么， 三个异步操作的数据哪里去了呢？ 都在then里面呢， all会把所有异步操作的结果放进一个数组中传给then， 就是上面的results。 所以上面的代码输出结果为：

![ll](D:\individual\参考手册\Javascript\book\note\all.gif)

有了all， 你就可以执行多个异步操作， 并且在一个回调中处理所有的返回数据， 是不是很爽， **有一个场景是很适合用这个的， 一些游戏类的素材比较多的应用， 打开网页是， 预先加载需要用到的各种资源图片、 flash以及各种静态文件。 所有的都加载完成后， 我们再进行页面的初始化。**

### 2.6 race操作

all方法的效果实际上是[**谁跑的慢，以谁为准执行回调** ]， 那么相对的就有另一个方法[**谁跑得快， 以谁为准执行回调**]， 者就是race方法， 这个词本来就是赛跑的意思。 race的用法与all一样， 来看一下：

```javascript
    function runAsync1() {                                          // 做一些异步操作
        var p = new Promise(function (resolve, reject) {
            // 做一些异步操作
            setTimeout(function () {
                console.log("异步任务1执行完成!");
                resolve("随便什么数据1!");
            }, 1000);
        });
        return p;
    }

    function runAsync2() {                                          // 做一些异步操作
        var p = new Promise(function (resolve, reject) {
            setTimeout(function () {
                console.log("异步操作2执行完成！");
                resolve("随便什么数据2");
            }, 2000);
        });
        return p;
    }

    function runAsync3() {                                          // 做一些异步操作
        var p = new Promise(function (resolve, reject) {
            setTimeout(function () {
                console.log("一步操作3执行完成!");
                resolve("随便什么数据3");
            }, 3000);
        });
        return p;
    }

    Promise
        .race([runAsync1(), runAsync2(), runAsync3()])
        .then(function (results) {
            console.log(results);
        });

```

这三个的操作同样是并行执行的， 结果应该可以猜得到， 1秒之后runAsync执行完毕了， 是的then里面的就是执行了， 结果是酱紫的：

![race](D:\individual\参考手册\Javascript\book\note\race.gif)

在then里面的回调开始执行时， **runAsync2() 和runAsync3()并没有停止， 仍旧在执行。 于是再过1秒后， 输出了他们结束的标志。**

这个race有什么用呢？ 使用的场景很多， 比如**我们可以用race给某个异步请求设置超时时间， 并且在超时后执行相应的操作**， 代码如下：

```javascript
    // 请求某个图片资源
    function requestImg() {
        var p = new Promise(function (resolve, reject) {
            var img = new Image();
            img.onload = function () {
                resolve(img);
            };
            img.src = "http://202.118.16.50:8033/Java-logo.jpg11";
        });
        return p;
    }

    // 延时函数， 用于给请求计时
    function timeout() {
        var p = new Promise(function (resolve, reject) {
            setTimeout(function () {
                reject("图片请求超时");
            }, 1000);
        });
        return p;
    }

    Promise
        .race([requestImg(), timeout()])
        .then(function (results) {
            document.body.appendChild(results);
            console.log(results);
        })
        .catch(function (reason) {
            console.log(reason);
        });

```

requsetImg 函数会异步请求一张图片， 我们把地址写为 “xxx”， 所以肯定是无法请求的的。 timeout函数是一个延时5秒的异步操作。 我们把这两个返回Promise 对象的函数放进race， 于是他两就会赛跑， 如果1秒之内图片请求成功了， 那么边进入then方法， 执行正常的流程。 如果1秒图片还未成功返回， 那么timeout就跑赢了， 则进入catch， 报出“图片请求超时” 的信息

图片请求失败：

![race使用](D:\individual\参考手册\Javascript\book\note\race使用.png)

图片请求成功：

![race成功请求](D:\individual\参考手册\Javascript\book\note\race成功请求.png)

