SOA框架在启动之后，会在不同的阶段调用不同的钩子。

```go
package main

type XMainHook struct {
}

//程序启动后调用
func (p *XMainHook) OnStartup() (err error) {
    return
}

//加载配置文件之前调用
func (p *XMainHook) OnBeforeLoadConfig() (err error) {
    return
}

//加载配置文件之后，服务正式运行之前调用
func (p *XMainHook) OnAfterLoadConfig() (err error) {
    return
}

//热加载配置之前调用
func (p *XMainHook) OnBeforeReloadConfig() (err error) {
    return
}

//热加载配置之后调用
func (p *XMainHook) OnAfterReloadConfig() (err error) {
    return
}

//程序退出时调用
func (p *XMainHook) OnShutdown() (err error) {
    return
}
```

框架总共有6个回调阶段：

- OnStartup， 在程序启动时调用。
- OnBeforeLoadConfig， 在加载配置文件之前调用。
- OnAfterLoadConfig， 在加载配置文件之后调用，这时候配置已经可用了。
- OnBeforeReloadConfig, 在热加载配置文件之前调用。
- OnAfterReloadConfig， 在热加载配置文件之后调用。
- OnShutdown， 在程序退出时调用。