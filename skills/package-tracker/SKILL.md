---
name: package-tracker
description: Query package/express tracking worldwide via unified API. Integrates with 快递鸟 (Kdniao) and is extensible to other providers. Use when the user asks to track a parcel, query logistics, look up express by tracking number, or integrate courier tracking.
homepage: https://github.com/openlang-cn/package-tracker.git
metadata: {"openclaw":{"emoji":"📦","homepage":"https://github.com/openlang-cn/package-tracker.git","requires":{"bins":["python"]}}}
---

# Package Tracker

## Purpose

Helps implement and use a unified package tracking layer that can integrate multiple courier APIs (starting with 快递鸟). Use this skill when:

- User wants to track a parcel by tracking number
- User asks to integrate express/courier query in code
- User mentions 快递鸟, Kdniao, or “快递查询”

## Quick Start

1. **Config**: Copy `package_tracker.json.example` to `package_tracker.json`, then fill in `providers.kdniao.ebusiness_id` and `providers.kdniao.api_key`.

2. **Query** (Python):
   ```bash
   python -m package_tracker track <ShipperCode> <LogisticCode>
   # e.g. python -m package_tracker track ZTO 638650888018
   ```

3. **In code**:
   ```python
   from package_tracker import get_tracker
   tracker = get_tracker()
   result = tracker.track(shipper_code="ZTO", logistic_code="638650888018")
   ```

## Provider: 快递鸟 (Kdniao)

- **即时查询** RequestType: `1002`
- **Endpoint**: `https://api.kdniao.com/Ebusiness/EbusinessOrderHandle.aspx`
- **RequestData** (JSON): `ShipperCode`, `LogisticCode`, optional `OrderCode`, optional `CustomerName` (required for 顺丰 SF: last 4 digits of phone)
- **Sign**: `RequestData` (unencoded JSON, no spaces) + ApiKey → MD5 → Base64 → URL-encode

Do not put API keys in code; store them in `package_tracker.json` (and avoid committing it).

## Adding Another Provider

1. Add a new module under `package_tracker/providers/` implementing the same track interface (e.g. `track(shipper_code, logistic_code, **kwargs) -> dict`).
2. Register in `package_tracker/__init__.py` and select by config (set `default`) or pass `provider=...` in `get_tracker(...)`.

## Reference

- 快递鸟即时查询: [API 文档](https://www.kdniao.com/api-track)
- 完整实现（CLI + Python 包、JSON 配置）见仓库根目录 `package_tracker/` 与 `README.md`；通过 ClawHub 仅安装本技能时，需另行克隆该仓库或安装对应包。
