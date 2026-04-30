# JWST Proposal Scraper

从 [STScI 官方网站](https://www.stsci.edu/jwst/science-execution/approved-programs) 抓取 JWST 已批准 proposal 的信息，支持按 **Observing Cycle**、**Programmatic Category** 和 **科学子类别** 过滤。

## 依赖管理

本项目使用 [uv](https://docs.astral.sh/uv/) 管理 Python 依赖，无需手动 `pip install`。

```bash
# 安装 uv（若未安装）
curl -LsSf https://astral.sh/uv/install.sh | sh

# 首次使用时同步依赖（自动读取 pyproject.toml）
uv sync
```

## 快速开始

```bash
# 列出所有 Cycle 的可用科学类别（不抓取数据）
uv run python scrape_stsci_proposals.py --list-categories

# 抓取 Cycle 5 的所有类型和所有科学类别
uv run python scrape_stsci_proposals.py --cycle 5

# 抓取所有 Cycle（1-5）
uv run python scrape_stsci_proposals.py --all-cycles
```

## 参数说明

| 参数 | 说明 | 示例 |
|------|------|------|
| `--cycle N [N ...]` | 指定 Cycle（与 `--all-cycles` 二选一，必填） | `--cycle 4 5` |
| `--all-cycles` | 抓取所有 Cycle（1-5） | `--all-cycles` |
| `--types TYPE [...]` | Programmatic Category，多选，默认全选 | `--types go gto` |
| `--science-categories CAT [...]` | GO 项目科学子类别（支持部分名称匹配），不指定则抓取全部 | `--science-categories "Galaxies"` |
| `--list-categories` | 显示每个 Cycle 的科学类别列表后退出 | `--list-categories` |

### Programmatic Categories（`--types` 可选值）

| 值 | 含义 |
|----|------|
| `go` | General Observer（社区公开竞争申请） |
| `gto` | Guaranteed Time Observations（仪器团队保障时间，Cycle 1-3）|
| `ddt` | Director's Discretionary Time（紧急时变天体） |
| `ers` | Early Release Science（首批五个月科学观测，Cycle 1）|
| `joint` | Joint Observing Programs（JWST 联合其他望远镜）|

> **注意**：Calibration 程序不在抓取范围内。GTO/DDT/ERS/JOINT 页面无科学子类别，`--science-categories` 对其不生效。

## 使用示例

```bash
# 抓取 Cycle 4 和 5 的 GO 项目中的星系相关类别
uv run python scrape_stsci_proposals.py \
    --cycle 4 5 \
    --types go \
    --science-categories "Galaxies" "High-Redshift Galaxies and the Distant Universe" \
                         "Nearby Galaxies to Cosmic Noon" \
                         "Supermassive Black Holes and Active Galaxies"

# 抓取 Cycle 3 的 GO + GTO 全部科学类别
uv run python scrape_stsci_proposals.py --cycle 3 --types go gto

# 抓取所有 Cycle 的 DDT 项目
uv run python scrape_stsci_proposals.py --all-cycles --types ddt

# 抓取 Cycle 1-3 的 GO 项目，仅保留 ISM 和大尺度结构类别
uv run python scrape_stsci_proposals.py \
    --cycle 1 2 3 \
    --types go \
    --science-categories "Interstellar Medium" "Large Scale Structure" "Intergalactic Medium"
```

## 输出文件

结果保存在脚本同目录，文件名格式：

```
jwst_proposals_c{cycle}_{types}.csv     # 完整数据（UTF-8 BOM）
jwst_proposals_c{cycle}_{types}.xlsx    # 带格式的 Excel，按 Programmatic Category 分 Sheet
scrape_stsci.log                        # 运行日志（追加写入）
```

**CSV/Excel 字段说明：**

| 字段 | 说明 |
|------|------|
| `program_id` | Program ID（与 STScI 数据库一致）|
| `title` | Proposal 标题 |
| `pi` | PI 及 Co-PI 姓名 |
| `program_type` | Programmatic Category（GO/GTO/DDT/ERS/JOINT）|
| `cycle` | 观测周期（1-5）|
| `sci_category` | GO 项目科学子类别 |
| `obs_type` | 观测类型（GO/Survey/Treasury/Archive 等）|
| `instruments` | 使用仪器及模式 |
| `allocated_hours` | 获批时间（小时）|
| `exclusive_access_months` | 专属访问期（月） |
| `program_href` | STScI 页面上的详情链接 |

---

## 各 Cycle 科学子类别（GO 项目）

### Cycle 1（2022 年 7 月起）

- Exoplanets and Exoplanet Formation
- Galaxies
- Intergalactic Medium and the Circumgalactic Medium
- Large Scale Structure of the Universe
- Solar System Astronomy
- Stellar Physics and Stellar Types
- Stellar Populations and the Interstellar Medium
- Supermassive Black Holes and Active Galaxies

### Cycle 2（2023 年 7 月起）

- Exoplanets and Exoplanet Formation
- Galaxies
- Intergalactic Medium and the Circumgalactic Medium
- Large Scale Structure of the Universe
- Solar System Astronomy
- Stellar Physics and Stellar Types
- Stellar Populations and the Interstellar Medium
- Supermassive Black Holes and Active Galaxies

### Cycle 3（2024 年 7 月起）

- Exoplanets and Exoplanet Formation
- Galaxies
- Intergalactic Medium and the Circumgalactic Medium
- Large Scale Structure of the Universe
- Solar System Astronomy
- Stellar Physics and Stellar Types
- Stellar Populations and the Interstellar Medium
- Supermassive Black Holes and Active Galaxies

### Cycle 4（2025 年 7 月起）

- Exoplanet Atmospheres and Habitability
- Exoplanet System Formation and Dynamics
- Gas, Dust, and the ISM
- High-Redshift Galaxies and the Distant Universe
- Nearby Galaxies to Cosmic Noon
- Solar System Astronomy
- Stars and Stellar Populations
- Supermassive Black Holes and Active Galaxies

### Cycle 5（2026 年 7 月起）

- Exoplanet Atmospheres and Habitability
- Exoplanetary System Formation and Dynamics
- Gas, Dust, and the ISM
- High-Redshift Galaxies and the Distant Universe
- Nearby Galaxies to Cosmic Noon
- Solar System Astronomy
- Stars and Stellar Populations
- Supermassive Black Holes and Active Galaxies

> **注意**：`--science-categories` 支持部分名称匹配（不区分大小写）。例如 `"Galaxies"` 会同时匹配 Cycle 1-3 的 `"Galaxies"` 以及 Cycle 4-5 的 `"High-Redshift Galaxies and the Distant Universe"` 和 `"Nearby Galaxies to Cosmic Noon"`。

---

## 数据来源

- STScI JWST Approved Programs: https://www.stsci.edu/jwst/science-execution/approved-programs
- 数据每次从官网实时抓取，反映最新审批状态。
