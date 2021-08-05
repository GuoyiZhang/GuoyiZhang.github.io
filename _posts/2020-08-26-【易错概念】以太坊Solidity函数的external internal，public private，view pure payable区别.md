# 【易错概念】以太坊Solidity函数的external/internal，public/private，view/pure/payable区别


![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91cGxvYWQtaW1hZ2VzLmppYW5zaHUuaW8vdXBsb2FkX2ltYWdlcy8xMTkwNTc0LWIzN2JiM2NkNjYyMGJmOWEucG5nP2ltYWdlTW9ncjIvYXV0by1vcmllbnQvc3RyaXB8aW1hZ2VWaWV3Mi8yL3cvMTAwMC9mb3JtYXQvd2VicA?x-oss-process=image/format,png)

### 1. 函数类型：内部(internal)函数和外部(external)函数

函数类型是一种表示函数的类型。可以将一个函数赋值给另一个函数类型的变量，也可以将一个函数作为参数进行传递，还能在函数调用中返回函数类型变量。 函数类型有两类：- *内部（internal）*函数和 *外部（external）* 函数：

**内部函数只能在当前合约内被调用**（更具体来说，在当前代码块内，包括内部库函数和继承的函数中），因为它们不能在当前合约上下文的外部被执行。 调用一个内部函数是通过跳转到它的入口标签来实现的，就像在当前合约的内部调用一个函数。

外部函数由一个地址和一个函数签名组成，可以通过外部函数调用传递或者返回。

函数类型表示成如下的形式
function (<parameter types>) {internal|external} [pure|constant|view|payable] [returns (<return types>)]

与参数类型相反，返回类型不能为空 —— 如果函数类型不需要返回，则需要删除整个

returns (<return types>)
部分。

**函数类型默认是内部函数**，因此不需要声明

internal
关键字。 与此相反的是，**合约中的函数本身默认是 public 的**，只有当它被当做类型名称时，默认才是内部函数。

有两种方法可以访问当前合约中的函数：**一种是直接使用它的名字，

f
，另一种是使用

this.f
** 。 前者适用于内部函数，后者适用于外部函数。

如果当函数类型的变量还没有初始化时就调用它的话会引发一个异常。 如果在一个函数被

delete
之后调用它也会发生相同的情况。

如果外部函数类型在 Solidity 的上下文环境以外的地方使用，它们会被视为

function
类型。 该类型将函数地址紧跟其函数标识一起编码为一个

bytes24
类型。。

请注意，当前合约的 public 函数既可以被当作内部函数也可以被当作外部函数使用。 如果想将一个函数当作内部函数使用，就用

f
调用，如果想将其当作外部函数，使用

this.f
。

除此之外，public（或 external）函数也有一个特殊的成员变量称作

selector
，可以返回 [ABI 函数选择器](https://solidity-cn.readthedocs.io/zh/develop/abi-spec.html#abi-function-selector):
pragma solidity ^0.4.16; contract Selector { function f() public view returns (bytes4) { return this.f.selector; } }

如果使用内部函数类型的例子:

pragma solidity ^0.4.16; library ArrayUtils { // 内部函数可以在内部库函数中使用， // 因为它们会成为同一代码上下文的一部分 function map(uint[] memory self, function (uint) pure returns (uint) f) internal pure returns (uint[] memory r) { r = new uint[](self.length); for (uint i = 0; i < self.length; i++) { r[i] = f(self[i]); } } function reduce( uint[] memory self, function (uint, uint) pure returns (uint) f ) internal pure returns (uint r) { r = self[0]; for (uint i = 1; i < self.length; i++) { r = f(r, self[i]); } } function range(uint length) internal pure returns (uint[] memory r) { r = new uint[](length); for (uint i = 0; i < r.length; i++) { r[i] = i; } } } contract Pyramid { using ArrayUtils for /*; function pyramid(uint l) public pure returns (uint) { return ArrayUtils.range(l).map(square).reduce(sum); } function square(uint x) internal pure returns (uint) { return x /* x; } function sum(uint x, uint y) internal pure returns (uint) { return x + y; } }

另外一个使用外部函数类型的例子:

pragma solidity ^0.4.11; contract Oracle { struct Request { bytes data; function(bytes memory) external callback; } Request[] requests; event NewRequest(uint); function query(bytes data, function(bytes memory) external callback) public { requests.push(Request(data, callback)); NewRequest(requests.length - 1); } function reply(uint requestID, bytes response) public { // 这里要验证 reply 来自可信的源 requests[requestID].callback(response); } } contract OracleUser { Oracle constant oracle = Oracle(0x1234567); // 已知的合约 function buySomething() { oracle.query("USD", this.oracleResponse); } function oracleResponse(bytes response) public { require(msg.sender == address(oracle)); // 使用数据 } }
 
注解
Lambda 表达式或者内联函数的引入在计划内，但目前还没支持。

2. 函数可见性说明符：public，private，external，internal

* public
：内部、外部均可见（参考为存储/状态变量创建 [getter 函数](https://solidity-cn.readthedocs.io/zh/develop/contracts.html#getter-functions)）
* private
：仅在当前合约内可见
* external
：仅在外部可见（仅可修饰函数）——就是说，仅可用于消息调用（即使在合约内调用，也只能通过

this.func
的方式）
* internal
：仅在内部可见（也就是在当前 Solidity 源代码文件内均可见，不仅限于当前合约内，译者注）

函数可见性说明符格式：
function myFunction() <visibility specifier> returns (bool) { return true; }

3. 函数修改器

pure 修饰函数时：不允许修改或访问状态——但目前并不是强制的。
view 修饰函数时：不允许修改状态——但目前不是强制的。
payable 修饰函数时：允许从调用中接收 以太币Ether 。
constant 修饰状态变量时：不允许赋值（除初始化以外），不会占据 存储插槽storage slot 。
constant 修饰函数时：与 view 等价。
anonymous 修饰事件时：不把事件签名作为 topic 存储。
indexed 修饰事件时：将参数作为 topic 存储。

4. 参考

[Solidity中文帮助文档](https://solidity-cn.readthedocs.io/zh/develop/)

