# Deploy Shadowsocks-rust
Deploy shadowsocks-rust by Docker and use watchtower to autoupdate container.


## 安装 Docker
`curl -fsSL https://get.docker.com -o get-docker.sh`

`DRY_RUN=1 sh ./get-docker.sh`

## Shadowsocks-rust 配置
`mkdir -p /etc/shadowsocks-rust`
```
cat > /etc/shadowsocks-rust/config.json <<EOF
{
    "server":"0.0.0.0",
    "server_port":9000,
    "password":"password0",
    "timeout":300,
    "method":"aes-256-gcm",
    "nameserver":"8.8.8.8",
    "mode":"tcp_and_udp"
}
EOF
```

## Pull the image and start 
```
docker pull ghcr.io/shadowsocks/ssserver-rust:latest && docker pull containrrr/watchtower
```
```
docker run --name ss-rust --restart always -p 9000:9000/tcp -p 9000:9000/udp -v /etc/shadowsocks-rust/config.json:/etc/shadowsocks-rust/config.json -dit ghcr.io/shadowsocks/ssserver-rust:latest
```
```
docker run -d --name watchtower --restart=always -v /var/run/docker.sock:/var/run/docker.sock containrrr/watchtower --cleanup
```

# Uninstall 
```
docker stop ss-rust && docker stop watchtower
docker rm -f ss-rust && docker rm -f watchtower
systemctl stop docker && systemctl disable docker
apt purge docker-ce docker-ce-cli containerd.io
rm -rf /var/lib/docker
rm -rf /var/lib/containerd
rm -rf /etc/shadowsocks
```

## Thanks
1. [shadowsocks-rust](https://github.com/shadowsocks/shadowsocks-rust)
2. [Docker Docs](https://docs.docker.com/engine/install/ubuntu/)
3. [watchtower: A process for automating Docker container base image updates.](https://github.com/containrrr/watchtower)