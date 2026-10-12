# 一起看直播

一个单文件网页：和朋友**同时看同一场直播**，右边还能**打字聊天**。直播本身天然同步，无需额外同步逻辑。

## 功能

- 📺 **直播播放**：支持 m3u8（HLS，推荐）和 flv。m3u8 在 Safari 用原生播放，其他浏览器走 hls.js；flv 走 flv.js
- 💬 **实时聊天**：基于 MQTT，填同一个房间号就能聊
- 🔗 **分享链接**：`?url=...&room=...` 参数预填，点"复制分享链接"一键生成发给朋友
- 📐 **布局**：视频占 4/5，聊天栏占 1/5；手机上自动上下排列
- 🔧 **开机自检**：页面底部绿色小字显示浏览器、解码、JS 库加载状态，播不了先看这里

## 文件

- `index.html` —— 页面本体（唯一需要经常碰的文件）
- `hls.min.js` / `flv.min.js` / `mqtt.min.js` —— 播放和聊天库，本地引用不依赖 CDN，传一次就行
## 部署（GitHub Pages）

1. 新建公开仓库（比如 `live`），把 4 个文件传到根目录
2. Settings → Pages → Source 选 `Deploy from a branch`，Branch 选 `main` / `(root)`，保存
3. 等 1–2 分钟，访问 `https://<用户名>.github.io/live/`

> 传完文件如果页面没变化：等构建跑完（Actions 里看），再按 `Ctrl+Shift+R` 硬刷新——多半是浏览器缓存。

## 使用

1. 把直播链接粘到顶部输入框（m3u8 结尾的优先，flv 也行），点**播放**
2. 右边聊天区填**房间号**（两人填同一个，复杂一点）+ 昵称，点**进入**
3. 点**复制分享链接**，把生成的 `?url=...&room=...` 发给朋友，对方打开点开始就行

以后换直播：页面里粘新链接 → 复制分享链接 → 发出去。**不用改代码、不用重新部署。**
## 聊天服务器

默认用公共服务器 `wss://broker.emqx.io:8084/mqtt`，开箱即用。

自建 mosquitto（公共连不上时）：`mosquitto.conf` 里加 `listener 8083` 和 `protocol websockets`，然后跑：

    docker run -d --name mosquitto --restart unless-stopped -p 1883:1883 -p 8083:8083 -v mosquitto.conf:/mosquitto/config/mosquitto.conf eclipse-mosquitto:2

再在页面"聊天服务器设置"里填 `ws://你的服务器IP:8083/mqtt`。

注意：填 `ws://` 地址时页面必须用 `http://` 打开（`https` 页面连不上 `ws://`，浏览器安全限制）。

## 注意事项

- 直播链接失效（播完下播）就黑屏，重新抓新链接换上就行
- 浏览器有广告拦截的话，把 `github.io` 加白名单，否则播放器库可能被拦
- iPhone 自带浏览器（Safari）用原生 HLS；其他浏览器走 hls.js
- 聊天经第三方 MQTT 服务器中转，别聊敏感信息
- 直播流地址不要外传（无鉴权，谁拿到谁能看）
