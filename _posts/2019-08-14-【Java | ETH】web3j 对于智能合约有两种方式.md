# 【Java | ETH】web3j 对于智能合约有两种方式


### **1****、第一种：直接使用****RawTrasaction****进行创建**

// using a raw transaction RawTransaction rawTransaction = RawTransaction.createContractTransaction( <nonce>, <gasPrice>, <gasLimit>, <value>, "0x <compiled smart contract code>"); // send... // get contract address EthGetTransactionReceipt transactionReceipt = web3j.ethGetTransactionReceipt(transactionHash).send(); if (transactionReceipt.getTransactionReceipt.isPresent()) { String contractAddress = transactionReceipt.get().getContractAddress(); } else { // try again }

### **2、第二种：将合约代码转换成****Java Bean****。**

（1）首先我们需要一份写好的智能合约。
pragma solidity ^0.4.18; // Example taken from https://www.ethereum.org/greeter, also used in // https://github.com/ethereum/go-ethereum/wiki/Contract-Tutorial/#your-first-citizen-the-greeter contract mortal { //* Define variable owner of the type address/*/ address owner; //* this function is executed at initialization and sets the owner of the contract /*/ function mortal() { owner = msg.sender; } //* Function to recover the funds on the contract /*/ function kill() { if (msg.sender == owner) suicide(owner); } } contract greeter is mortal { //* define variable greeting of the type string /*/ string greeting; //* this runs when the contract is executed /*/ function greeter(string _greeting) public { greeting = _greeting; } //* main function /*/ function greet() constant returns (string) { return greeting; } }

注意：智能合约的代码是solidity写的，有关solidity语法和使用可以查询solidity官网

https://solidity.readthedocs.io/en/v0.4.21/

有可能需要FQ，请自行百度科学上网

（2）进入https://ethereum.github.io/browser-solidity//#optimize=false&version=soljson-v0.4.19+commit.c4cbbb05.js网站，此网站是在线智能合约编译网站，所以比较简便，不需要安装truffle等工具。

