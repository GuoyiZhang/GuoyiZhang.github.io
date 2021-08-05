# 【以太坊】Web3j通过合约地址监听transfer事件获取以太坊交易数据


We3j
web3j是一个轻量级的Java库，用于在Ethereum网络上集成客户端（节点）。

核心特性

通过Java类型的JSON-RPC与Ethereum客户端进行交互
支持所有的JSON-RPC方法类型
支持所有Geth和Parity方法，用于管理账户和签署交易
同步或异步的发送客户端请求
可从Solidity ABI文件自动生成Java只能合约功能包
业务需求: 分析区块链转账记录，获取交易信息进一步分析。

原创代码，用web3j接入以太坊全节点，通过合约地址监听transfer事件，获取交易记录。

注意：注意版本问题。不同版本event的构造参数列表是不一样的。
<dependency>     <groupId>org.web3j</groupId>     <artifactId>core</artifactId>     <version>3.4.0</version> </dependency>

**GitHub**: [**link**](https://github.com/TianShengBingFeiNiuRen/block-chain-data/blob/master/src/main/java/com/andon/blockchaindata/service/MonitorTransferService.java). 欢迎star

@Service public class MonitorTransferService { private static final Logger LOG = LoggerFactory.getLogger(MonitorTransferService.class); private Web3j web3j; private ThreadLocal<Set<BigInteger>> blockNumberSet = ThreadLocal.withInitial(HashSet::new); @Value("${ipcSocketPath}") private String ipcSocketPath; @PostConstruct public void init() { // web3j = Web3j.build(new HttpService()); //RPC方式 web3j = Web3j.build(new UnixIpcService(ipcSocketPath)); //IPC方式 } //*/* /* 监听合约(分步) /*/ public void contractFilterOfStep(MonitorTransferRequest monitorTransferRequest) { new Thread(() -> { try { BigInteger blockNumber = getBlockNumber(); LOG.info("BlockChainNumber={}", blockNumber); BigInteger startBlock = new BigInteger(monitorTransferRequest.getFirstBlock()); BigInteger step = new BigInteger("100"); assert blockNumber != null; int num = Integer.parseInt(String.valueOf(blockNumber.subtract(startBlock).divide(step))); for (int i = 0; i < num; i++) { // 监听合约(分步) contractFilterStep(monitorTransferRequest, startBlock, startBlock.add(step)); startBlock = startBlock.add(step); } // 监听合约 contractFilter(monitorTransferRequest, startBlock); } catch (NumberFormatException e) { LOG.error("监控合约异常终止,e={}", e.getMessage()); } }).start(); } //*/* /* 监听合约 /*/ private void contractFilter(MonitorTransferRequest monitorTransferRequest, BigInteger startBlock) { Event event = new Event("Transfer", Arrays.asList( new TypeReference<Address>() { }, new TypeReference<Address>() { }), Collections.singletonList(new TypeReference<Uint256>() { })); EthFilter filter = new EthFilter( DefaultBlockParameter.valueOf(startBlock), DefaultBlockParameterName.LATEST, monitorTransferRequest.getContractAddress()); filter.addSingleTopic(EventEncoder.encode(event)); // 监听并处理 contractExtract(filter); } //*/* /* 监听并处理 /*/ private void contractExtract(EthFilter filter) { Subscription subscription = web3j.ethLogObservable(filter).subscribe(log -> { BigInteger blockNumber = log.getBlockNumber(); // 提取转账记录 LOG.info("BlockNumber=", blockNumber); blockNumberSet.get().add(blockNumber); List<String> topics = log.getTopics(); String fromAddress = topics.get(1); String toAddress = topics.get(2); String value = log.getData(); String timestamp = ""; try { EthBlock ethBlock = web3j.ethGetBlockByNumber(DefaultBlockParameter.valueOf(log.getBlockNumber()), false).send(); timestamp = String.valueOf(ethBlock.getBlock().getTimestamp()); } catch (IOException e) { LOG.warn("Block timestamp get failure,block number is {}", log.getBlockNumber()); LOG.error("Block timestamp get failure,{}", e); } TransferRecord transferRecord = new TransferRecord(); transferRecord.setFromAddress("0x" + fromAddress.substring(26)); transferRecord.setToAddress("0x" + toAddress.substring(26)); transferRecord.setValue(new BigDecimal(new BigInteger(value.substring(2), 16)).divide(BigDecimal.valueOf(1000000000000000000.0), 18, BigDecimal.ROUND_HALF_EVEN)); transferRecord.setTimeStamp(timestamp); }); // subscription.unsubscribe(); } //*/* /* 监听合约(分步) /*/ private void contractFilterStep(MonitorTransferRequest monitorTransferRequest, BigInteger startBlock, BigInteger endBlock) { Event event = new Event("Transfer", Arrays.asList( new TypeReference<Address>() { }, new TypeReference<Address>() { }), Collections.singletonList(new TypeReference<Uint256>() { })); EthFilter filter = new EthFilter( DefaultBlockParameter.valueOf(startBlock), DefaultBlockParameter.valueOf(endBlock), monitorTransferRequest.getContractAddress()); filter.addSingleTopic(EventEncoder.encode(event)); // 监听并处理 contractExtract(filter); } //*/* /* 区块数量 /*/ private BigInteger getBlockNumber() { EthBlockNumber send; try { send = web3j.ethBlockNumber().send(); return send.getBlockNumber(); } catch (IOException e) { LOG.warn("请求区块链信息异常 >> 区块数量,{}", e); } return null; } }

 


