# WireGuard Easy

**WireGuard Easy** 是最简单的安装和管理 WireGuard 的方法，适用于任何 Linux 主机！

## 使用说明

> **14版本以上启用了bcrypt 密码哈希，以前设置密码方式失效**

`wg-password`（也称为 **wgpw**）是一个生成 bcrypt 密码哈希的脚本，旨在通过与 **`wg-easy`** 集成来提高安全性，方便管理 WireGuard 配置。

### Docker 使用方法

使用 Docker 生成 bcrypt 密码哈希，运行以下命令：

```sh
docker run -it ghcr.io/wg-easy/wg-easy wgpw YOUR_PASSWORD
```

示例输出：
```sh
PASSWORD_HASH='$2b$12$coPqCsPtcFO.Ab99xylBNOW4.Iu7OOA2/ZIboHN6/oyxca3MWo7fW'
```

如果未提供密码，工具将提示您输入：

```sh
docker run -it ghcr.io/wg-easy/wg-easy wgpw
Enter your password:  # 输入密码（输入不可见）
PASSWORD_HASH='$2b$12$coPqCsPtcFO.Ab99xylBNOW4.Iu7OOA2/ZIboHN6/oyxca3MWo7fW'
```

### 重要说明

- **在 `docker-compose.yml` 中使用**：在 `docker-compose.yml` 文件中，将生成的哈希中的每个 `$` 替换为 `$$`，以防止解释错误。

```yaml
- PASSWORD_HASH=$$2y$$10$$hBCoykrB95WSzuV4fafBzOHWKu9sbyVa34GJr8VV5R/pIelfEMYyG
```