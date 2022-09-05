**DDD（领域驱动设计模式）**包含的4个层：

1. Domain：定义应用程序的域和**业务逻辑**的地方
2. Infrastructure（基础设施）：此层包含独立于我们的应用程序而存在的所有内容：**外部库，数据库引擎**等
3. Application：域与界面层之间的通道。将**请求从接口层发送到域层**，由域层处理请求并返回
4. Interface：包含与其他系统交互的所有内容，如**Web服务**、RMI接口或Web应用程序及批处理前端

**interface(接收请求) -> Application(转发) -> Domain(服务处理) ->infrastructure(调用底层库)**

