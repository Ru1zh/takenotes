# takenotes

`takenotes` 用于将一个或多个 PDF、PowerPoint、Word 学习材料整理为有来源位置的中文 Obsidian 学习笔记。它支持分别生成，也支持把用户选择的多个文件合并为一个去重后的 note。只有以英文为主要教学语言的课程材料才生成一个汇总 Glossary。

## Skill 文件结构

- `SKILL.md`：触发条件、授权边界与执行入口。
- `references/study-prompt.md`：详细工作流和输出模板。
- `agents/openai.yaml`：Codex 中的显示信息、默认提示和自动触发策略。

## 生成文件存放位置

Skill 区分三个路径概念：

- `V`：当前 Obsidian Vault 根目录，包含顶层 `Notes/` 与 `Source/`。
- `D`：用户明确指定的笔记目录。
- `R`：用户明确指定的独立输出根目录。

当用户说“在 `D` 下创建笔记”或“笔记保存到 `D`”时，笔记直接写入 `D/笔记名.md`。如果 `D` 已位于 `V/Notes/**` 中，Skill 不会在 `D` 内再次创建 `Notes/` 或 `Source/`；必要图片仍保存到 Vault 顶层的 `V/Source/Img/...`。

只有用户明确使用“输出根目录”或要求“所有产物都放在该目录”时，才把该目录视为 `R`，并在其中使用 `R/Notes/...` 与 `R/Source/...`。

用户没有指定任何位置时，课程材料默认生成到：

```text
V/
├─ Notes/
│  ├─ Course/
│  │  └─ 课程标识/
│  │     ├─ 课程标识_lec1_主题名称.md
│  │     └─ 课程标识_Table of Content.md
│  └─ English/Glossary/Course/课程标识/
│     └─ 课程标识_lec1_Glossary.md   # 仅英文主语言课程材料
└─ Source/Img/Course/课程标识/
   └─ 课程标识_lec1_语义名称.png       # 仅在图片确有必要时
```

非课程学习笔记不使用课程编号、课程目录、自动 Glossary 或 `course:` 元数据，例如：

```text
V/Notes/研究方法.md
V/Source/Img/研究方法/研究方法_实验流程.png
```

例如，用户指定“在 `Notes/CS/Machine Learning/` 下创建笔记”时，笔记直接生成到：

```text
V/Notes/CS/Machine Learning/笔记名.md
```

图片仍按课程或主题保存到 `V/Source/Img/...`，不会生成 `V/Notes/CS/Machine Learning/Notes/` 或 `V/Notes/CS/Machine Learning/Source/`。

任务完成后，Skill 会报告每个实际生成文件的位置，便于直接找到笔记、目录、Glossary 和图片，而不是只给出模板路径。

此 Glossary 路径规则适用于新生成的文件；它不会自动搬迁旧 Glossary 或改写指向旧文件的 Wiki-link。

课程笔记末尾的“相关笔记”只列对应 Glossary（若有）、课程目录顺序中的上一篇和下一篇（若有），以及课程目录文件。新建或插入笔记时会检查相邻笔记的前后链接，并在获授权的范围内同步更新；若现有笔记不在授权范围内，会先列出待更新文件并请求授权。

## 使用方式

生成并分别整理多个课件：

```text
$takenotes 根据这些课件创建 Obsidian 笔记，所有产物输出到独立输出根目录 D:\NotesOutput。
```

把选定的多个文件共同生成一个 note：

```text
$takenotes 将我选择的主课件和补充课件合并为一篇 note，去除重复内容，笔记保存到 Notes/研究专题/。
```

当用户已经明确要求合并时，Skill 不会再次询问是否合并；若只提供多个文件而没有说明，默认按课程和讲次分组，仅在归属或合并意图无法可靠判断时询问。

与 Skill 一同提供的参考规范不会替代用户当前请求，也不会自动扩大文件写入范围。
