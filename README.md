# 🚀 【最隐蔽的节点】AnyTLS+Reality+SingBox节点搭建教程

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

