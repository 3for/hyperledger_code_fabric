## main.go
主入口文件。

```go
func main() {
/*1、config.Load()获取配置信息（configName为orderer），将配置信息中的相对路径转换为绝对路径（绝对路径=配置文件所在路径+配置内容相对路径）.【见fabric/orderer/localconfig config.SetEnvPrefix(Prefix)
优先级顺序为：
（1）环境变量$FABRIC_CFG_PATH
（2）路径获取：
1、当前路径./
2、$GOPATH/src/github.com/hyperledger/fabric/sampleconfig
3、/etc/hyperledger/fabric
源码获取的是sampleconfig目录下的orderer.yaml文件配置信息。】*/
	conf := config.Load()
//2、initializeLoggingLevel 可按模块设置log级别	
	initializeLoggingLevel(conf)
/*3、initializeProfilingService 若配置信息中的General.Profile.Enabled为true，则设置profiling service
orderer.yaml文件中有类似如下配置信息：
# Enable an HTTP service for Go "pprof" profiling as documented at:
    # https://golang.org/pkg/net/http/pprof
    Profile:
        Enabled: false
        Address: 0.0.0.0:6060
*/
	initializeProfilingService(conf)
/*4、initializeGrpcServer 监听General.ListenAddress:General.ListenPort端口，同时创建GRPC server（若General.TLS.Enabled为true，则使用grpcs://；为false则使用grpc://）
*/
	grpcServer := initializeGrpcServer(conf)
/*5、initializeLocalMsp 根据General.LocalMSPDir、General.BCCSP、General.LocalMSPID初始化local MSP
*/
	initializeLocalMsp(conf)
/*6、signer := localmsp.NewSigner() 获取signer
*/
	signer := localmsp.NewSigner()
/*7、initializeMultiChainManager 根据General.LedgerType和FileLedger.Location或FileLedger.Prefix创建orderer Ledger的存储路径和ledger factory；通过configtx(.yaml)配置文件（General.GenesisProfile），创建testchainid创世区块；获取multichain.manager
*/
	manager := initializeMultiChainManager(conf, signer)
/*8、NewServer 根据singer和manager，创建ab.AtomicBroadcastServer服务（基于broadcast目标和ledger Reader）
*/
	server := NewServer(manager, signer)
/*9、ab.RegisterAtomicBroadcastServer 注册所创建的ab.AtomicBroadcastServer服务
*/
	ab.RegisterAtomicBroadcastServer(grpcServer.Server(), server)
	logger.Info("Beginning to serve requests")
/*10、启动GRPC服务
*/
	grpcServer.Start()
}
```
