一个好的 `addEvent()` 方法应达到那些要求：

1. 支持同一元素的同一事件句柄可以绑定多个监听函数；
2. 如果在同一元素的同一事件句柄上多次注册同一函数， 那么第一次注册后的所有注册都被忽略；
3. 函数体内的 `this` 指向的应当是正在处理事件的节点（如当前正在运行事件句柄的节点）；
4. 监听函数的执行顺序应当是按照绑定的顺序执行；
5. 在函数体内不用使用 `event = event || window.event `; 来标准化 Event 对象 ；

一、John Resig 所写的`addEvent()` 函数

```
function addEvent(obj, type, fn) {
    if (obj.attachEvent){
        obj['e'+type+fn] = fn;
        obj[type+fn] = function () {
            obj['e'+type+fn](window.event);
        };
        obj.attachEvent('on'+type, obj[type+fn]);
    } else {
        obj.addEventListener(type, fn, false);
    }
}
function removeEvent(obj, type, fn) {
    if (obj.detachEvent) {
        obj.detachEvent('on'+type, obj[type+fn]);
        obj[type+fn] = null;
    } else {
        obj.removeEventListener(type, fn, false);
    }
}
```

这个函数简单易懂， 的确非常令人惊讶， 看看是否满足上面5点要求：

对于第一点满足了；

对于第三点和第五点， 肯定也满足了；

对于第二点， 并没有满足， 因为`addEventListener()`会忽略重复注册， 而`attachEvent()`则不会忽略；

但是第四点， 并没有满足， 因为DOM标准没有确定调用一个对象的时间处理函数的顺序， 所以不应该想当然的认为它们以注册的顺序调用

二、Dean Edward 所写的`addEvent()` 函数

```
function addEvent(element, type, handler) {
    if (!handler.$$guid) handler.$$guid = addEvent.guid++;
    if (!element.events) element.events = {};
    var handlers = element.event[type];
    if (!handlers) {
        handlers = element.events[type] = {};
        if (element['on' + type]) {
            handlers[0] = element['on' + type];
        }
    }
    handlers[hander.$$guid] = handler;
    element['on' + type] = handleEvent;
}
addEvent.guid = 1;
function removeEvent(element, type, handler) {
    if (element.events && element.event[type]) {
        delete element.events[type][handler.$$guid];
    }
}
function handleEvent(event) {
    var returnValue = true;
    event = event || fixEvent(window.event);
    var handlers = this.events[event.type];
    for (var i in handlers) {
        this.$$handleEvent = handlers[i];
        if (this.$$handleEvent(event) === false) {
            returnValue = false;
        }
    }
    return returnValue;
}
function fixEvent(event) {
    event.preventDefault = fixEvent.preventDefault;
    event.stopPropagation = fixEvent.stopPropagation;
    return event;
}
fixEvent.preventDefault = function () {
    this.returnValue = false;
};
fixEvent.stopPropagation = function () {
    this.cancelBubble = true;
}
```

该函数使用了传统的实践绑定方法， 所以它可以在所有的浏览器中工作， 也不会造成内存泄漏。

但是对于最初的五点， 该函数只有满足了前四点， 只有最后一点没有满足， 因为在JavaScript中对`for/in `语句的执行顺序没有规定是按照赋值的顺序执行， 尽管大部分时刻是按照预期的顺序执行， 因此在不同的JavaScript版本或实现中这一语句的顺序有可能不同。

三、Dean Edward 的 `addEvent()` 函数的改进

```

Array.prototype.indexOf = function (obj) {
    var result = -1,
        length = this.length,
        i = length - 1;
    for ( ;i >= 0; i-- ) {
        if (this[i] == obj) {
            result = i;
            break;
        }
    }
    return result;
};
Array.prototype.contains = function (obj) {
  return (this.indexOf(obj) >= 0);
};
Array.prototype.append = function (obj, nodup) {
    if (! (nodup && this.contains(obj))) {
        this[this.length] = obj;
    }
};
Array.prototype.remove = function (obj) {
    var index = this.indexOf(obj);
    if (! index) return ;
    return this.splice(index, 1);
};
function addEvent(element, type, fun) {
    if (!element.events) element.events = {};
    var handlers = element.events[type];
    if (! handlers) {
        handlers = element.events[type] = [];
        if (element['on' + type]) {
            handlers[0] = element['on' + type];
        }
    }
    handlers.append(fun, true);
    element['on' + type] = handleEvent;
}
function removeEvent(element, type, fun) {
    if (element.events && element.events[type]) {
        element.events[type].remove(fun);
    }
}
function handleEvent(event) {
    var retuenValue = true, i = 0;
    event = event || fixEvent(window.event);
    var handlers = this.eventsp[event.type], length = handlers.length;
    for (; i < length; i++) {
        if (handlers[i].call(this, event) === false) {
            retuenValue = false;
        }
    }
    return returnValue;
}
function fixEvent(event) {
    event.preventDefault = fixEvent.preventDefault;
    event.stopPropagation = fixEvent.stopPropagation;
    return event;
}
fixEvent.preventDefault = function () {
    this.returnValue = false;
};
fixEvent.stopPropagation = function () {
    this.cancelBubble = true;
}
```

