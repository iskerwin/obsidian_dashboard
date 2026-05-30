<%*
/*
* ==================================================
* CALLOUT TEMPLATE
* 作者：Kerwin
* 用途：
*   快速插入 Obsidian Callout
* 功能：
*   1. 选择 Callout 类型
*   2. 选择折叠状态
*   3. 可输入标题
*   4. 可输入多行内容
*   5. 自动生成 Obsidian Callout 语法
* 说明：
*   输出示例：
*   > [!tip]+ 标题
*   > 内容第一行
*   > 内容第二行
* ==================================================
*/

// ── 配置 ──
const CONFIG = {
    callouts: {
        tip:      "💡 Tip",
        bug:      "🐛 Bug",
        note:     "📝 Note",
        info:     "ℹ️ Info",
        todo:     "✅ Todo",
        quote:    "💬 Quote",
        danger:   "🚨 Danger",
        warning:  "⚠️ Warning",
        failure:  "❌ Failure",
        example:  "🔍 Example",
        success:  "🎉 Success",
        question: "❓ Question",
        abstract: "📊 Abstract",
    },
    foldOptions: {
        None: "",
        Expanded: "+",
        Collapsed: "-",
    },
};

try {

    // ── 选择 Callout 类型 ──
    const type =
        await tp.system.suggester(
            Object.values(CONFIG.callouts),
            Object.keys(CONFIG.callouts),
            true,
            "Select callout type."
        );

    // ── 选择折叠状态 ──
    const fold =
        await tp.system.suggester(
            Object.keys(CONFIG.foldOptions),
            Object.values(CONFIG.foldOptions),
            true,
            "Select callout fold option."
        );

    // ── 输入标题 ──
    const title =
        await tp.system.prompt(
            "Title (optional):",
            "",
            false
        );

    // ── 输入内容 ──
    const content =
        await tp.system.prompt(
            "Content (new line → Shift+Enter):",
            "",
            true,
            true
        );

    // ── 构建 Callout Header ──
    const titlePart =
        title && title.trim()
            ? " " + title.trim()
            : "";

    const calloutHead =
        "> [!" + type + "]" + fold + titlePart;

    // ── 构建 Callout Body ──
    const contentText =
        content ? content.trimEnd() : "";

    const contentLines =
        contentText
            ? contentText
                .split("\n")
                .map(line => "> " + line)
                .join("\n")
            : "";

    // ── 输出结果 ──
    tR += contentLines
        ? calloutHead + "\n" + contentLines
        : calloutHead;

} catch {

    // 用户按 Esc 取消，静默退出
}
-%>
