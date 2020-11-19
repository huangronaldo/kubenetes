### 公共配置参数
* --log-backtrace-at aceLocation：记录日志每到＂file ：行号"时打印 stack trace ，默认值为0
* --log-dir string：日志文件路径
* --log-flush－frequency duration：设置flush日志文件的时间间隔，默认值为5s
* --logtostderr：设置为 true 则表示将日志输出到 stderr， 不输出到日志文件
* --alsologtostderr：设置为 true 则表示将日志输出到文件同时输出到stderr
* --stderrthreshold serverity：将该threshold级别之上的日志输出到stderr，默认值为2
* --v Level：glog 日志级别
* --vmodule moduleSpec： glog 基于模块到详细日志级别，格式为pattern=N，以逗号分隔
* --version=[true]：设置为true，则将打印版本信息然后退出

### kube-apiserver 启动参数
* --admission-control string
  
  对发送给 API Server 的任何请求进行准入控制，配置为一个“准入控制器”的列表，多个准入控制器时以逗号分隅。多个准入控制器将按顺序对发送给 API Server 
的请求进行拦截和过滤，若某个准入控制器不允许该请求通过，则 API Server 拒绝此调用请求。可配置的准入控制器如下，默认值为 AlwaysAdmit：
  * AlwaysAdmit ：允许所有请求
  * AlwaysDeny：禁止所有请求，一般用于测试。
  * AlwaysPulllmages ：在启动容器之前总是去下载镜像，相当于在每个容器的配置项 imagePullPolicy=Always
  * DefaultStorageClass：为了实现共享存储的动态供应，为未指定 StorageClas 或PV 的PVC 尝试匹配默认的 StorageClass
  * DefaultTolerationSecond ：这个插件为那些没有设置 forgiveness tolerations 并具有 notready:NoExecute和unreachable:NoExecute 两种 taints的Pod 设置默认的“容忍”时间，为 5min
  * DenyEscalatingExec：拦截所有 exec attach 具有特权的 上的请求
  * DenyExecOnPrivileged：它会拦截所有想在 privileged container 上执行命令的请求。如果集群支持 privileged container ，又希望限制用户在这 privileged container 上执行命令，那么推荐使用该控制器
  * lmagePolicyWebhook：这个插件将允许后端的－个 Webhook 程序来完成admission controller 的功能
  * InitialResources：实验性功能，用于当 Pod 未设置资源请求和资源限制时，根据该镜像历史资源使用数据自动为其设置资源请求
  * LimitPodHardAntiAffinityTopology：用于 Pod 互斥性调度时，对 to pologyKey 的限制
  * LimitRanger ：用于配额管理，作用于 Pod Container 上，确保 Pod Container上的配额不会超标
  * NamespaceLifecycle：如果尝试在一个不存在的 namespace 中创建资源对象，则该创建请求将被拒绝.删除一个 namespace 时， 系统将会删除该 namespace的所有对象，包括 Pod Service等
  * PodPreset：Pod 启动时注入应用所需的设置
  * PodSecurityPolicy：在创建或修改 Pod 时决定是否根据 Pod security context 和可用的 PodSecurityPolicy对Pod 安全策略进行控制  
  * 