![https://images2018.cnblogs.com/blog/1171881/201803/1171881-20180316140507926-1397833168.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWFnZXMyMDE4LmNuYmxvZ3MuY29tL2Jsb2cvMTE3MTg4MS8yMDE4MDMvMTE3MTg4MS0yMDE4MDMxNjE0MDUwNzkyNi0xMzk3ODMzMTY4LnBuZw)

点击图示中details按钮，会出现智能合约的ABI和BIN

![https://images2018.cnblogs.com/blog/1171881/201803/1171881-20180316140656853-1765048701.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWFnZXMyMDE4LmNuYmxvZ3MuY29tL2Jsb2cvMTE3MTg4MS8yMDE4MDMvMTE3MTg4MS0yMDE4MDMxNjE0MDY1Njg1My0xNzY1MDQ4NzAxLnBuZw)

 将BYTECODE中的object复制下来保存成greeter.bin，点击ABI旁边的复制按钮将其保存成greeter.abi。

进入[https://github.com/web3j/web3j/releases/tag/v4.2.0](https://github.com/web3j/web3j/releases/tag/v3.3.1)网站，下载web3j-4.2.0.zip，并解压。

![https://images2018.cnblogs.com/blog/1171881/201803/1171881-20180316140924541-633893352.png](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9pbWFnZXMyMDE4LmNuYmxvZ3MuY29tL2Jsb2cvMTE3MTg4MS8yMDE4MDMvMTE3MTg4MS0yMDE4MDMxNjE0MDkyNDU0MS02MzM4OTMzNTIucG5n)
web3j solidity generate <编译的bin文件地址> <编译的abi文件地址> -o <输出目录> -p <java包名> eg：web3j solidity generate D:\F_\test_sol_test.bin D:\F_\test_sol_test.abi -o E:\test\src\main\java -p com.demo.test

-**o**
** **后接生成好的java文件放置的位置，

-p
 后接生成的java文件的包名

**或者 **进入目录bin下，将greeter.abi，复制,创建符合规则的json文件，格式如下：

![](https://img-blog.csdn.net/20180820161929791?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0tlaXRoMDAz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
web3j truffle generate C:\Users\Administrator\Desktop\web3j-3.2.0\bin\HumanStandardToken.json -o C:\Users\Administrator\Desktop\web3j-3.2.0\data -p com.ycwallet.servcie　　　　　　　　 web3j truffle generate　本地合约json地址 -o 是源代码要放的目录，-p是包名，生成出来Greeter.java
 
import org.web3j.abi.FunctionEncoder; import org.web3j.abi.TypeReference; import org.web3j.abi.datatypes.Function; import org.web3j.abi.datatypes.Type; import org.web3j.abi.datatypes.Utf8String; import org.web3j.crypto.Credentials; import org.web3j.protocol.Web3j; import org.web3j.protocol.core.RemoteCall; import org.web3j.protocol.core.methods.response.TransactionReceipt; import org.web3j.tx.Contract; import org.web3j.tx.TransactionManager; import java.math.BigInteger; import java.util.Arrays; import java.util.Collections; public class Greeter extends Contract { private static final String BINARY = "6060604052341561000f57600080fd5b6040516103203803806103208339810160405280805160008054600160a060020a03191633600160a060020a03161790559190910190506001818051610059929160200190610060565b50506100fb565b828054600181600116156101000203166002900490600052602060002090601f016020900481019282601f106100a157805160ff19168380011785556100ce565b828001600101855582156100ce579182015b828111156100ce5782518255916020019190600101906100b3565b506100da9291506100de565b5090565b6100f891905b808211156100da57600081556001016100e4565b90565b6102168061010a6000396000f30060606040526004361061004b5763ffffffff7c010000000000000000000000000000000000000000000000000000000060003504166341c0e1b58114610050578063cfae321714610065575b600080fd5b341561005b57600080fd5b6100636100ef565b005b341561007057600080fd5b610078610130565b60405160208082528190810183818151815260200191508051906020019080838360005b838110156100b457808201518382015260200161009c565b50505050905090810190601f1680156100e15780820380516001836020036101000a031916815260200191505b509250505060405180910390f35b6000543373ffffffffffffffffffffffffffffffffffffffff9081169116141561012e5760005473ffffffffffffffffffffffffffffffffffffffff16ff5b565b6101386101d8565b60018054600181600116156101000203166002900480601f0160208091040260200160405190810160405280929190818152602001828054600181600116156101000203166002900480156101ce5780601f106101a3576101008083540402835291602001916101ce565b820191906000526020600020905b8154815290600101906020018083116101b157829003601f168201915b5050505050905090565b602060405190810160405260008152905600a165627a7a723058209dd925f8a845985cc97b98b1d11e67cb72915d7316a7eeb4e28cec3f5f398c9f0029"; protected Greeter(String contractAddress, Web3j web3j, Credentials credentials, BigInteger gasPrice, BigInteger gasLimit) { super(BINARY, contractAddress, web3j, credentials, gasPrice, gasLimit); } protected Greeter(String contractAddress, Web3j web3j, TransactionManager transactionManager, BigInteger gasPrice, BigInteger gasLimit) { super(BINARY, contractAddress, web3j, transactionManager, gasPrice, gasLimit); } public RemoteCall<TransactionReceipt> kill() { Function function = new Function( "kill", Arrays.<Type>asList(), Collections.<TypeReference<?>>emptyList()); return executeRemoteCallTransaction(function); } public RemoteCall<String> greet() { Function function = new Function("greet", Arrays.<Type>asList(), Arrays.<TypeReference<?>>asList(new TypeReference<Utf8String>() {})); return executeRemoteCallSingleValueReturn(function, String.class); } public static RemoteCall<Greeter> deploy(Web3j web3j, Credentials credentials, BigInteger gasPrice, BigInteger gasLimit, String _greeting) { String encodedConstructor = FunctionEncoder.encodeConstructor(Arrays.<Type>asList(new org.web3j.abi.datatypes.Utf8String(_greeting))); return deployRemoteCall(Greeter.class, web3j, credentials, gasPrice, gasLimit, BINARY, encodedConstructor); } public static RemoteCall<Greeter> deploy(Web3j web3j, TransactionManager transactionManager, BigInteger gasPrice, BigInteger gasLimit, String _greeting) { String encodedConstructor = FunctionEncoder.encodeConstructor(Arrays.<Type>asList(new org.web3j.abi.datatypes.Utf8String(_greeting))); return deployRemoteCall(Greeter.class, web3j, transactionManager, gasPrice, gasLimit, BINARY, encodedConstructor); } public static Greeter load(String contractAddress, Web3j web3j, Credentials credentials, BigInteger gasPrice, BigInteger gasLimit) { return new Greeter(contractAddress, web3j, credentials, gasPrice, gasLimit); } public static Greeter load(String contractAddress, Web3j web3j, TransactionManager transactionManager, BigInteger gasPrice, BigInteger gasLimit) { return new Greeter(contractAddress, web3j, transactionManager, gasPrice, gasLimit); } }

部署智能合约

import org.web3j.crypto.Credentials; import org.web3j.protocol.Web3j; import org.web3j.protocol.http.HttpService; import org.web3j.tx.Contract; public class SmartContractTest1 { public static void main(String[] args) throws Exception { Web3j web3j = Web3j.build(new HttpService("https://kovan.infura.io/<your-token>")); String ownAddress = "0xD1c82c71cC567d63Fd53D5B91dcAC6156E5B96B3"; String toAddress = "0x6e27727bbb9f0140024a62822f013385f4194999"; Credentials credentials = Credentials.create("xxxxxxxxxxxxxxxxxxx"); //部署智能合约 Greeter greeter = Greeter.deploy(web3j,credentials,Contract.GAS_PRICE,Contract.GAS_LIMIT,"zx").send(); System.out.println(greeter.getContractAddress()); 　　　　 //调用智能合约 System.out.println(greeter.greet().send()); } }

## 注：远程连接的化 节点需要配置

geth --datadir data --networkid 31415926 --rpc --rpcaddr "0.0.0.0" --rpcport 8545 --rpccorsdomain "/*" --rpcapi="db,eth,net,web3,personal,web3" console

 


