# NeWebRTCP2PCommunication 点对点通讯--实现内网之间直接通讯的穿透原理与机制

## 一、概念
### 1.1 视频通话
> 视频通话：双方能进行语音与视频之间的传输，要求低延时，降噪，高保真；  
> 技术实现：WebRTC（包括微信、QQ、网易云、融云都是基于WebRTC进行展开的）。  

### 1.2 `WebRTC`
`WebRTC`：网页及时通讯(`Web Real-Time Communication`)，是一个支持网页浏览器进行实时语音对话或视频对话的API。它于2011年6月1日开源并在Google、Mozilla、Opera支持下被纳入万维网联盟W3C推荐标准。 

#### 1.2.1 `WebRTC`音频和视频引擎
![image](https://github.com/tianyalu/NeWebRTCP2PCommunication/raw/master/show/web_rtc_engine.png)  

#### 1.2.2 `WebRTC`协议
![image](https://github.com/tianyalu/NeWebRTCP2PCommunication/raw/master/show/web_rtc_protocol.png)  

#### 1.2.3 `WebRTC`交互流程
![image](https://github.com/tianyalu/NeWebRTCP2PCommunication/raw/master/show/web_rtc_communication.png)  

### 1.3 SDP交换流程
`SDP`是一种会话描述协议（`Session Description Protocol`），包含了一系列信息，也包括会话使用的媒体种类，双方IP和port，带宽，会话属性等。  
`SDP`交换流程：  
> 首先`Offer`方通过`new RTCPPeerConnction（config）`建立`PeerConnection`；  
> `Offer`方通过`createOffer`生成`SessionDescription`，设置`localDescription`，并通过信令服务器发送给`Answer`方；  
> `Answer`方收到`offer`，发现并没有与之对应的`peerConnection`，新建`peerConnection`，并设置`remoteDescription`；  
> `Answer`方通过`createAnswer`生成`sessionDescription`，设置`localDescription`，并通过信令服务器发送`answer`；  
> `Offer`方收到`answer`，设置`remoteDescription`；  
> `SDP`交换结束。  

### 1.4 ICE交换流程
`ICE`是一种用于实现NAT/防火墙穿越的协议：  
* `P2P`直接通讯  
* 使用`STUN`服务器实现突破`NAT`的`P2P`通讯  
* 使用`TURN`中继服务器实现突破防火墙的中继通讯  
实现流程：  
> 双方在`new RTCPeerConnnection(config)`建立连接后，当网络候选者可用时，会触发`icecandidate`事件；  
> 在`onIcecandidate`事件处理程序中将`candidate`通过信令服务器发送给对方；  
> 双方在接受到彼此的`candidate`后，通过`addIceCandidate`将对方的`candidate`加入到`PeerConnection`实例中；  
> 在连接建立前获取建立后调用`peerConnection.addStream()`；  
> 方法将本地视频/音频数据流加入到`connection`中；  
> 当对方接受到视频流时会触发`addStream`事件，在其处理程序中我们可以接受数据流将其显示。  

![image](https://github.com/tianyalu/NeWebRTCP2PCommunication/raw/master/show/ice_communication.png)   

### 1.5 视频通话流程
![image](https://github.com/tianyalu/NeWebRTCP2PCommunication/raw/master/show/video_communication_process.png)  

SDP总体架构图：  
![image](https://github.com/tianyalu/NeWebRTCP2PCommunication/raw/master/show/video_communication_structure.png)  
被叫端B得到SDP流程：  
![image](https://github.com/tianyalu/NeWebRTCP2PCommunication/raw/master/show/b_get_sdp.png)  

主叫端A得到SDP流程：  
![image](https://github.com/tianyalu/NeWebRTCP2PCommunication/raw/master/show/a_get_sdp.png)  