javascript 的事件绑定主要有四种方法

> onXXX为某一事件， fun为某一function， domld为某一DOM对象id
>
> 1. 在DOM中， 直接使用onXXX = “fun);” 进行绑定
> 2. 在JavaScript代码中用DOM对象 onXXX = fun 进行绑定
> 3. 用DOM对象 .attachEvent("onXXX", fun) 进行绑定
> 4. 用<scripr for="domid" event="onXXX">fun();</script>进行绑定

三、Dean Edward 的`addEvent()`函数的改进

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>event test</title>
</head>
<body onload="init()">
<!--绑定方式一： 在元素中， 通过onXXX(事件)设置绑定方法-->
<button id="btn1" onclick="display()">绑定方式一</button>

<!--绑定方式二： 在JavaScript代码中， 通过获得元素， 为元素的onXXX(事件)设置绑定方法-->
<button id="btn2">绑定方式二</button>

<!--绑定方式三： 通过for、event为元素绑定事件（IE4+）， for后面是元素id， event是具体事件-->
<button id="btn3">绑定方式三</button>

<!--绑定方式四：通过attachEvent为元素绑定事件（IE5+）， 第一个 参数是事件名， 第二个参数是具体事件-->
<button id="btn4">绑定方式四</button>

<script>
    function init() {
        // 为button2绑定事件
        document.getElementById("btn2").onclick = display;
        // 为button4绑定事件
        document.getElementById("btn4").attachEvent('onclick', display);
    }
    function display(event) {
        var targ;   // 触发事件的对象引用
        if (!event) {
            var event = window.event;       // 获得当前事件（IE）
        }
        if (event.target){ // IE 没有target
            targ = event.target;
        } else if (event.srcElement){       //适用于IE
            targ = event.srcElement;
        }
        // 对触发事件对象进行操作
        alert(targ.tagName+'-'+targ.id+'-'+event.offsetX);
        targ.disabled = "disabled";
    }
</script>
<script for="btn3" event="onclick">
    display();      // 为button绑定事件
</script>
</body>
</html>
```

附件一、 event事件： 

```
onabort: 图像的加载被终端
onblur: 元素失去焦点
onchange: 域的内容被改变
onclick: 当用户点击某个对象时调用的事件句柄
ondbclick: 当用户双击某个对象时调用的事件句柄
onerror: 在加载文档或图像时发生错误
onfocus: 元素获得焦点
onkeydown: 某个键盘按键被按下
onkeypress: 某个键盘按键被按下并松开
onkeyup: 某个键盘按键被松开
onload: 一张页面或一副图像完成加载
onmousedown: 鼠标按钮被按下
onmousemove: 鼠标被移动
onmouseout: 鼠标从某个元素被移开
onmouseover: 鼠标移到某个元素之上
onmouseup: 鼠标按键被松开
onreset: 重置按钮被点击
onresize: 窗口或框架被重新调整大小
onselect: 文本被选中
onsubmit: 确认按钮被点击
onunload: 用户退出页面
```

附件二、IE event属性

* cancelBubble: 如果事件句柄想阻止事件传播到包容对象， 必须把该属性设置为true;
* formElement: 对于mouseover和mouseout事件， fromElement引用移出鼠标的元素。
* keyCode: 对于keypress事件该属性声明了被敲击的键生成的 Unicode 字符码。对于 keydown 和 keyup 事件，它指定了被敲击的键的虚拟键盘码。虚拟键盘码可能和使用的键盘的布局相关。
* offsetX,offsetY ：发生事件的地点在事件源元素的坐标系统中的 x 坐标和 y 坐标。 
  returnValue： 如果设置了该属性，它的值比事件句柄的返回值优先级高。把这个属性设置为 fasle，可以取消发生事件的源元素的默认动作。 
* srcElement ：对于生成事件的 Window 对象、Document 对象或 Element 对象的引用。 
* toElement： 对于 mouseover 和 mouseout 事件，该属性引用移入鼠标的元素。 
* x,y： 事件发生的位置的 x 坐标和 y 坐标，它们相对于用CSS动态定位的最内层包容元素。