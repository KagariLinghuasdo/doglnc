# LNC 和动物朋友将领拓展：协作规则

本文档适用于仓库根目录 `D:\Documents\Paradox Interactive\Hearts of Iron IV\mod\doglnc`。
所有修改、实验和代码评审都应遵守以下规则。

## 一、强制规则

1. 遇到 HOI4 专有名词、脚本关键字、触发器、效果、修正或文件格式问题，先查询 [Hearts of Iron 4 Wiki](https://hoi4.paradoxwikis.com/Hearts_of_Iron_4_Wiki) 和 [Modding](https://hoi4.paradoxwikis.com/Modding)，再编码。
2. 每个面向用户的回答开头使用“哥哥”。
3. 不覆盖或撤销其他人的未提交修改。开始工作前先查看 `git status --short`；只修改本任务需要的文件。
4. HOI4 脚本对标识符、作用域、括号和文件路径很敏感。新增内容优先遵循同目录已有写法，不要为了格式统一而顺手重写无关文件。
5. 普通脚本、`descriptor.mod` 和其他文本使用 UTF-8 无 BOM；本地化 `.yml` 使用 UTF-8 BOM。只有本地化文本或已有中文内容确有需要时才使用 Unicode，并保留文件原有换行和编码。
6. 查询 Wiki 时优先使用真实浏览器访问 [Hearts of Iron 4 Wiki](https://hoi4.paradoxwikis.com/Hearts_of_Iron_4_Wiki) 和 [Modding](https://hoi4.paradoxwikis.com/Modding) 页面及其站内搜索；若普通抓取接口返回 `401 Unauthorized`，不要据此判断 Wiki 不可访问。真实浏览器已验证可正常打开这两个页面并搜索 `country_event`。
7. 在新增或修改任何 HOI4 修正 buff、modifier、trigger、effect、决议字段或事件字段之前，必须先通过真实浏览器查阅对应 Wiki 文档或站内搜索结果，确认字段名称、数值含义和适用作用域；不得仅凭记忆或仓库内已有写法直接编码。若 Wiki 未明确说明，必须在交付说明中标注未确认项，并优先进行游戏内日志验证。

## 二、仓库地图

这是一个直接放在 HOI4 用户模组目录下的内容模组，根目录必须保持与游戏目录相同的相对结构。

- `descriptor.mod`：模组名称、版本、标签、支持的游戏版本和 Steam Workshop 信息。
- `common/abilities/`：指挥官能力。
- `common/characters/`：角色定义及其国家、职位和特质引用。
- `common/country_leader/`：国家领袖特质。
- `common/decisions/`：决议和决议分类；`categories/` 存放分类。
- `common/dynamic_modifiers/`：动态修正。
- `common/ideas/`：国家精神和想法。
- `common/on_actions/`：挂接游戏生命周期事件的效果。
- `common/scripted_effects/`：可复用脚本效果。
- `common/unit_leader/`：陆军、海军和空军指挥官特质。
- `events/`：事件定义及 `add_namespace` 命名空间。
- `history/general/`：通用历史角色或顾问内容。
- `interface/`：`.gfx` 图片定义。
- `gfx/`：实际图片资源。
- `localisation/simp_chinese/`：简体中文本地化。
- `localisation/english/`：英文本地化；新增可见文本时应同步补充。

当前 `descriptor.mod` 声明的模组版本为 `0.1`，支持游戏版本为 `1.17.3.0`。修改游戏版本兼容性前先确认实际测试版本。

## 三、命名与脚本约定

- 新增内容优先使用 `lnc_` 前缀；事件使用 `LNC_` 命名空间，避免与原版和其他模组冲突。
- 文件名使用描述性的小写或现有文件的既有风格；不要把多个无关系统塞进一个文件。
- `common/scripted_effects/` 中的效果名、`events/` 中的事件 ID、决议 ID、想法 ID、动态修正 ID 和特质 ID 必须全局唯一。
- 新增事件时先声明唯一命名空间，再使用 `命名空间.编号` 形式的事件 ID；事件选项、标题和描述必须有对应本地化键。
- 新增角色时同时检查 `common/characters/`、角色生成 scripted effect、职位/特质引用以及对应本地化，避免只定义一半导致日志报错。
- 变量、国家旗帜、想法和动态修正的名称应体现所属功能，例如现有 `LNC_`、`JAP_` 前缀；修改变量前确认当前作用域。
- 复杂的 `on_daily` 或变量计算效果要控制执行范围，并在代码旁留下简短注释说明计算目的，避免无条件对所有国家重复执行高成本逻辑。

## 四、本地化与资源

- 本地化文件必须放在 `localisation/<language>/`，文件名使用现有的 `*_l_simp_chinese.yml` 或 `*_l_english.yml` 形式。
- 文件首行语言声明必须与文件内容匹配，例如 `l_simp_chinese:` 或 `l_english:`；键名与脚本引用必须完全一致。
- 本地化键使用稳定、唯一的标识符；修改键名时同步搜索所有脚本引用。
- 保留 HOI4 本地化所需的缩进、冒号和转义格式；不要把普通 YAML 工具自动格式化后直接覆盖游戏文件。
- 新增 `.gfx` 定义时确认实际图片文件存在、路径大小写一致，并检查界面引用的 sprite 名称。

## 五、修改流程

1. 阅读相关目录和引用链，先定位定义、调用方和本地化。
2. 查询 Wiki 确认关键字、作用域、触发器、效果和文件格式。
   - 涉及修正 buff 或具体字段时，先在真实浏览器打开对应的 Modifiers、Effects、Triggers、Decisions 或相关页面，记录确认过的字段，再开始编辑。
3. 做最小范围修改，并保持现有命名和缩进风格。
4. 用 `rg` 搜索新增 ID，检查是否重复、是否存在未定义引用和拼写不一致。
5. 检查括号配对、事件命名空间、本地化文件首行、`.gfx` 资源路径和 `descriptor.mod`。
6. 启动游戏或加载存档进行实际触发测试；重点查看 `Documents\Paradox Interactive\Hearts of Iron IV\logs\error.log` 和 `game.log`。
7. 在提交或交付前再次运行 `git status --short` 和 `git diff --check`，确认没有意外改动。

本仓库目前没有自动化构建或测试脚本，因此“验证通过”必须明确说明做过哪些静态检查和游戏内测试；没有启动游戏时，不要声称功能已完成运行验证。

## 六、评审重点

优先检查以下问题：

- 作用域错误，例如国家、角色、事件目标和变量属于不同上下文。
- 事件 ID、命名空间、决议 ID、特质 ID 或本地化键冲突。
- 缺少本地化、错误的语言前缀、错误编码或 YAML 缩进。
- `on_daily` 无限制执行导致性能问题。
- scripted effect 只定义未调用，或调用了不存在的 effect/trigger。
- 修改 `descriptor.mod` 后支持版本、路径或 Workshop 字段不一致。
- 代码格式变化掩盖了真正的行为变化；评审应优先关注行为回归和日志错误。
