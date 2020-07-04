# yii2-websocket

## 配置文件
```php
"components" => [
	'websocket' => [
            'class' => '\yii\websocket\swoole\WebSocket',
            //'host' => '127.0.0.1',
            'host' => '0.0.0.0',
            'port' => 9501,
            'channels' => [
                //'push-signin'   => 'api\websocket\channels\SigninChannel', // 配置 channel 对应的执行类
                'push-message'  => 'api\websocket\channels\PushMessageChannel', // 配置 channel 对应的执行类

            ],
        ],
]
```

## 基本使用

每个 channel 的功能都需要定义一个单独的类。例如，如果你需要为指定客户端推送一条消息，则该类可能如下所示：

```php
namespace xxx\channels;

class PushMessageChannel extends BaseObject implements \yiiplus\websocket\ChannelInterface
{
	public function execute($fd, $data)
	{
		return [
			$fd, // 第一个参数返回客户端ID，多个以数组形式返回
			$data // 第二个参数返回需要返回给客户端的消息
		];
	}

	public function close($fd)
	{
		return;
	}
}
```

以下是从客户端发送消息的方法：

```php
Yii::$app->websocket->send(['channel' => 'push-message', 'message' => '用户 xxx 送了一台飞机！']);
```

执行任务的确切方式取决于使用的驱动程序。 大多数驱动程序可以使用控制台命令运行，组件需要在应用程序中注册。

此命令启动一个守护进程，该守护进程维护一个 WebSocket Server，根据客户端发来的数据，处理相关 channel 的任务：

```bash
yii websocket/start
```

