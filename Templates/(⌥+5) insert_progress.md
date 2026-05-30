```dataviewjs
/*
* ==================================================
* PROJECT TASKS
* 作者：Kerwin
* 用途：
*   显示当前 Project 的专属任务和进度
* 对应 CSS：
*   .project-progress-*
* 功能：
*   1. 自动统计当前项目任务
*   2. 自动计算完成率
*   3. 显示 Progress Bar
*   4. 显示完成 / 未完成统计
*   5. 显示项目任务列表
*   6. 保留 Dataview 原生 Task 交互
* 说明：
*   识别规则：任务内容中包含 [[当前项目名称]]
* ==================================================
*/

// ── 当前项目 ──
const projectName = dv.current().file.name;

// ── 读取任务 ──
const tasks = dv.pages()
    .file.tasks
    .where(t =>
        !t.path.startsWith("Templates/") &&
        t.text.includes("[[" + projectName + "]]")
    );

// ── 统计与输出 ──
const total = tasks.length;
const done = tasks.where(t => t.completed).length;
const left = total - done;
const pct = total ? done / total * 100 : 0;
const pctStr = pct.toFixed(1);

dv.paragraph(`
<div class="project-progress">
  <div class="project-progress-header">
    <div class="project-progress-title">Project Progress</div>
    <div class="project-progress-percent">${pctStr}%</div>
  </div>
  <div class="project-progress-bar">
    <div class="project-progress-fill" style="width:${pctStr}%"></div>
  </div>
  <div class="project-progress-meta">
    已完成 ${done} · 待完成 ${left} · 总任务 ${total}
  </div>
</div>
`);

dv.header(3, "Tasks");

if (tasks.length === 0) {
    dv.paragraph("暂无关联任务");
} else {
    dv.taskList(tasks.sort(t => t.due, "asc"), false);
}
```
