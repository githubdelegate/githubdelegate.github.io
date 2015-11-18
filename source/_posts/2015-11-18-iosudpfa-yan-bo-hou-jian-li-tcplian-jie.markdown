---
layout: post
title: "iOS UDP发广播,接收,TCP监听"
date: 2015-11-18 11:30:45 +0800
comments: true
categories: 
---

iOS 手机端先发送UDP广播，硬件设备端收到后，建立TCP连接，然后手机端读取硬件设备发送的信息。

---
1.UDP广播

```

- (void)viewDidAppear:(BOOL)animated
{
    [super viewDidAppear:animated];
    
     // 1.开始发送udp
    [self setupUdpClient];
    
    // 2.开启定时器，每个一秒发送一次
    self.sendTimer = [NSTimer scheduledTimerWithTimeInterval:1 target:self selector:@selector(p_sendUdp) userInfo:nil repeats:YES];
}

- (void)setupUdpClient
{
    NSError *error;
    _sendUdp = [[AsyncUdpSocket alloc] initWithDelegate:self];
    [_sendUdp enableBroadcast:YES error:&error]; // 开启广播
    if (![self checkError:error]) {
        return;
    }
    
    [_sendUdp bindToPort:9997 error:&error]; // 绑定端口
    if (![self checkError:error]) {
        return;
    }
}

- (BOOL)p_sendUdp
{
    NSString *sendStr = [NSString stringWithFormat:@"{\"ip\":\"\%@\"}",[Helper getIPAddress]]; // 发送本机IP地址
    NSData *sendData = [sendStr dataUsingEncoding:NSUTF8StringEncoding];
   return [_sendUdp sendData:sendData toHost:USCSoundBoxSendHost port:9998 withTimeout:-1 tag:0];
}


```
---

2.UDP接收

```
- (void)beginReceive
{
    NSError *error;
    _receiveUdp = [[AsyncUdpSocket alloc] initWithDelegate:self];
    [_receiveUdp enableBroadcast:YES error:&error];
    [_receiveUdp bindToPort:9997 error:&error];
    // 开始接受udp数据
    [_receiveUdp receiveWithTimeout:-1 tag:0];
}

// 当收到udp数据包时就会调用，但是你经常会收到不是自己想要的数据包，这些数据包可能来着其他主机，你需要忽略掉这些，这个代理方法返回bool类型，如果返回NO,当收到其他数据包，就会继续调用这个代理方法。
- (BOOL)onUdpSocket:(AsyncUdpSocket *)sock didReceiveData:(NSData *)data withTag:(long)tag fromHost:(NSString *)host port:(UInt16)port
{
    return NO;
}

```
 
2.TCP 监听连接请求

```
 // 开始监听 
 self.asyServer = [[AsyncSocket alloc]initWithDelegate:self];
 if([self.asyServer acceptOnPort:9997 error:&error]){
       NSLog(@"开始监听端口。。。%d",USCSoundBoxClientPort);
 }else{
     NSLog(@"errr=%@",error);
 }
    

// 建立连接，读取数据   
- (void)onSocket:(AsyncSocket *)sock didAcceptNewSocket:(AsyncSocket *)newSocket
{
    NSLog(@"%@",[NSString stringWithFormat:@"建立与%@的连接",newSocket.connectedHost]);
    self.acceptedSocket = newSocket;
    [newSocket readDataWithTimeout:-1 tag:0];
    newSocket.delegate = self;
}

```

### 注意
1.如果同时开启udp接收和tcp监听的话，tcp监听会失效。



