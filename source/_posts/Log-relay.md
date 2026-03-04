---
title: 'Log: relay'
mathjax: true
date: 2026-02-28 00:47:58
categories:
  - 網站建置
tags: [dev, personal_website]
---

## 簡述：
我因為想在個人網頁上放有關我GitHub需要用到第三方的API(Gemini推薦給我的)，
但在我使用的時候，遇到`status code 503`，因為知道API是放在vercel上的(https://github-readme-stats.vercel.app)，
因此當下認定了是call API 的次數到了上限，於是便有了這次架relay的過程。

## 作法：
### 1. 先到Github上fork出API原本的code
如標題：
![pic_fork](https://meee.com.tw/wHTXeE0.png)

### 2. 申請GitHub token
我因為要在網站上放上我的repos跟我的top languages(最常使用的語言)，因此token的scope只用了`user` 跟 `repo`
![pic_token](https://meee.com.tw/AUYRlQ7.png)

### 3. 到vercel上部署服務
其實沒什麼特別的，就只是把fork出來的repo直接部署上去，甚至連部署的指令都不用改，
需要注意的是要把剛才申請出來的token放進environment variable，名字是 `PAT_1`
![pic_deployment](https://meee.com.tw/WWgjvSo.png)


### 4.將原本的url替換成自己部署的
如標題，沒什麼麼好解釋的，畢竟處理request的變成自己的網站了。

### note:
我一開始原本有思考過要不要用GitHub student pack的digital ocean 免費200美元額度做這件事，
但後來覺得這樣做還要再買個domain有點麻煩，而且我現在沒什麼閑錢可以運用，因此就算了。
但未來有機會，我會想買一個自己的domain，將這個網站搬過去，也將我覺得酷的東西放上去，也可以在上面做自己的郵件收發。



