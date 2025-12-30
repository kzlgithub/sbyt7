[![图片](https://img.youtube.com/vi/AmA37gOaqOs/0.jpg)](https://www.youtube.com/watch?v=AmA37gOaqOs)

# 🚀 【最隐蔽的节点】AnyTLS+Reality+SingBox节点搭建教程

<div align="center">
  <a href="https://www.youtube.com/@%E8%B5%9B%E5%8D%9A%E4%BA%91%E6%A2%AF">
    <strong>🚀 点击访问赛博云梯 YouTube 主页</strong>
  </a>
</div>

### Singbox一键安装脚本：
```
curl -fsSL https://sing-box.app/install.sh | sh -s -- --version 1.12.13
```
### private_key生成命令：
```
sing-box generate reality-keypair
```

### 修改/etc/sing-box/config.json：
<details>
<summary>点击展开查看完整代码</summary>
  
```
{
  "inbounds": [
    {
      "type": "anytls", // 协议类型：AnyTLS，提供增强的混淆能力
      "listen": "::",   // 监听地址："::" 表示同时监听 IPv4 和 IPv6
      "listen_port": 443, // 监听端口：建议保持 443 以模拟正常 HTTPS 流量
      "users": [
        {
          "name": "saibo",      // 用户名
          "password": "saiboyunti" // 认证密码
        }
      ],
      "tls": {
        "enabled": true,          // 启用 TLS
        "server_name": "www.bing.com", // 伪装域名：向外展示的域名
        "reality": {
          "enabled": true,        // 启用 REALITY 传输
          "handshake": {
            "server": "www.bing.com", // 真实握手目标服务器
            "server_port": 443
          },
          // 这里的 Private Key 必须由 sing-box generate reality-keypair 命令生成
          "private_key": "cJP6Fk4aHeImdO1NdrO3MtLgxX3R3iyozbqy94IqfnY",
          "short_id": "a1b2c3d4e5f67890" // 简短 ID，用于识别合法客户端
        }
      },
      "padding_scheme": [ // 填充方案：用于混淆数据包长度指纹
        "stop=8",        // 停止策略
        "0=50-100",      // 定义前几个关键包的随机长度范围
        "1=150-500",
        "2=500-1200,c,500-1200,c,500-1200",
        "3=20-100,500-1200",
        "4=600-1100",
        "5=400-900",
        "6=700-1300",
        "7=300-800"
      ]
    }
  ]
}
```
</details>

### 检查配置文件是否正确：
```
sing-box check -c /etc/sing-box/config.json
```

### 重启singbox：
```
systemctl restart sing-box
```

### 检查运行状态：
```
systemctl status sing-box
```

### 新建config.json文件：
<details>
<summary>点击展开查看完整代码</summary>

```
{
  "log": {
    "level": "info",
    "timestamp": true
  },
  "dns": {
    "servers": [
      {
        "tag": "google",
        "type": "tls",
        "server": "8.8.8.8",
        "detour": "anytls-out"
      },
      {
        "tag": "local",
        "type": "udp",
        "server": "223.5.5.5"
        // 删除了这里的 detour: direct
      }
    ],
    "strategy": "ipv4_only",
    "final": "google"
  },
  "inbounds": [
    {
      "type": "tun",
      "address": "172.19.0.1/30",
      "auto_route": true,
      "strict_route": true,
      "sniff": true
    }
  ],
  "outbounds": [
    {
      "type": "anytls",
      "tag": "anytls-out",
      "server": "70.39.194.224",
      "server_port": 443,
      "password": "saiboyunti",
      "tls": {
        "enabled": true,
        "server_name": "www.bing.com",
        "utls": {
          "enabled": true,
          "fingerprint": "chrome"
        },
        "reality": {
          "enabled": true,
          "public_key": "0fhmTgsjGIjRlxTaBeuJY4N_0fhGeLQMFgE2VcBoVic",
          "short_id": "a1b2c3d4e5f67890"
        }
      }
    },
    {
      "type": "direct",
      "tag": "direct"
    }
  ],
  "route": {
    "rules": [
      {
        "protocol": "dns",
        "action": "hijack-dns"
      },
      {
        "ip_is_private": true,
        "action": "route",
        "outbound": "direct"
      },
      {
        "action": "route",
        "outbound": "anytls-out"
      }
    ],
    "auto_detect_interface": true,
    "default_domain_resolver": "local" // 修复 WARN 警告
  }
}
```
</details>


### 启动singbox客户端：
```
sing-box.exe run -c config.json
```

### bat脚本启动：
```
@echo off
cd /d %~dp0
sing-box.exe run -c config.json
pause
```







<div align="center">
  <div style="background-color: #050a10; border: 2px solid #00f3ff; border-radius: 10px; padding: 20px; width: 90%; max-width: 600px; position: relative; overflow: hidden; font-family: 'Courier New', Courier, monospace; box-shadow: 0 0 20px rgba(0, 243, 255, 0.3);">
    
    <div style="color: #00f3ff; font-weight: bold; font-size: 18px; margin-bottom: 20px; text-shadow: 0 0 10px #00f3ff;">
      LIVE_NODE_CONNECTION_STATUS: [STABLE]
    </div>

    <svg width="400" height="150" viewBox="0 0 400 150">
      <circle cx="50" cy="75" r="8" fill="#ff00ff">
        <animate attributeName="r" values="8;10;8" dur="1.5s" repeatCount="indefinite" />
        <animate attributeName="opacity" values="1;0.5;1" dur="1.5s" repeatCount="indefinite" />
      </circle>
      <text x="35" y="105" fill="#ff00ff" font-size="12">LOCAL</text>

      <line x1="60" y1="75" x2="340" y2="75" stroke="#00f3ff" stroke-width="2" stroke-dasharray="10,5">
        <animate attributeName="stroke-dashoffset" from="100" to="0" dur="2s" repeatCount="indefinite" />
      </line>

      <circle r="4" fill="#00f3ff">
        <animateMotion path="M 60 75 L 340 75" dur="1.5s" repeatCount="indefinite" />
      </circle>

      <circle cx="350" cy="75" r="8" fill="#00f3ff">
        <animate attributeName="r" values="8;12;8" dur="1s" repeatCount="indefinite" />
      </circle>
      <text x="315" y="105" fill="#00f3ff" font-size="12">REMOTE_NODE</text>
    </svg>

    <div style="position: absolute; top: 0; left: 0; width: 100%; height: 2px; background: rgba(0, 243, 255, 0.5); box-shadow: 0 0 10px #00f3ff; animation: scan 3s linear infinite;"></div>

    <div style="text-align: left; background: rgba(0,0,0,0.5); padding: 10px; border: 1px solid #333; height: 60px; overflow: hidden; color: #00ff00; font-size: 12px; line-height: 1.5;">
       > INITIALIZING AnyTLS PROTOCOL...<br>
       > HANDSHAKE WITH REALITY SERVER: SUCCESS<br>
       > ENCRYPTED TUNNEL ESTABLISHED: 128-BIT AES<br>
       > PACKET TRANSMISSION ACTIVE...
    </div>

    <style>
      @keyframes scan {
        0% { top: 0%; }
        100% { top: 100%; }
      }
    </style>

    <br>
    <a href="https://www.youtube.com/watch?v=AmA37gOaqOs" target="_blank">
      <button style="background: none; border: 1px solid #ff00ff; color: #ff00ff; padding: 10px 20px; cursor: pointer; font-weight: bold; text-shadow: 0 0 5px #ff00ff; box-shadow: 0 0 10px #ff00ff inset;">
        ENTER THE MATRIX [VIDEO TUTORIAL]
      </button>
    </a>
  </div>
</div>
