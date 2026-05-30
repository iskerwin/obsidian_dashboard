```dataviewjs
/*
* ==================================================
* PROJECTS
* 作者：Kerwin
* 用途：
*   显示 Projects 文件夹下的项目列表
* 对应 CSS：
*   .projects-*
*   .project-*
* 功能：
*   1. 读取 Projects 文件夹下的所有项目笔记
*   2. 显示项目名称、状态、截止日期
*   3. 按截止日期排序
*   4. 点击项目名称可打开对应笔记
*   5. 支持项目笔记 Frontmatter 自定义 icon
* 说明：
*   项目笔记建议 Frontmatter：
*   ---
*   icon: 🏢
*   status: 进行中
*   due: 2026-06-01
*   ---
* ==================================================
*/

// ── 配置 ──
const CONFIG = {
  folder: "Projects",
  limit: 20,
  defaultStatus: "进行中",
  defaultIcon: "📁",
};

// ── 日期格式化 ──
const formatDate = (date) => {
  if (!date) return "无截止日期";

  const d = date?.isValid === true ? date : dv.date(date);

  if (!d || !d.isValid) return "无截止日期";

  return d.toFormat("yyyy-MM-dd");
};

// ── 状态转 CSS class ──
const getStatusClass = (status) => {
  if (!status) return "project-status-active";

  const s = String(status).toLowerCase();

  if (s.includes("完成") || s.includes("done")) {
    return "project-status-done";
  }

  if (s.includes("等待") || s.includes("pending") || s.includes("wait")) {
    return "project-status-waiting";
  }

  if (s.includes("暂停") || s.includes("hold")) {
    return "project-status-paused";
  }

  return "project-status-active";
};

// ── 获取项目数据 ──
const projects = dv
  .pages(`"${CONFIG.folder}"`)
  .sort((p) => p.due ?? dv.date("9999-12-31"), "asc")
  .slice(0, CONFIG.limit);

// ── 渲染 ──
const wrapper = dv.container.createEl("div", {
  cls: "projects-panel",
});

const header = wrapper.createEl("div", {
  cls: "projects-panel-header",
});

header.createEl("div", {
  cls: "projects-panel-title",
  text: "Projects",
});

header.createEl("div", {
  cls: "projects-panel-count",
  text: projects.length + " projects",
});

if (projects.length === 0) {
  wrapper.createEl("div", {
    cls: "projects-empty",
    text: "暂无项目",
  });
}

const list = wrapper.createEl("div", {
  cls: "projects-list",
});

projects.forEach((project) => {
  const status = project.status || CONFIG.defaultStatus;
  const dueText = formatDate(project.due);
  const icon = project.icon || CONFIG.defaultIcon;

  const item = list.createEl("div", {
    cls: "project-item",
  });

  const link = item.createEl("a", {
    cls: "project-name",
    text: icon + " " + project.file.name,
    href: project.file.path,
  });

  link.addEventListener("click", (e) => {
    e.preventDefault();
    app.workspace.openLinkText(project.file.path, "", true);
  });

  item.createEl("span", {
    cls: "project-status " + getStatusClass(status),
    text: status,
  });

  item.createEl("span", {
    cls: "project-due",
    text: dueText,
  });
});
```
