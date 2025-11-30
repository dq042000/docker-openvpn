# Docker OpenVPN 快速部署指南

本專案使用 [kylemanna/openvpn](https://hub.docker.com/r/kylemanna/openvpn) Docker 映像，快速建置 OpenVPN 伺服器。

---

## 目錄結構

- `docker-compose.yml`：主要 Docker Compose 設定檔
- `openvpn-data/`：持久化 OpenVPN 設定與金鑰資料夾

---

## 快速開始

### 1. 初始化 OpenVPN 設定（僅需一次）
```sh
# 進入 openvpn-data 目錄
mkdir -p openvpn-data
cd openvpn-data

# 初始化 PKI 與 OpenVPN 設定
docker run -v $PWD:/etc/openvpn --rm openvpn ovpn_genconfig -u tcp://你的伺服器IP:10443
docker run -v $PWD:/etc/openvpn --rm -it openvpn ovpn_initpki nopass
```

### 2. 啟動 OpenVPN 服務
```sh
cd /path/to/docker-openvpn
docker compose up -d
```

### 3. 建立用戶端憑證
```sh
docker compose exec openvpn easyrsa build-client-full <用戶名> nopass
docker compose exec openvpn ovpn_getclient <用戶名> > <用戶名>.ovpn
```

---

## 用戶端連線

1. 下載 `<用戶名>.ovpn` 檔案到用戶端設備。
2. 使用 OpenVPN 客戶端（如 Windows、macOS、Android、iOS）匯入並連線。

---

## 相關設定說明

- **Port**：預設對外開放 10443/tcp
- **資料夾**：`openvpn-data` 內含所有伺服器設定與金鑰，請妥善備份
- **IP Forwarding**：已於容器內啟用
- **NAT**：kylemanna/openvpn 映像自動處理
- **權限**：建議以 root 權限執行初始化與啟動

---

## 常見問題

- **Q: 如何變更連接埠？**
  - 修改 `docker-compose.yml` 的 `ports` 與初始化時的 `ovpn_genconfig` 參數。
- **Q: 如何新增/撤銷用戶？**
  - 新增：`easyrsa build-client-full <用戶名> nopass`
  - 撤銷：`easyrsa revoke <用戶名> && easyrsa gen-crl`
- **Q: openvpn-data 可以直接刪除嗎？**
  - 不建議，該資料夾包含所有金鑰與設定。

---

## 參考連結
- [kylemanna/openvpn 官方文件](https://github.com/kylemanna/docker-openvpn)
- [OpenVPN 官方網站](https://openvpn.net/)