<%*
/*
* ==================================================
* CODE BLOCK TEMPLATE
* 作者：Kerwin
* 用途：
*   快速插入代码块
* 功能：
*   1. 选择语言
*   2. 支持自定义语言
*   3. 支持多行输入
*   4. 自动生成 Markdown 代码块
* 说明：
*   输出格式：
*   ```language
*   code
*   ```
* ==================================================
*/

// ── 配置 ──
const CONFIG = {
    defaultLanguage: "powershell",
    languageOptions: [
        "powershell",
        "javascript",
        "dataview",
        "dataviewjs",
        "css",
        "html",
        "yaml",
        "json",
        "bash",
        "markdown",
        "mermaid",
        "other",
    ],
};

// ── 选择语言 ──
let language =
    await tp.system.suggester(
        CONFIG.languageOptions,
        CONFIG.languageOptions,
        true,
        "Select programming language"
    );

if (!language) {
    return;
}

// ── 自定义语言 ──
if (language === "other") {

    language =
        await tp.system.prompt(
            "Enter the language name:",
            "",
            true
        );

    if (!language || !language.trim()) {
        new Notice("未输入语言名称，已取消。");
        return;
    }

    language = language.trim();
}

// ── 输入代码内容 ──
const content =
    await tp.system.prompt(
        "Enter your code:",
        "",
        true,
        true
    );

if (!content || !content.trim()) {
    new Notice("未输入内容，已取消。");
    return;
}

// ── 输出 Markdown Code Block ──
tR +=
    "```" +
    language +
    "\n" +
    content +
    "\n```";

-%>
