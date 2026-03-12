# Package Tracker

统一快递/物流查询，可扩展接入多家快递 API。当前已接入 **快递鸟 (Kdniao)** 即时查询。

Query package/express tracking worldwide via unified API. Integrates with 快递鸟 (Kdniao) and is extensible to other providers.

## 环境准备

1. 在 [快递鸟](https://www.kdniao.com/) 注册并开通「即时查询」。
2. 获取 **商户 ID (EBusinessID)** 和 **ApiKey**。
3. **配置文件（JSON）**：复制 `skills/package-tracker/package_tracker.json.example` 为 `package_tracker.json`，填写 `default` 与 `providers.kdniao`。

## 配置（JSON）

在项目根目录（默认读取 `./package_tracker.json`）或通过 CLI `--config` 指定配置文件路径。仅支持 **JSON**。

- **default**：默认供应商，不指定 `--provider` 时使用。
- **providers**：各供应商的选项（如 `kdniao` 的 `ebusiness_id`、`api_key`、`sandbox`，以及可选的 `api_url` / `sandbox_url` / `request_type` 用于覆盖默认接口参数）。

示例见 `skills/package-tracker/package_tracker.json.example`。

## 使用方式

### 命令行

```bash
# 使用配置中的默认供应商查询
cd skills/package-tracker
python -m package_tracker track ZTO 638650888018

# 指定供应商与配置文件
python -m package_tracker track ZTO 638650888018 --provider kdniao --config ./package_tracker.json

# 顺丰需传收/寄件人手机后 4 位
python -m package_tracker track SF SF00003618100 --customer-name 1234

# 输出原始 JSON
python -m package_tracker track ZTO 638650888018 --json

# 使用沙箱
python -m package_tracker track ZTO 638650888018 --sandbox
```

### Python 代码

```python
from package_tracker import get_tracker

# 使用配置中的默认供应商
tracker = get_tracker()
# 或指定供应商与配置文件
# tracker = get_tracker(provider="kdniao", config_path="./package_tracker.json")
result = tracker.track(shipper_code="ZTO", logistic_code="638650888018")

if result.get("Success"):
    print("状态:", result.get("State"))
    for t in reversed(result.get("Traces", [])):
        print(t.get("AcceptTime"), t.get("AcceptStation"))
else:
    print("失败:", result.get("Reason"))
```

顺丰需传 `customer_name`（手机号后 4 位）：

```python
result = tracker.track(
    shipper_code="SF",
    logistic_code="SF00003618100",
    customer_name="1234",
)
```

## 项目结构

```
package-tracker/
├── skills/package-tracker/          # ClawHub 技能（可发布到 clawhub.com）
│   └── SKILL.md
│   ├── package_tracker/             # 技能内自包含 Python 包 + CLI
│   ├── package_tracker.json.example
│   └── requirements.txt
├── CLAWHUB.md         # ClawHub 发布说明
└── README.md
```

## 发布到 ClawHub

本技能已按 [ClawHub](https://clawhub.com/) 规范编写，可发布到 OpenClaw 公共技能中心。发布步骤见 [CLAWHUB.md](CLAWHUB.md)。

## 扩展其他快递 API

1. 在 `skills/package-tracker/package_tracker/` 下新增 provider 模块，实现 `BaseTracker` 的 `track(...)`。
2. 在 `skills/package-tracker/package_tracker/registry.py` 中注册并可通过 `provider` 参数选择（或在 JSON 里设置 `default`）。

## 快递鸟说明

- 接口：即时查询，RequestType `1002`。
- 文档：[快递鸟 API 文档](https://www.kdniao.com/api-track)。
- 快递公司编码见官方《快递公司编码表》。
