## main.go
主入口文件。

调用 `orderer.common.server` 包中的 `Main()` 方法。

```go
func main() {
	conf := config.Load()
	initializeLoggingLevel(conf)
	initializeProfilingService(conf)
	grpcServer := initializeGrpcServer(conf)
	initializeLocalMsp(conf)
	signer := localmsp.NewSigner()
	manager := initializeMultiChainManager(conf, signer)
	server := NewServer(manager, signer)
	ab.RegisterAtomicBroadcastServer(grpcServer.Server(), server)
	logger.Info("Beginning to serve requests")
	grpcServer.Start()
}
```
