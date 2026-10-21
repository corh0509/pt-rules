# pt-rules

mihomo / Clash 用的**规则集镜像**，每天自动从上游同步一次，给自己的代理配置当 `rule-providers` 和 `geodata` 源。

> ⚠️ 自动生成，**不要手工改这里的文件**——下次同步会被覆盖。

---

## 内容

| 文件 | 来源 | 用途 |
|---|---|---|
| `ruleset/direct.txt` | [Loyalsoldier/clash-rules](https://github.com/Loyalsoldier/clash-rules) | 直连域名规则 |
| `ruleset/proxy.txt` | 同上 | 需要走代理的域名 |
| `ruleset/apple.txt` | 同上 | Apple 服务（通常直连） |
| `ruleset/emos-mihomo.yaml` | [binaryu/emos-proxy-rule](https://github.com/binaryu/emos-proxy-rule) | 分流规则（classical） |
| `geoip.dat` | [MetaCubeX/meta-rules-dat](https://github.com/MetaCubeX/meta-rules-dat) | GeoIP 数据库 |
| `geosite.dat` | 同上 | GeoSite 数据库 |
| `.keepalive` | 本仓库 Actions 写入 | 保活时间戳（防止 60 天无提交被归档） |

## 更新机制

GitHub Actions → `.github/workflows/sync.yml`：

- 每天 **UTC 03:00（北京时间 11:00）** 自动拉取上游；
- 支持手动触发（Actions → Sync Rules → Run workflow）；
- 有变化才提交，提交信息形如 `Auto sync rules: 2026-10-21 03:12`；
- 每次成功后写 `.keepalive`，保持仓库活跃。

## 怎么用（mihomo）

```yaml
rule-providers:
  proxy:
    type: http
    behavior: domain
    format: text
    interval: 86400
    url: "https://raw.githubusercontent.com/corh0509/pt-rules/main/ruleset/proxy.txt"
    path: ./ruleset/proxy.txt

  direct:
    type: http
    behavior: domain
    format: text
    interval: 86400
    url: "https://raw.githubusercontent.com/corh0509/pt-rules/main/ruleset/direct.txt"
    path: ./ruleset/direct.txt

  apple:
    type: http
    behavior: domain
    format: text
    interval: 86400
    url: "https://raw.githubusercontent.com/corh0509/pt-rules/main/ruleset/apple.txt"
    path: ./ruleset/apple.txt
```

GeoIP / GeoSite 直接指向 release 文件：

```yaml
geodata-mode: true
geox-url:
  geoip: "https://raw.githubusercontent.com/corh0509/pt-rules/main/geoip.dat"
  geosite: "https://raw.githubusercontent.com/corh0509/pt-rules/main/geosite.dat"
```

> 国内访问 raw.githubusercontent.com 不稳的话，可把上面的域名换成自己的反代（例如 NAS 上的 Lucky）。

## 说明

- 仓库是 **public** 的，内容全部来自公开上游，不含任何个人配置或凭据；
- 上游若改了文件结构，同步任务会失败，需要顺手更新 `sync.yml`；
- 规则条数：direct ≈ 110k、proxy ≈ 27k、apple ≈ 166（2026-10 时点）。
