```dataviewjs
/*
* ==================================================
* TAGS
* 作者：Kerwin
* 用途：
*   显示全库标签墙
* 对应 CSS：
*   .tags-*
*   .tag-level-*
* 功能：
*   1. 统计全库标签
*   2. 按使用次数降序排列
*   3. 根据使用次数显示颜色等级
*   4. 标签可点击
*   5. 点击后打开 Obsidian 搜索 tag:#xxx
* ==================================================
*/

// ── 配置 ──
const CONFIG = {
  limit: 60,
  excludePath: "Templates/",
};

// ── 统计标签 ──
const tagCounts = {};

dv.pages()
  .where((page) => !String(page.file.path).startsWith(CONFIG.excludePath))
  .forEach((page) => {
    if (!page.file.tags) return;

    page.file.tags.forEach((tag) => {
      const cleanTag = String(tag).replace(/^#/, "");
      tagCounts[cleanTag] = (tagCounts[cleanTag] || 0) + 1;
    });
  });

// ── 排序标签 ──
const sortedTags = Object.entries(tagCounts)
  .sort((a, b) => b[1] - a[1])
  .slice(0, CONFIG.limit);

const maxCount = Math.max(...sortedTags.map((t) => t[1]), 1);

// ── 渲染 ──
const wrapper = dv.container.createEl("div", {
  cls: "tags-panel",
});

const header = wrapper.createEl("div", {
  cls: "tags-panel-header",
});

header.createEl("div", {
  cls: "tags-panel-title",
  text: "Tags",
});

header.createEl("div", {
  cls: "tags-panel-subtitle",
  text: sortedTags.length + " tags",
});

if (sortedTags.length === 0) {
  wrapper.createEl("div", {
    cls: "tags-empty",
    text: "暂无标签",
  });
}

const list = wrapper.createEl("div", {
  cls: "tags-panel-list",
});

sortedTags.forEach(([tag, count]) => {
  const level = Math.ceil((count / maxCount) * 5);

  const item = list.createEl("button", {
    cls: "tags-panel-item tag-level-" + level,
  });

  item.createEl("span", {
    cls: "tags-panel-name",
    text: "#" + tag,
  });

  item.createEl("span", {
    cls: "tags-panel-count",
    text: String(count),
  });

  item.addEventListener("click", () => {
    app.internalPlugins.plugins["global-search"].instance.openGlobalSearch(
      "tag:#" + tag,
    );
  });
});
```
