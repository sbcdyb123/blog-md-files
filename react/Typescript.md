```bash
npm install typescript -g
```



创建配置文件

```bash
tsc --init
```

监视ts文件

```bash
tsc -w
```





# 基础类型

布尔类型：`boolean`

数字类型：`number`

字符串类型：`string`

数组类型：`array`

对象类型:`Object`

```typescript
let arr=number[] = [1,2,3]//第一种
let arr=Array<number> = [1,2,3]//第二种
```

元组类型：`tuple`

```typescript
let arr:[number,string] = [1,'str']//每一个位置一个类型
```

枚举类型：`enum`

```typescript
enum Err {null=-1,undefined=-2,success=1}

let e:Err = Err.null

console.log(e)//-1  如果赋值了就是赋值的数值,如果未赋值,显示的就是下标
```

```typescript
enum Err {null,undefined,success}

let e:Err = Err.null

console.log(e)//0
```

任意类型：`any`

`null`和`undefined`   (`nerve`类型的子类型)

`viod`类型:表示方法没有返回任何类型

```typescript
function a():void{
	console.log('aaa')
}
```

`never`类型:代表从不会出现的值,这意味着声明`never`类型的变量只能被`never`类型所赋值

# 函数定义

```typescript
function logInfo(name:string,age:number,sex:string):string{
	return `我叫${name},是个${sex}人,今年${age}岁`
}
```

## 函数参数可选:

TS函数定义,调用函数时,参数是必传的,如果需要制定参数可不传,则需要指定参数可选.

```typescript
function logInfo(name:string,age?:number,sex?:string):string{
	return `我叫${name},是个${sex}人,今年${age}岁`
}
```

在参数后加问号.且可选参数必须配置到参数的最后面

## 默认参数

```typescript
function logInfo(name:string,age:number=18,sex?:string):string{
	return `我叫${name},是个${sex}人,今年${age}岁`
}
```

## 剩余参数

```
function add(...result:number[]):number{
	return result.reduce((a,b)=>a+b)
}
```

## 函数重载

```typescript
function getInfo(name: string): string

function getInfo(age: number): number

function getInfo(str: any): any {
  if (typeof str === 'string') {
    return `我叫${str}`
  } else {
    return `我今年${str}岁`
  }
}
```

限制传入参数的类型,但是允许几个类型.

# 类

```typescript
class Person{
	name:string//和es6不同,es6不需要写这么一行
	constructor(name:string){
		this.name=name
	}
	run():void{
		console.log(this.name)
	}
}
let p = new Person('张三')
p.run()//张三
```

## 继承

```typescript
class Person{
	name:string//和es6不同,es6不需要写这么一行
	constructor(name:string){
		this.name=name
	}
	run():void{
		console.log(this.name)
	}
}
class Man{
	constructor(name:string){
		super(name)
	}
}
let zs = new Man('张三')

```

## 类里面的修饰符

`public`:公有,在类里面,子类,类外面都可以访问

`protected`:保护类型,在类里面,子类里面可以访问,再累外部无法访问.

`private`:私有,在类里面可以访问,在子类里和外部都无法访问

>  **属性如果不加修饰符,默认就是公有**

## 类里面的静态方法和静态属性

```typescript
class Person{
	public name:string
	constructor(name:string){
		this.name=name
	}
	run():void{
		console.log(this.name)
	}
	static print():void{
	console.log('静态方法')
	}
}
```

静态方法没发调用类里的属性,只能使用静态属性

## 多态

父类定义一个方法不去实现,让继承他的子类去实现.每一个子类有不同的表现形式.

```typescript
class Human{
  public sex:string
  constructor(sex:string){
    this.sex=sex
  }
  like():void{
    console.log('人类的性取向')
  }
}
class Man extends Human{
  constructor(sex:string){
    super(sex)
  }
  like():void{
    console.log('我喜欢女人')
  }
}
class WoMen extends Human{
  constructor(sex:string){
    super(sex)
  }
  like():void{
    console.log('我喜欢男人')
  }
}
```

## 抽象类

`typescript`种的抽象类,它是提供其他类继承的基类,不能直接被实例化.

用`abstract`关键字定义抽象类和抽象方法,抽象类中的抽象方法不包含具体实现并且必须在派生类中实现.

