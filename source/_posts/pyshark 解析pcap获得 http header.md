---
title: pyshark 解析 pcap 获取 http header
date: 2021-05-29 14:00:00
tags: 
      - python
categories: 
- [编程语言, python]
---

使用 pyshark 解析 pcap，输出 http header 信息的 csv 文件

<!--more-->

## 1. pyshark 简介

Python wrapper for tshark, allowing python packet parsing using wireshark dissectors.

There are quite a few python packet parsing modules, this one is different because it doesn't actually parse any packets, it simply uses tshark's (wireshark command-line utility) ability to export XMLs to use its parsing.

This package allows parsing from a capture file or a live capture, using all wireshark dissectors you have installed. Tested on windows/linux.

## 2. Reading from a capture file
```python
import pyshark
cap = pyshark.FileCapture('/tmp/mycapture.cap')
cap
>>> <FileCapture /tmp/mycapture.cap>
print cap[0]
Packet (Length: 698)
Layer ETH:
        Destination: aa:bb:cc:dd:ee:ff
        Source: 00:de:ad:be:ef:00
        Type: IP (0x0800)
Layer IP:
        Version: 4
        Header Length: 20 bytes
        Differentiated Services Field: 0x00 (DSCP 0x00: Default; ECN: 0x00: Not-ECT (Not ECN-Capable Transport))
        Total Length: 684
        Identification: 0x254f (9551)
        Flags: 0x00
        Fragment offset: 0
        Time to live: 1
        Protocol: UDP (17)
        Header checksum: 0xe148 [correct]
        Source: 192.168.0.1
        Destination: 192.168.0.2
```

## 3. Reading from a live interface
```python
capture = pyshark.LiveCapture(interface='eth0')
capture.sniff(timeout=50)
capture
>>> <LiveCapture (5 packets)>
capture[3]
<UDP/HTTP Packet>

for packet in capture.sniff_continuously(packet_count=5):
    print 'Just arrived:', packet
```

Capturing from a live interface can be done in two ways: either using the sniff() method to capture a given amount of packets (or for a given amount of time) and then read the packets from the capture object as a list, or use the sniff_continously() method as a generator and work on each packet as it arrives. Another alternative is defining a callback for each received packet:
```python
def print_callback(pkt):
    print 'Just arrived:', pkt
capture.apply_on_packets(print_callback, timeout=5)
```

The capture can also run on multiple interfaces if a list is provided, or all interfaces if no interface is provided. It can even be run through a remote interface using RemoteCapture.

## 4. 属性和方法
使用 LiveCapture 或者 FileCapture 方法建立 Capture 对象后，在捕获（capture）和数据包（packet）层面就会有多个方法和属性可用。PyShark的强大在于可以调用tshark内建的所有数据包解码器。

1. 获取数据包摘要（类似于tshark捕获的输出）
   ```shell
   >>> for pkt in cap:
   ...:     print pkt
   ...:
   2 0.512323 0.512323 fe80::f141:48a9:9a2c:73e5 ff02::c SSDP 208 M-SEARCH * HTTP/
   3 1.331469 0.819146 fe80::159a:5c9f:529c:f1eb ff02::c SSDP 208 M-SEARCH * HTTP/
   4 2.093188 0.761719 192.168.1.1 239.255.255.250 SSDP 395 NOTIFY * HTTP/1.  0x0000 (0)
   5 2.096287 0.003099 192.168.1.1 239.255.255.250 SSDP 332 NOTIFY * HTTP/1.  0x0000 (0)
   ```
2. 按层深入获取数据包属性
   > 可在 ipython 中 使用 dir(pkt) 或 pkt. 按tab键
   ```shell
   >>> pkt.   #(tab auto-complete)
   pkt.captured_length     pkt.highest_layer       pkt.ip                  pkt.pretty_print        pkt.transport_layer
   pkt.eth                 pkt.http                pkt.layers              pkt.sniff_time          pkt.udp
   pkt.frame_info          pkt.interface_captured  pkt.length              pkt.sniff_timestamp
   >>>
   >>> pkt[pkt.highest_layer].    #(tab auto-complete)
   pkt_app.                 pkt_app.get_field_value  pkt_app.raw_mode         pkt_app.request_version
   pkt_app.DATA_LAYER       pkt_app.get_raw_value    pkt_app.request
   pkt_app.chat             pkt_app.layer_name       pkt_app.request_method
   pkt_app.get_field        pkt_app.pretty_print     pkt_app.request_uri
   ```
