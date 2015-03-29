 学习Step1：js中有关Function的属性属性和方法
==============================================

## 1. 属性(Propertities)

###  * arguments

获取当前正在执行的Function对象的所有参数，是一个类似数组但不是数组的对象。
由*arguments[n]*来访问实际传递给函数的参数，并拥有数组长度属性length。

    function testArg(a, b) {
        var actCount = arguments.length,
             expCount = testArg.length,
             result;

        result = "Expected arguments' count is " + expCount + ";<br/>"; //Expected arguments' count is 2;
        result += "Actual arguments' count is " + actCount + ".<br/>";//Actual arguments' count is 1.
        result += "They are:<br/>";
        for (var i = 0; i < actCount; i++) {
            result += arguments[i] + ";<br/>";  //1
        }
        if (arguments instanceof Array) {
            result += "arguments is an Array instance."
        } else if (arguments instanceof Object) {
            result += "arguments is an Object instance." //arguments is an Object instance.
        }
        document.write(result);
    }
    testArg(1);



*注：js中typeof和instanceof的区别*


* typeof是一个一元运算符，放在变量之前，返回一个字符串，说明该变量的类型。返回结果：number, boolean, string, function, object, undefined。一般使用typeof判断一个变量是否存在。 `if(typeof a != 'undefined') {}`
* instance 用于判断一个变量是否为某个对象的一个实例。
` if(arguments instanceof Array) {}`

-----------------------------------------------------------------------

### * length

`FunctionName.length`  

获取函数定义的参数个数，区别于上面的arguments.length   

    function checkVarCount(a, b) {
        if (checkVarCount.length !== arguments.length) {   //checkVarCount(1);
            alert("The count doesn't match the function definition.");
        }
        alert("Successfully call the function");    // checkVarCount(1, 2);
    }

---------------------------------------------------------------------------

### * caller

`FunctionName.caller`  

获取调用当前函数的函数。caller函数只有当函数正在执行时才被定义。  

如果函数是从js程序顶层调用的，则caller返回null。如果在字符串上下文使用caller属性，则其结果和functionname.toString相同，将显示函数的反编译文本。

    function test() {
        if(test.caller == null) {
            document.write("test is called from the toppest level");
        }else {
            document.write("test is called from the function: ");
            document.writeln(test.caller.toString());
        }
    }
    test();  //test.caller == null
    function testOuter() {
        test();
    }
    testOuter();  //  testOuter

---------------------------------------------------------------------

### * callee

`[functionName.]arguments.callee`    返回正在被执行的function对象。

callee属性是arguments对象的一个属性，仅当该函数正在执行时使用。通常这个属性被用来递归调用匿名函数。  

    var fac = function(n) {
        if(n <= 1) {
            return 1;
        }else {
            return n * arguments.callee(n - 1);
        }
    }

--------------------------------------------------------------------

### * constructor

获取创建某个对象的函数。constructor属性就是用来构造对象实例的函数引用。

    var MyObj = function() {
        this.id = 1;
    }
    var o = new MyObj();
    if(o.constructor == MyObj) {
        document.write("o is MyObj");
    }

---------------------------------------------------------------------

### * prototype 

获取对象的原型。每一个构造函数都有一个prototype属性，指向另一个对象。该对象的所有属性和方法都会被构造函数的实例继承。这意味着我们可以把不变的属性和方法，直接定义在prototype对象上。

    function Man(name) {
        this.name = name;
    }
    Man.prototype.sex = 'M';
    Man.prototype.struggle = function(){
        alert("day day up!");
    }
        
我们也可以向已经定义好的对象（包括js原生对象）中追加方法和属性：

    var num = new Number(2);
    console.log(typeof (num.add));   //undefined
    Number.prototype.add = function(n) {
        return this + n;
    }
    console.log(num.add(1));   //3

-----------------------------------------------------------------------------

## 2. 方法

### * apply

`functionName.apply([thisObj],[argArray])`  

调用函数，并用指定对象thisObj替换函数的this值，同时用指定数组替换函数的参数。  
如果argArray为无效值，则会抛出*Object expected*错误；如果thisObj和argArray都没有提供，则用当前this作为thisObj。

    var callMe = function(arg1, arg2) {
        console.log("this value:" + this);
        for(i in arguments) {
            console.log(arguments[i]);    
        }
    }
    callMe(1, 2);  //this value: [Object window] 
                  // 1    2
    callMe.apply(3, [4, 5]);//this value: 3
                           //4   5
    callMe.apply();  //this value: [Object window]

    try {
        callMe.apply(3, 2);
    } catch(e) {
        console.log(e.message);//Function.prototype.apply: Arguments list has wrong type
    }

*注1：this 在对象或者方法中为window全局对象，当被是new实例化之后，就是实例化对象这些方法是function的方法，不是对象的方法，不可被实例化对象调用。*

**注2：一些非数组但类似数组的类型也可以被apply使用，只要他们可以被index枚举**

    var x = {
        "1": "111",
        "2": "222",
        "length": 2
    }
    var test = function(arg1, arg2) {
        console.log(arg1 + arg2);
    }
    test.apply(null, x);   //111222
-------------------------------------------------

### *call

`call([thisObj],[arg1,arg2...argn])`  

调用一个对象的方法，用另一个对象替换当前对象。如果没有提供thisObj参数，则global对象被用作thisObj。与apply唯一不同的是，apply第二个参数必须是Array，而call就是将所有参数列出，用逗号分隔。

------------------------------------------------------------------

### * bind

`function.bind(thisArg[,arg1,arg2...])`  

function和thisArg为必选项，返回一个与function相同的新函数，只不过this和参数不同。  
该绑定函数将bind方法中指定的参数做第一个参数，调用新函数时，指定的参数做第二个参数。以此类推。

    var old = function(a) {
        console.log(this.n);
        console.log(a);
    }
    var arg = {
        n: 10
    };    
    var newO = old.bind(arg, 1);
    old(9);
    newO(3);
   
---------------------------------------------------------------

### * toString

`objectName.toString([radix])`

返回对象的字符串形式。radix可选，为将数字值转换为字符串指定一个基数(几进制)，此值仅用于数字。

    (10).toString(2);     // 1010
    [1,'q',4,'c'].toString()  // "1,q,4,c"
----------------------------------

### * valueOf

`object.valueOf()`

返回对象的原生值。


------------------------------------------------------

*注：*
==

*参考1：[全面理解Javascript中Function对象的属性和方法](http://www.cnblogs.com/liontone/p/3970420.html)*  

*参考2：[ECMA探秘,比较操作符和apply](http://vastwu.github.io/javascript/2013/01/13/ECMA_apply.html)*