`abstract`抽象方法只能放在抽象类里面

抽象类和抽象方法是用来定义标准的

```
abstract class Human{
  public sex:string
  constructor(sex:string){
    this.sex=sex
  }
  abstract like():void{
    console.log('人类的性取向')
  }
}
class Man extends Human{
  constructor(sex:string){
    super(sex)
  }
  like():void{
    console.log('我喜欢女人')
  }
}
class WoMen extends Human{
  constructor(sex:string){
    super(sex)
  }
  like():void{
    console.log('我喜欢男人')
  }
}
```

# 接口

在面向对象的编程中,接口是一种规范的定义,它定义了行为和动作的规范,在程序设计里面,接口起到一种规范的作用.

接口定义了某一批类所需要遵循的规范,接口不关心这些类的内部状态数据,也不关心这些类里方法的实现细节,它只规定这批类里必须提供哪些方法,提供这些方法的类就可以满足实际需要,typescript中的接口类似于java,同时还增加了更灵活的接口类型,包括属性/函数/可索引和类等.

### 属性类接口

```typescript
function printName(name:FullName) {
  return name.firstName+name.lastName
}

printName({
  firstName:'张',
  lastName:'三',
  age:18//报错 如果直接在实参中写了接口不包含的属性,就会报错
})
```

```typescript
interface FullName{
  firstName:string;
  lastName:string;
}

function printName(name:FullName) {
  return name.firstName+name.lastName
}
let obj = {
  firstName:'张',
  lastName:'三',
  age:18
}
printName(obj)//在外部定义的对象,只要对象中包含接口指定的即可.
```



### 函数类型接口

```typescript
interface encrypt{
  (key:string,value:string):string
}

const md5:encrypt = (key:string,value:string):string => key+value

const getInfo:encrypt = (key:string,value:string):string => key+value
```



### 可索引接口

主要是对数组,对象进行约束

```typescript
interface userArr{
  [index:number]:string
}
let manArr:userArr = ['11','123']
```

```typescript
interface userObject{
  [index:string]:string
}
let manArr:userObject = {name:'张三',sex:'男'}
```



### 类类型接口

```typescript
 interface Human{
   name:string
   eat(str:string):void
 }
 class Man implements Human{
   name:string
   constructor(name:string){
     this.name = name
   }
   eat(str:string):void{
     console.log(str)
   }
 }
```



### 接口扩展



```typescript
interface Animal {
  eat():void
}
interface  Human extends Animal{
  name: string
  work(): void
}
class Man implements Human {
  name: string
  constructor(name: string) {
    this.name = name
  }
  work(): void {
    console.log(this.name)
  }
  eat():void{
    console.log('吃饭');
  }
}
```

# 泛型

## 泛型函数

泛型就是解决类 接口 方法的复用性,以及对不特定数据类型的支持

```typescript
function printInfo<T>(value:T):T{
	return value
}
printInfo<number>(123)//正确的
printInfo<number>('111')//错误的
```

T表示泛型:具体什么类型是调用这个方法的时候决定的

## 泛型类

```typescript
class MinClass<T>{
  public list:T[] = []
  constructor(list:T[]){
    this.list = list
  }
  add(value:T):void{
    this.list.push(value)
  }
  min():T{
    let minNumber = this.list[0]
    for(let i=0;i<this.list.length;i++){
      if(minNumber>this.list[i]){
        minNumber = this.list[i]
      }
    }
    return minNumber
  }
}
```

## 泛型接口

```typescript
//方法一
interface configFn {
  <T>(str: T): T
}
const printInfo: configFn = <T>(str: T) => str
printInfo<number>(123)//正确的
printInfo<number>('111')//错误的
```

```typescript
//方法二
interface configFn<T> {
  (str: T): T
}
const printInfo = <T>(str: T) => str
const printInfo1:configFn<number> = printInfo
printInfo<number>(123)//正确的
printInfo<number>('111')//错误的
```

## 把类作为参数来约束数据传入的类型的泛型类

```typescript
class User{
	userName:string | undefined
	passWord:string | undefined
}
class mysqlDB{
	add(user:User):boolean{
		console.log(user)
		return true
	}
}
let u = new User()
u.userName = '张三'
u.passWord = '123456'
let DB = new mysqlDB()
DB.add(u)
```

