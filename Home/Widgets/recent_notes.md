```dataviewjs
/*
* ==================================================
* RECENT NOTES
* 作者：Kerwin
* 用途：
*   显示 Notes 文件夹中最近修改的笔记
* 对应 CSS：
*   .recent-notes-*
*   .recent-note-*
* 功能：
*   1. 显示最近修改的笔记
*   2. 按修改时间倒序排列
*   3. 点击笔记名称可打开对应笔记
*   4. 支持笔记 Frontmatter 自定义 icon
*   5. 显示相对时间，例如 5m ago / 2h ago / 3d ago
* 说明：
*   笔记可选 Frontmatter：
*   ---
*   icon: 📸
*   ---
* ==================================================
*/

// ── 配置 ──
const CONFIG = {
  folder: "Notes",
  limit: 15,
  defaultIcon: "📄",
};

// ── 相对时间格式化 ──
const formatRelativeTime = (date) => {
  if (!date) return "";

  const now = dv.luxon.DateTime.now();
  const diff = now.diff(date, ["days", "hours", "minutes"]).toObject();

  if (diff.days >= 1) {
    return Math.floor(diff.days) + "d ago";
  }

  if (diff.hours >= 1) {
    return Math.floor(diff.hours) + "h ago";
  }

  return Math.max(1, Math.floor(diff.minutes)) + "m ago";
};

// ── 获取最近修改的笔记 ──
const pages = dv
  .pages(`"${CONFIG.folder}"`)
  .sort((p) => p.file.mtime, "desc")
  .slice(0, CONFIG.limit);

// ── 渲染 ──
const wrapper = dv.container.createEl("div", {
  cls: "recent-notes",
});

const header = wrapper.createEl("div", {
  cls: "recent-notes-header",
});

header.createEl("div", {
  cls: "recent-notes-title",
  text: "Recent Notes",
});

header.createEl("div", {
  cls: "recent-notes-count",
  text: pages.length + " notes",
});

if (pages.length === 0) {
  wrapper.createEl("div", {
    cls: "recent-notes-empty",
    text: "暂无笔记",
  });
}

const list = wrapper.createEl("div", {
  cls: "recent-notes-list",
});

pages.forEach((page) => {
  const icon = page.icon || CONFIG.defaultIcon;

  const item = list.createEl("div", {
    cls: "recent-note-item",
  });

  const link = item.createEl("a", {
    cls: "recent-note-title",
    text: icon + " " + page.file.name,
    href: page.file.path,
  });

  link.addEventListener("click", (e) => {
    e.preventDefault();
    app.workspace.openLinkText(page.file.path, "", true);
  });

  item.createEl("div", {
    cls: "recent-note-meta",
    text: formatRelativeTime(page.file.mtime),
  });
});
```
