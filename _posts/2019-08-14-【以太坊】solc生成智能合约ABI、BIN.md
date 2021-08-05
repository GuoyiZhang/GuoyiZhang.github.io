# 【以太坊】solc生成智能合约ABI、BIN


安装solc
npm install -g solc

创建智能合约test.sol

pragma solidity 0.4.24; contract test { function main(uint a) constant returns (uint b) { uint result = a /* 8; return result; } }

编译test.sol，其中d:\\test.sol为编译文件，d:\\为生成目录，默认d:\\F_，需手动建立F_文件夹

solcjs D:\\test.sol --optimize --bin --abi --output-dir D:\\

编译成功后生成abi文件和bin文件