上面的数据库类 ,只能接收user,局限性大.可以用泛型来制定多种类校验

```typescript
class User{
	userName:string | undefined
	passWord:string | undefined
}
class mysqlDB<T>{
	add(info:T):boolean{
		console.log(info)
		return true
	}
}
let u = new User()
u.userName = '张三'
u.passWord = '123456'
let DB = new mysqlDB<User>()
DB.add(u)
```

# 命名空间

```typescript
namespace A{
	export class User{
		userName:string | undefined
		passWord:string | undefined
	}
	export class mysqlDB<T>{
		add(info:T):boolean{
			console.log(info)
			return true
		}
	}
}

let u = new A.User()
u.userName = '张三'
u.passWord = '123456'
let DB = new A.mysqlDB<A.User>()
DB.add(u)
```

# 装饰器

装饰器是一种特殊类型的声明,它能够被附加到l类声明,方法,属性和参数上,它可以修改类的行为.通俗的讲装饰器就是一个方法,可以注入到类,方法,属性参数上来扩展类属性方法参数的功能.

## 类装饰器

装饰器修改

```typescript
function logClass(info:any):void{
	console.log(info)
	info.prototype.apiUrl = 'www.flfper.com'
}

@logClass
class HttpClient{
	constructor(){

	}
	getData(){

	}
}
let http:any = new HttpClient()
console.log(http.apiUrl)  
```



装饰器重写类

```typescript
function logClass(info:any){
	console.log(info)
	return class extends info{
		apiUrl:any= '-----111111'
		getData(){
			console.log(this.apiUrl)
		}
	}
}

@logClass
class HttpClient{
	public apiUrl:string |undefined
	constructor(apiUrl:string){
		this.apiUrl = apiUrl
	}
	getData(){
		console.log(this.apiUrl)
	}
}
```



## 属性装饰器

```typescript
function logPropety(str:string){
	return function(target:any,attr:string){
		console.log(target,attr)
		target[attr] = '装饰器修改属性'
	}
}
@logClass('给装饰器传参')
class HttpClient{
	@logPropety('修改的属性')
	public url:string| undefined
	getData(){

	}
}
let http:any = new HttpClient()
console.log(http.apiUrl)  
```



## 方法装饰器

```typescript

function get(str: string) {
	return function (target: any, methodName: string, desc: any) {
		console.log(target);
		console.log(methodName);
		console.log(desc.value);
		const oldMethod = desc.value
		desc.value = function (...reset: any[]) {
			console.log(reset, '我是新方法');
			oldMethod.apply(this, reset)
		}
	}
}

class HttpClient {
	public url: string | undefined
	@get('装饰器传参')
	getData(...reset: any[]) {
		console.log(reset,'我是老方法');
	}
}
let http: any = new HttpClient()
http.getData(1,2,3)
```



## 参数装饰器

```typescript
function logParams(str: string) {
	return function (target: any, methodName: string, paramsIndex: any) {
		console.log(target);
		console.log(methodName);
		console.log(paramsIndex);

	}
}

class HttpClient {
	public url: string | undefined
	getData(@logParams('111') id:any) {
		console.log('老方法',id);
	}
}

let http = new HttpClient()
http.getData(1)
```



## 装饰器的写法

### 普通装饰器(无法传承)

```typescript
function logClass(info:any):void{
	console.log(info)
	info.prototype.apiUrl = 'www.flfper.com'
}

@logClass
class HttpClient{
	constructor(){

	}
	getData(){

	}
}
let http:any = new HttpClient()
console.log(http.apiUrl)  
```



### 装饰器工厂

```typescript
function logClass(info:string){
	return function(target:any){
		console.log(info)
		console.log(target)
		target.prototype.apiUrl = info
	}
}  
 
@logClass('给装饰器传参')
class HttpClient{
	constructor(){

	}
	getData(){

	}
}
let http:any = new HttpClient()
console.log(http.apiUrl)  
```



> 装饰器是过去几年中JS最大的成就之一,已经是ES7的标准特性之一

## 装饰器的执行顺序

属性>方法>方法参数>类

如果有多个同样的装饰器它会限制性后面的