3. capture 对象
   ```shell
   dir(cap)
   Out[3]:
   ['apply_on_packets',
    'close',
    'current_packet',
    'display_filter',
    'encryption',
    'input_filename',
    'next',
    'next_packet']
   ```

   此处真正强大的是apply_on_packets()和next()方法。next()方法使得 capture 对象可以通过for循环进行遍历。
   
   apply_on_packets() 方法是另一种遍历数据包的方式，它接受一个函数作为参数并将之作用于所有的数据包。

   ```shell
   >>> cap = pyshark.FileCapture('test.pcap', keep_packets=False)
   >>> def print_highest_layer(pkt)
   ...: print pkt.highest_layer
   >>> cap.apply_on_packets(print_highest_layer)
   HTTP
   HTTP
   HTTP
   HTTP
   HTTP
   ... (truncated)
   ```

   这个方法也可以用于打印之外的功能，例如将数据包添加入一个列表进行其它处理。下面的脚本会将所有的数据包加入到一个列表中并打印总数：
   ```shell
   import pyshark

   def get_capture_count():
       p = pyshark.FileCapture('test.cap.pcap', keep_packets=False)

       count = []
       def counter(*args):
           count.append(args[0])

       p.apply_on_packets(counter, timeout=100000)

       return len(count)

   print get_capture_count()
   ```


## 5. FileCapture和LiveCapture模块
PyShark中进行数据包分析的两个典型方法是使用 FileCapture 和 LiveCapture 模块。
前者从一个存储的捕获文件中导入u数据包，后者将使用本机的网络接口进行嗅探。
使用这两个模块都会返回一个 capture 对象。之后的文章中会详细介绍。
我们首先来了解一下这两个模块如何使用。

两个模块提供相似的参数来控制 capture 对象中返回的数据包。下面的定义直接从模块的docstring中获取：
- interface: [仅用于LiveCapture] 进行嗅探的网络接口。如果没有给出，使用可用的第一个接口。
- bpf_filter: [仅用于LiveCapture] 在嗅探时使用的BPF(tcpdump)过滤条件。
- input_file: [仅用于FileCapture] 保存的捕获文件的路径（PCAP, PCAPNG格式）。
- keep_packets: 设定在调用next()函数之后是否保留之前读取的数据包。用于在读取较大的捕获时节省内存。
- display_filter: 设定在读取捕获时使用的display过滤条件（即Wireshark过滤器）。
- only_summaries: 仅产生数据包摘要，比正常读取速度快的多，但包含信息很少。
- decryption_key: 可选的用于加密解密捕获的流量的密钥。
- encryption_type: 捕获的数据流量所使用的加密标准（必须是WEP，WPA-PWD或WPA-PWK中之一，默认是WPA-PWK）。

### 5.1 only_summaries选项
使用 only_summaries 后，返回的 capture 对象中的数据包将只包含摘要信息，类似于tshark的默认输出：
```shell
>>> cap = pyshark.FileCapture('test.pcap', only_summaries=True)
>>> print cap[0]
2 0.512323 0.512323 fe80::f141:48a9:9a2c:73e5 ff02::c SSDP 208 M-SEARCH * HTTP/

>>> dir(cap[0])
['delta', 'destination', 'info', 'ip id', 'length', 'no', 'protocol', 'source', 'stream', 'summary_line', 'time', 'window']
```

使用这个选项，读取捕获文件将变得很快，但每个数据包将只包含下面的这些属性。如果你想获取嗅探中的IP地址来构建会话列表，或者使用时间和包长度来计算带宽的统计数据，那么这些信息也足够了。

