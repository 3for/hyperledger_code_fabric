与configtx.yaml配置内容对应。
同时提供了Orderer的默认配置信息。
// TopLevel consists of the structs used by the configtxgen tool.
type TopLevel struct {
	Profiles      map[string]*Profile `yaml:"Profiles"`
	Organizations []*Organization     `yaml:"Organizations"`
	Application   *Application        `yaml:"Application"`
	Orderer       *Orderer            `yaml:"Orderer"`
}
var genesisDefaults = TopLevel{
	Orderer: &Orderer{
		OrdererType:  "solo",
		Addresses:    []string{"127.0.0.1:7050"},
		BatchTimeout: 2 * time.Second,
		BatchSize: BatchSize{
			MaxMessageCount:   10,
			AbsoluteMaxBytes:  10 * 1024 * 1024,
			PreferredMaxBytes: 512 * 1024,
		},
		Kafka: Kafka{
			Brokers: []string{"127.0.0.1:9092"},
		},
	},
}
