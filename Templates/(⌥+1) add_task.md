<%*
/*
* ==================================================
* CREATE TASK TEMPLATE
* 作者：Kerwin
* 用途：
*   快速创建任务，并追加到指定任务文件
* 功能：
*   1. 输入任务名称
*   2. 输入截止日期
*   3. 可选择关联项目
*   4. 自动追加到 Home/Tasks.md
*   5. 任务格式统一为：
*      - [ ] 📅 YYYY-MM-DD | [[Project]] | Task Name
* 说明：
*   如果不选择项目，则格式为：
*   - [ ] 📅 YYYY-MM-DD | Task Name
* ==================================================
*/

// ── 配置 ──
const CONFIG = {
    taskFilePath: "Home/Tasks.md",
    projectsDir: "Projects",
    dateFormat: "YYYY-MM-DD",
};

// ── 工具函数 ──
const Utils = {

    getAllFiles: (folder) => {
        return folder.children.reduce((files, child) => {
            if (child instanceof tp.obsidian.TFile) {
                files.push(child);
            }

            if (child instanceof tp.obsidian.TFolder) {
                files.push(...Utils.getAllFiles(child));
            }

            return files;
        }, []);
    },

    getFileFromDirectory: async (directoryPath, promptMessage) => {
        const folder =
            app.vault.getAbstractFileByPath(directoryPath);

        if (!folder || !(folder instanceof tp.obsidian.TFolder)) {
            new Notice("目录不存在：" + directoryPath);
            return undefined;
        }

        const allFiles =
            Utils.getAllFiles(folder);

        if (allFiles.length === 0) {
            new Notice("目录中没有文件：" + directoryPath);
            return undefined;
        }

        const fileNames =
            allFiles.map(file => file.basename);

        const displayItems = [
            "（不关联项目）",
            ...fileNames.map(name => "📁 " + name),
        ];

        const values = [
            null,
            ...fileNames,
        ];

        const selected = await tp.system.suggester(
            displayItems,
            values,
            false,
            promptMessage
        );

        return selected;
    },

    appendToFile: async (filePath, content) => {
        const file =
            app.vault.getAbstractFileByPath(filePath);

        if (!(file instanceof tp.obsidian.TFile)) {
            return false;
        }

        const current =
            await app.vault.read(file);

        const separator =
            current.endsWith("\n") ? "" : "\n";

        await app.vault.modify(
            file,
            current + separator + content + "\n"
        );

        return true;
    },
};

// ── 主函数 ──
async function createAndAppendTask() {

    // ── 输入任务名称 ──
    const taskName =
        await tp.system.prompt(
            "任务名称：",
            "",
            false
        );

    if (!taskName || taskName.trim() === "") {
        new Notice("未输入任务名称，已取消。");
        return;
    }

    // ── 输入截止日期 ──
    const dueDate =
        await tp.system.prompt(
            "截止日期：",
            tp.date.now(CONFIG.dateFormat),
            true
        );

    if (dueDate === null) {
        new Notice("已取消。");
        return;
    }

    // ── 选择关联项目 ──
    const projectName =
        await Utils.getFileFromDirectory(
            CONFIG.projectsDir,
            "选择关联的项目文件："
        );

    if (projectName === undefined) {
        new Notice("已取消。");
        return;
    }

    // ── 构建任务内容 ──
    const projectPart =
        projectName
            ? "[[" + projectName + "]]"
            : "";

    const parts = [
        "- [ ] 📅 " + dueDate,
        projectPart,
        taskName.trim(),
    ].filter(Boolean);

    const taskContent =
        parts.join(" | ");

    // ── 写入任务文件 ──
    const success =
        await Utils.appendToFile(
            CONFIG.taskFilePath,
            taskContent
        );

    if (success) {
        new Notice("✅ 任务已追加到 " + CONFIG.taskFilePath);
    } else {
        new Notice("❌ 追加失败，文件不存在：" + CONFIG.taskFilePath);
    }
}

await createAndAppendTask();

return "";
-%>