```shell
>>> pkt.     #(tab auto-complete)
pkt.delta         pkt.info          pkt.no            pkt.stream        pkt.window
pkt.destination   pkt.ip id         pkt.protocol      pkt.summary_line
pkt.host          pkt.length        pkt.source        pkt.time
```
- delta : 当前数据包和上一个数据包捕获时间的差值。
- destination : IP层的目标地址。
- info ：应用层数据的简短摘要（比如”HTTP GET /resource_folder/page.html”）。
- ip id : IP标识符字段。
- length : 以字节表示的数据包长度。
- no : 数据包在列表中的索引值。
- protocol : 数据包中识别出的最高层级的协议。（译注：HTTP数据包如果是JSON的数据，此处可能是JSON而非HTTP）
- source : IP层的源地址。
- stream : 索引值，标识出该数据包属于哪一个TCP流（仅用于TCP数据包）。
- summary_line : 将所有的摘要属性输出在一个tab分隔的字符串中。
- time : 当前数据包到达时间与第一个数据包的差值。
- window : TCP的窗口大小（仅用于TCP数据包）。

### 5.2 keep_packets选项
PyShark只会在要对数据包进行处理的时候才会将其读入内存。在你处理数据包的过程中，PyShark会将每个数据包添加到 capture 对象中叫 _packet 的列表属性的末尾。当处理大量的数据包时，这个列表将占用大量的内存，因此PyShark提供了这个选项使得内存中一次仅保留一个数据包。如果 keep_packets 设置为False（默认为True），PyShark在读取新数据包时会将上一个从内存中清除。我发现这样能提升一点数据包遍历处理的速度，提升一点也是好的！

