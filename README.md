# Yolo pedestrian detection

## 本地摄像头环境配置
1. 安装 ffmpeg
   ```
    sudo apt install ffmpeg libx264-dev
   ```

2. 修改配置文件

   打开配置文件 `/etc/ffserver.conf`

   然后修改为以下内容

   ```
   HTTPPort 8090                                 #绑定端口号
   
   HTTPBindAddress 0.0.0.0                       #绑定IP
   
   MaxHTTPConnections 2000                       #最大HTTP连接数
   
   MaxClients 1000                               #最大客户端连接数
   
   MaxBandwidth 1000                             #最大带宽
   
   CustomLog -                                   #日志文件，- 为直接打印
   
   <Feed feed1.ffm>                              #feed:每一个输入都建立一个feed
   
   File /tmp/feed1.ffm                           #feed缓存文件位置和名称
   
   FileMaxSize 10M                               #缓存文件最大值
   
   ACL allow 127.0.0.1                           #允许写入feed的IP
   
   ACL allow 192.168.0.0 192.168.255.255         #允许写入feed的IP范围
   
   </Feed>
   
   RTSPPort 8554                                 #rtsp端口号
   
   RTSPBindAddress 0.0.0.0                       #rtsp IP地址
   
   <Stream live1.h264>                           #
   
   Format rtp                                    #视频流的格式
   
   Feed feed1.ffm                                #视频流的种子来源
   
   VideoCodec libx264                            #
   
   VideoFrameRate 24                             #视频帧率
   
   VideoBitRate 128                              #视频比特率
   
   VideoBufferSize 100                           #视频缓冲区大小
   
   VideoSize 640x480                             #视频帧大小
   
   VideoQMin 1                                   #
   
   VideoQMax 31                                  #
   
   NoAudio                                       #无音频
   
   AVPresetVideo default                          
   
   AVPresetVideo baseline
   
   AVOptionVideo flags +global_header
   
   ACL allow localhost
   
   ACL allow 192.168.0.0 192.168.255.255
   
   </Stream>
   
   <Stream stat.html>
   
   Format status
   
   ACL allow localhost
   
   ACL allow 192.168.0.0 192.168.255.255
   
   </Stream>
   
   <Redirect index.html>
   
   URL http://www.ffmpeg.org/
   
   </Redirect>
   
   ```

3. 启动ffserver服务

   直接在终端运行

   ```
   ffserver
   ```

4. 转发rtsp流

   ```
   ffmpeg -f v4l2 -i /dev/video0  -s 640x480 -r 24 -vcodec libx264 -an http://127.0.0.1:8090/feed1.ffm
   ```

   注意：其中/dev/video0 表示USB摄像头对应的名称，要根据自己的设备情况对应更改

