```dataviewjs
/*
* ==================================================
* DASHBOARD
* 作者：Kerwin
* 用途：
*   统计 Obsidian 全库任务完成情况
* 对应 CSS：
*   .dashboard-*
* 功能：
*   1. 统计全库任务总数、已完成、待完成
*   2. 环形进度条显示完成百分比
*   3. 根据完成率显示状态文案
* 说明：
*   CONFIG.searchScope — 指定扫描目录，空字符串 = 扫描整个库
*   CONFIG.excludePath — 排除目录
* ==================================================
*/

// ── 配置 ──
const CONFIG = {
  searchScope: "",
  excludePath: "Templates/",
};

// ── 读取任务 ──
const pages = CONFIG.searchScope
  ? dv.pages(`"${CONFIG.searchScope}"`)
  : dv.pages();

const tasks = pages.file.tasks.where(
  (t) => !t.path.startsWith(CONFIG.excludePath),
);

// ── 统计数据 ──
const total = tasks.length;
const done = tasks.where((t) => t.completed).length;
const left = total - done;
const pct = total ? (done / total) * 100 : 0;
const pctStr = pct.toFixed(1);

// ── 状态文案 ──
const statusText =
  total === 0
    ? "暂无任务"
    : pct === 100
      ? "全部任务已完成"
      : pct >= 80
        ? "接近完成，保持节奏"
        : pct >= 50
          ? "进度过半，继续推进"
          : pct > 0
            ? "已经开始，逐步推进"
            : "尚未开始";

// ── 环形进度条 ──
const radius = 42;
const circumference = 2 * Math.PI * radius;
const offset = circumference - (pct / 100) * circumference;

// ── HTML 输出 ──
dv.container.innerHTML = `
<div class="dashboard-panel">
    <div class="dashboard-header">
        <div>
            <div class="dashboard-label">TASK DASHBOARD</div>
            <div class="dashboard-title">任务完成度</div>
            <div class="dashboard-status">${statusText}</div>
        </div>
        <div class="dashboard-ring">
            <svg width="120" height="120" viewBox="0 0 120 120">
                <circle cx="60" cy="60" r="${radius}" class="dashboard-ring-bg" />
                <circle
                    cx="60"
                    cy="60"
                    r="${radius}"
                    class="dashboard-ring-progress"
                    stroke-dasharray="${circumference}"
                    stroke-dashoffset="${offset}"
                    transform="rotate(-90 60 60)"
                />
            </svg>
            <div class="dashboard-ring-text">${pctStr}%</div>
        </div>
    </div>
    <div class="dashboard-stats">
        <div class="dashboard-stat-card">
            <div class="dashboard-stat-label">总任务</div>
            <div class="dashboard-stat-value">${total}</div>
        </div>
        <div class="dashboard-stat-card">
            <div class="dashboard-stat-label">已完成</div>
            <div class="dashboard-stat-value">${done}</div>
        </div>
        <div class="dashboard-stat-card">
            <div class="dashboard-stat-label">待完成</div>
            <div class="dashboard-stat-value">${left}</div>
        </div>
    </div>
</div>
`;
```
