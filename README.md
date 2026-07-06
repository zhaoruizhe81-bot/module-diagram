# Module Diagram

A [Claude Code](https://docs.claude.com/en/docs/claude-code) skill that generates **thesis-style function module diagrams** (功能模块图 / 模块图) and WBS tree diagrams from a JSON module tree — outputting clean SVG (and optional PNG) with multi-level layout, vertical leaf labels, and orthogonal parent-child bus connectors.

![output](https://img.shields.io/badge/output-SVG%20%2F%20PNG-blue) ![language](https://img.shields.io/badge/language-Python-3776AB)

## Features

- 📐 **Thesis-style layout** — horizontal boxes for root/intermediate nodes, vertical boxes with one character per line for leaves.
- 🌲 **Arbitrary depth** — supports 2-, 3-, 4-, and 5-level module trees.
- 🔗 **Orthogonal connectors** — parent → horizontal bus → children, anchored to the middle child (or average of the two middle children for even counts).
- 🈯 **Chinese-ready** — SimSun/SimHei font stacks, every leaf label rendered one character per line.
- 🖼️ **SVG + PNG** — vector SVG by default; PNG via `cairosvg` (falls back to `rsvg-convert`).

## Quick Start

```bash
python3 scripts/draw_module_diagram.py \
  --config module_config.json \
  --output output/module_diagram.svg \
  --png output/module_diagram.png
```

### Input format

A JSON tree where every node is either a **string leaf** or an object with `label` and `children`:

```json
{
  "label": "大学生科技竞赛管理系统",
  "children": [
    {
      "label": "用户",
      "children": ["团队管理", "注册登录", "竞赛列表"]
    },
    {
      "label": "管理员",
      "children": ["用户管理", "竞赛审核", "成绩统计"]
    }
  ]
}
```

## Layout rules

- Root and intermediate nodes are horizontal rectangular boxes.
- Leaf nodes are vertical boxes with **one Chinese character per line**.
- Leaves at the same depth share one height, based on the longest leaf label at that depth.
- Connectors are orthogonal: parent bottom center → horizontal bus → child top centers.
- Parent connector anchors align to the **middle child**; for even child counts, to the average of the two middle children.
- The same anchor/bus rule is applied recursively at every level.

## Install as a Claude Code skill

Clone into your skills directory:

```bash
git clone https://github.com/zhaoruizhe81-bot/module-diagram.git ~/.claude/skills/module-diagram
```

The `SKILL.md` frontmatter registers it; Claude Code will pick it up automatically.

## Validation

```bash
python3 -m py_compile scripts/draw_module_diagram.py
python3 scripts/draw_module_diagram.py --config module_config.json --output out.svg --png out.png
```

When debugging alignment, inspect the SVG line coordinates — for odd child counts, the parent's vertical line x-coordinate should equal the middle child's vertical line x-coordinate.

## License

[MIT](./LICENSE)
