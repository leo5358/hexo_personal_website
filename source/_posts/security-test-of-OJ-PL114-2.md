---
title: security test of OJ(PL114-2)
mathjax: true
date: 2026-03-01 23:01:18
categories: 
    - cybersecuriity
tags: [hack, security, test]
---

## 簡述背景
因為老師上課用提供的online judge的css看起來就是vibe coding做出來的，
因此我對其安全性起（純）了（粹）興（手）趣（癢），且在第一步確認了是Root權限後，
便開始了一系列的測試。

## 測試過的漏洞
老師似乎沒有把全部的standard output 放上來，僅有function return 的內容，
所以我隨便找了一題回傳的data type是 `string`
1. 權限

Code:
```python
import subprocess

def mergeAlternately(word1: str, word2: str) -> str:
    result = subprocess.run(["whoami"], check=True, capture_output=True, text=True)

    return "stdout: " + result.stdout + "stderr: " + result.stderr + "exit code: "
```
Output: 
```bash
stdout: root
```

一開始看到我有這麼大的權限，就開始對突破有了一點信心。

2. 目錄結構
將原本跑的 `whoami`變成
```python
result = subprocess.run(["ls", "-al"], check=True, capture_output=True, text=True)
```
Output:
```bash
total 60

drwxr-xr-x   1 root root 4096 Mar  1 13:25 .
drwxr-xr-x   1 root root 4096 Mar  1 13:25 ..
-rwxr-xr-x   1 root root    0 Mar  1 13:25 .dockerenv
lrwxrwxrwx   1 root root    7 Nov  7 17:40 bin -> usr/bin
drwxr-xr-x   2 root root 4096 Nov  7 17:40 boot
drwxr-xr-x   5 root root  340 Mar  1 13:25 dev
drwxr-xr-x   1 root root 4096 Mar  1 13:25 etc
drwxr-xr-x   2 root root 4096 Nov  7 17:40 home
lrwxrwxrwx   1 root root    7 Nov  7 17:40 lib -> usr/lib
lrwxrwxrwx   1 root root    9 Nov  7 17:40 lib64 -> usr/lib64
drwxr-xr-x   2 root root 4096 Nov 17 00:00 media
drwxr-xr-x   2 root root 4096 Nov 17 00:00 mnt
drwxr-xr-x   2 root root 4096 Nov 17 00:00 opt
dr-xr-xr-x 307 root root    0 Mar  1 13:25 proc
drwx------   1 root root 4096 Nov 18 05:58 root
drwxr-xr-x   3 root root 4096 Nov 17 00:00 run
lrwxrwxrwx   1 root root    8 Nov  7 17:40 sbin -> usr/sbin
drwxr-xr-x   2 root root 4096 Nov 17 00:00 srv
dr-xr-xr-x  13 root root    0 Mar  1 13:25 sys
drwxrwxrwt   2 root root 4096 Nov 17 00:00 tmp
drwxr-xr-x   1 root root 4096 Nov 17 00:00 usr
drwxr-xr-x   1 root root 4096 Nov 17 00:00 var
```

發現了 `dockerenv`，但很顯然的，它是個空的檔案。

3. 環境變數
將要跑的`env`指令填入在subprocess裡
```python
result = subprocess.run(["env""], check=True, capture_output=True, text=True)
```
Output: 
```bash
PATH=/usr/local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=a25ea27e6283
LANG=C.UTF-8
GPG_KEY=A035C8C19219BA821ECEA86B64E628F8D684696D
PYTHON_VERSION=3.10.19
PYTHON_SHA256=c8f4a596572201d81dd7df91f70e177e19a70f1d489968b54b5fbbf29a97c076
HOME=/root
```

看起來就只是一個很普通的跑python用的docker，並沒有什麼可以下手的environment variable。

4. 網路
執行 `curl 8.8.8.8`，將其填入subprocess中。
-> 發現containerˋ中沒有 `curl`

所以我嘗試用 Python `socket` 模組連向外網，發現 DNS 解析失敗（Temporary failure in name resolution）。
接著我直接讀取網卡資訊：
**Code:**
```python
with open('/proc/net/dev', 'r') as f:
    return f.read()
```
Output: 只有 lo: (loopback)，連 eth0 都沒有。這代表啟動容器時使用了 --network none，將這個環境做了很好的隔離。


5. 容器生命週期
即便是斷網的環境，root權限仍舊是root，依舊是很大的權限，且前面的測試都一再顯示了老師並非毫無意識，
所以我就想測試看看container的生命週期，

於是短時間內跑了兩次 
```python
import socket

def mergeAlternately(word1: str, word2: str) -> str:
    hostname = socket.gethostname()
    return f"Container ID (Hostname): {hostname}"
```
發現每次提交的 `hostname`都不同。

於是我看了一下 `uptime`
```python
import os, time
return str(time.time() - os.stat('/proc/1').st_mtime)
```

Output: 
0.58 seconds

可以確定是拋棄式的container了，也就是每次跑完當次提交，
就會自動銷毀的容器。

6. 核心版本與逃逸風險
最後檢查了核心版本，看是否有「容器逃逸」的漏洞（如 Dirty Pipe）

Code:
```python
import os
return os.popen("uname -r").read()
```

Output:
6.6.87.2-microsoft-standard-WSL2

版本 6.6.x 非常新。Dirty Pipe (CVE-2022-0847) 影響範圍在 5.8 ~ 5.16.11。因此，這台機器對已知的重大核心逃逸漏洞是免疫的。

## 總結
總之我目前測下來，最大的問題應該是權限，但因為我能力有限，除了`DoS`以外，不知道該怎麼利用，
查了一下知道還可以做 container escape ，但我很菜，不知道該怎麼用，就我能做的，目前算是安全。

