<div id="table-of-contents">
<h2>Table of Contents</h2>
<div id="text-table-of-contents">
<ul>
<li><a href="#orgc939e83">1. HTTP Live Streaming (HLS)实践</a>
<ul>
<li><a href="#orgab48e70">1.1. HTTP Live Streaming的作用</a></li>
<li><a href="#org227a0fa">1.2. HLS的组成</a>
<ul>
<li><a href="#org1af4c48">1.2.1. M3U8文件</a></li>
<li><a href="#org809cec0">1.2.2. TS 文件</a></li>
</ul>
</li>
<li><a href="#org9dcb6e8">1.3. Nginx服务器的配置</a></li>
<li><a href="#org659737b">1.4. iOS HLS 播发</a></li>
</ul>
</li>
</ul>
</div>
</div>

<a id="orgc939e83"></a>

# HTTP Live Streaming (HLS)实践


<a id="orgab48e70"></a>

## HTTP Live Streaming的作用

下面是Apple 官方文档的介绍：
HTTP Live Streaming允许您通过HTTP从普通的Web服务器发送音频和视频，
以便在基于iOS的设备（包括iPhone，iPad，iPod touch和Apple TV）上以及台式机（Mac OS X）上播放。
HTTP Live Streaming支持直播和预录内容（视频点播）.
HTTP Live Streaming以不同的比特率支持多个备用流，客户端软件可以随着网络带宽的变化而智能地切换流。 
HTTP Live Streaming还通过HTTPS提供媒体加密和用户认证，从而允许发布商保护他们的工作。


<a id="org227a0fa"></a>

## HLS的组成

HLS基于HTTP协议实现，传输内容包括两部分，一是M3U8描述文件，二是TS媒体文件。


<a id="org1af4c48"></a>

### M3U8文件

先看一个例子：bosendorfer.mp4 用FFMPEG打包生成的bosendorfer.m3u8

    #EXTM3U
    #EXT-X-VERSION:3
    #EXT-X-TARGETDURATION:33
    #EXT-X-MEDIA-SEQUENCE:0
    #EXTINF:26.593233,
    bosendorfer0.ts
    #EXTINF:24.591233,
    bosendorfer1.ts
    #EXTINF:25.091733,
    bosendorfer2.ts
    #EXTINF:23.857167,
    bosendorfer3.ts
    #EXTINF:25.191833,
    bosendorfer4.ts
    #EXTINF:26.459767,
     ...
     ...
    #EXT-X-ENDLIST

文件中 "#EXT-X-TARGETDURATION:" 用于指定最大的媒体段时间长度（sencond）, #EXTINF中指定的时间长度必须 <= 这个最大值.


<a id="org809cec0"></a>

### TS 文件

 TS 文件为传输流文件，视频编码主要格式h264/mpeg4，音频为acc/MP3。家里面的数字有线电视DVB-C 就是使用它来传输音视频stream.
其中最重要的PAT，PMT表来标示节目数据包。有兴趣的同学，可前往：[wiki](https://zh.wikipedia.org/wiki/MPEG2-TS) 了解。

1.  TS 文件生成方法

    使用FFMPEG工具，它的[官方网站](https://ffmpeg.org) 。
    
    1.  安装方法：
    
        `MAC`
        
            brew install ffmpeg
        
        `ubuntu`
        
            sudo apt-get install ffmpeg
    
    2.  ffmpeg相关命令
    
               ffmpeg -i bosendorfer.mp4 -c:v libx264 -c:a aac -strict -2 -f hls -hls_time 33 -hls_list_size 0 -hls_wrap 0 bosendorfer.m3u8
            
            -i                输入视频文件
            -c:v              输出视频格式
            -c:a              输出音频格式
            -strict           按严密的格式输出 
            -f hls            输出视频为HTTP Live Stream（M3U8）
            -hls_time         设置每片的长度，默认为2，单位为秒
            -hls_list_size    设置播放列表保存的最多条目，设置为0会保存所有信息，默认为5
            -hls_wrap         设置多少片之后开始覆盖，如果设置为0则不会覆盖，默认值为0。
        
        上面的命令将bosendorfer.mp4文件生成 m3u8和TS文件。


<a id="org9dcb6e8"></a>

## Nginx服务器的配置

你的VPS安装完成nginx后， 打开nginx.conf 文件(/usr/local/nginx/conf/nginx.conf) 
在它 http {&#x2026;} 里面添加一个location

    location /vod {
                types {
                    application/dash+xml mpd;
                    application/vnd.apple.mpegurl m3u8;
                    video/mp2t ts;
                }
                alias /home/alex/alexvideos/; #你的视频文件存放路径
                expires -1; 
            }

当服务器收到HTTP请求 <http://120.25.207.78:8080/vod/bosendorfer.m3u8> 时，它就会返回/home/alex/alexvideos/ 下的，m3u8和TS文件. 
用 safari浏览器可正常播发视频。


<a id="org659737b"></a>

## iOS HLS 播发

用WKWebView 或 UIWebView来加载URL即可。

    import UIKit
    import WebKit
    
    class ViewController: UIViewController {
        override func viewDidLoad() {
            super.viewDidLoad()
            // Do any additional setup after loading the view, typically from a nib.
            let webView = WKWebView(frame: CGRect.init(x: 0, y: 64, width:320 , height: 200))
            let str =  "http://120.25.207.78:8080/vod/bosendorfer.m3u8"
            let request = URLRequest(url: URL.init(string:str)!, cachePolicy: URLRequest.CachePolicy.useProtocolCachePolicy, timeoutInterval: 10.0)
    
            webView.load(request)
            view.addSubview(webView)
        }
    
        override func didReceiveMemoryWarning() {
            super.didReceiveMemoryWarning()
            // Dispose of any resources that can be recreated.
        }
    }

