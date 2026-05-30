<%*
/*
* ==================================================
* TASK SYMBOL TEMPLATE
* 作者：Kerwin
* 用途：
*   快速插入 Obsidian / Tasks 自定义任务状态
* 功能：
*   1. 选择任务符号
*   2. 可输入任务内容
*   3. 内容为空时只插入符号
*   4. 用户取消时不插入任何内容
* 说明：
*   输出示例：
*   - [ ] 普通任务
*   - [/] 进行中任务
*   - [!] 重要任务
* ==================================================
*/

// ── 配置 ──
const CONFIG = {
    symbols: {
        "- [ ]": "☐ Unchecked",
        "- [x]": "☑ Checked",
        "- [>]": "⏩ Rescheduled",
        "- [<]": "⏪ Scheduled",
        "- [!]": "❗ Important",
        "- [-]": "✖ Cancelled",
        "- [/]": "🏗 In Progress",
        "- [?]": "❓ Question",
        "- [*]": "⭐ Star",
        "- [n]": "📝 Note",
        "- [l]": "📍 Location",
        "- [i]": "ℹ Information",
        "- [I]": "💡 Idea",
        "- [S]": "💰 Amount",
        "- [p]": "👍 Pro",
        "- [c]": "👎 Con",
        "- [b]": "🔖 Bookmark",
        "- [\"]": "💬 Quote",
        "- [u]": "⬆ Up",
        "- [d]": "⬇ Down",
    },
};

try {

    // ── 选择任务状态 ──
    const selectedSymbol =
        await tp.system.suggester(
            Object.values(CONFIG.symbols),
            Object.keys(CONFIG.symbols),
            true,
            "选择任务状态或符号"
        );

    // ── 输入任务内容 ──
    const taskContent =
        await tp.system.prompt(
            "输入任务内容（可留空）:",
            "",
            false
        );

    // ── 构建输出内容 ──
    const contentPart =
        taskContent && taskContent.trim()
            ? " " + taskContent.trim()
            : "";

    tR +=
        selectedSymbol +
        contentPart;

} catch {

    // 用户按 Esc 取消，静默退出
}
-%>