### 5.3 display_filter和bpf_filter
这些过滤器有助于使你的应用集中精力于你想要分析的内容上。类似于使用Wireshark或者tshark进行嗅探，BPF过滤器可以用于确定进入到返回的 capture 对象中的流量。
BPF过滤器的灵活性不如Wireshark的display过滤器，但是你仍可以创造性的使用这些有限的关键字和偏移过滤器。
如果需要对使用BPF过滤器更详细的说明，参考[Wireshark的官方文档](https://wiki.wireshark.org/CaptureFilters)。

下面是一个使用BPF过滤器嗅探目标HTTP流量的例子：
```shell
>>> cap = pyshark.LiveCapture(interface='en0', bpf_filter='ip and tcp port 80')
>>> cap.sniff(timeout=5)
>>> cap
   <LiveCapture (21 packets)>
>>> print cap[5].highest_layer
HTTP
```

在读取保存的捕获文件时，你可以通过设置 display_filter 选项，利用Wireshark强大的解析器来限制返回的数据包。
下面是没有使用过滤器的情况下，我的test.pcap文件中的前几个数据包：
```shell
>>> cap = pyshark.FileCapture('test.pcap')
>>> for pkt in cap:
...:    print pkt.highest_layer
...:
HTTP
HTTP
HTTP
TCP
HTTP
... (truncated)
```

使用了display过滤器来限制只显示DNS数据流量：
```shell
>>> cap = pyshark.FileCapture('test.pcap', display_filter="dns")
>>> for pkt in cap:
...:    print pkt.highest_layer
...:
DNS
DNS
DNS
DNS
DNS
... (truncated)
```

## 6. 动态的层的引用
使用上面提到的动态变化的层属性（比如transport_layer和highest_layer）让我们在分析数据包时更灵活。
如果你对每个数据包都试图访问pkt.dns.qry_resp属性，那么如果这个数据包不是DNS数据包就会返回AttributeError异常。传输层也有类似的问题，因为有TCP和UDP两种可能。我们可以使用动态引用的层属性来获取源地址和目的地址，然后使用try/except来处理既不是TCP也不是UDP数据包的情况。

```python
import pyshark

cap = pyshark.FileCapture('test.pcap')

def print_conversation_header(pkt):
    try:
        protocol =  pkt.transport_layer
        src_addr = pkt.ip.src
        src_port = pkt[pkt.transport_layer].srcport
        dst_addr = pkt.ip.dst
        dst_port = pkt[pkt.transport_layer].dstport
        print '%s  %s:%s --> %s:%s' % (protocol, src_addr, src_port, dst_addr, dst_port)
    except AttributeError as e:
        #ignore packets that aren't TCP/UDP or IPv4
        pass

cap.apply_on_packets(print_conversation_header, timeout=100)
```

该脚本会输出：
```shell
UDP  10.10.10.12:51554 --> 239.255.255.250:1900
UDP  10.10.10.12:51554 --> 239.255.255.250:1900
UDP  10.10.10.15:58803 --> 8.8.8.8:53
UDP  8.8.8.8:53 --> 10.10.10.15:58803
TCP  10.10.10.15:58632 --> 192.168.20.197:80
TCP  192.168.20.197:80 --> 10.10.10.15:58632
TCP  10.10.10.15:58632 --> 192.168.20.197:80
```

## 7. 解析 http header
```python
import pyshark
import pandas as pd

cap = pyshark.FileCapture('./data/SUEE1.pcap', display_filter='http')

# 构造 DataFrame
output_csv_field_names = ['host', 'request method', 'request uri', 'request version', 'request full uri', 'user agent', 'referer']
output_df = pd.DataFrame(columns=output_csv_field_names)

# for 循环能遍历 pcap 中的 package
# 动态解析中有些数据包可能没有某个属性，会报 AttributeError
"""
for pkt in cap:
    try:
        print(pkt.http.host)
    except AttributeError as e:
        print("Tag was not found")
"""

# 没有找到 pyshark 中关于属性方法的介绍，下面的属性和方法我是通过 dir 函数探测出来的
# 也许 wireshark 中会有文档说明，毕竟 pyshark 使用的也是 tshark

for pkt in cap:
    try:
        http_layer_pkt = pkt.http
        output_df = output_df.append(
            {
                'host': http_layer_pkt.host,
                'request method': http_layer_pkt.request_method,
                'request uri': http_layer_pkt.request_uri,
                'request version': http_layer_pkt.request_version,
                'request full uri': http_layer_pkt.request_full_uri,
                'user agent': http_layer_pkt.user_agent,
                'referer': http_layer_pkt.referer
            }, ignore_index=True
        )
    except AttributeError:
        continue
print(output_df.head())
print(output_df.shape)
output_df.to_csv("./data/pcap_http_header.csv")

"""
In [11]: dir(cap[0].http)
Out[11]: 
['', 'DATA_LAYER', '__class__', '__delattr__', '__dict__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattr__', '__getattribute__', '__getstate__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__le__', '__lt__', '__module__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__setstate__', '__sizeof__', '__str__', __subclasshook__', '__weakref__', '_all_fields', '_field_prefix', '_get_all_field_lines', '_get_all_fields_with_alternates', '_get_field_or_layer_repr', '_get_field_repr', '_layer_name', '_sanitize_field_name', '_ws_expert', '_ws_expert_group', '_ws_expert_message', '_ws_expert_severity', 'chat', 'field_names', 'get', 'get_field', 'get_field_by_showname', 'get_field_value', 'host', 'layer_name', 'pretty_print', 'raw_mode', 'referer', 'request', 'request_full_uri', 'request_line', 'request_method', 'request_number', 'request_uri', 'request_version', 'user_agent']
"""
"""
In [12]: cap[0].http.field_names
Out[12]: ['', '_ws_expert', 'chat', '_ws_expert_message', '_ws_expert_severity', '_ws_expert_group', 'request_method', 'request_uri', 'request_version', 'host', 'request_line', 'user_agent', 'referer', 'request_full_uri', 'request', 'request_number']"""
```

## 8. 声明
参考文章
- [PyShark入门(1)：简介](https://segmentfault.com/a/1190000006043576)
- [PyShark入门(2)：FileCapture和LiveCapture模块](http://zodiacg.net/2016/07/intro2pyshark-ep2/)
- [PyShark入门(3)：capture对象](http://zodiacg.net/2016/07/intro2pyshark-ep3/)
- [PyShark入门(4)：packet对象](http://zodiacg.net/2016/07/intro2pyshark-ep4/)
- [pyshark](http://kiminewt.github.io/pyshark/)