```dataviewjs
/*
* ==================================================
* TASK
* 作者：Kerwin
* 用途：
*   显示 Obsidian 任务列表
* 对应 CSS：
*   .task-*
* 功能：
*   1. Open / All 切换
*   2. 按任务 due 日期的年月分组
*   3. 排除 Templates 目录
*   4. 保留 Dataview 原生任务列表交互
* ==================================================
*/

// ── 配置 ──
const CONFIG = {
  excludePath: "Templates/",
};

// ── 按钮组 ──
const createToggle = (container, showCompleted, onChange) => {
  const switchContainer = container.createEl("div", {
    cls: "task-switch",
  });

  const btnOpen = switchContainer.createEl("button", {
    cls: "task-btn" + (!showCompleted ? " active" : ""),
    text: "Open",
  });

  const btnAll = switchContainer.createEl("button", {
    cls: "task-btn" + (showCompleted ? " active" : ""),
    text: "All",
  });

  btnOpen.addEventListener("click", () => onChange(false));
  btnAll.addEventListener("click", () => onChange(true));
};

// ── 分组排序 ──
const groupAndSortTasks = (tasks) => {
  const groupByYearMonth = (task) => {
    if (!task.due) return "No Due Date";

    const date = task.due?.isValid === true ? task.due : dv.date(task.due);
    if (!date || !date.isValid) return "No Due Date";

    return date.year + "-" + date.month.toString().padStart(2, "0");
  };

  return tasks
    .groupBy(groupByYearMonth)
    .sort((g) => g.key, "desc")
    .map((group) => ({
      key: group.key,
      displayDate:
        group.key === "No Due Date"
          ? "📝 No Due Date"
          : "📅 " + dv.date(group.key + "-01").toFormat("yyyy MMMM"),
      tasks: group.rows.sort((t) => t.due, "desc"),
    }));
};

// ── 渲染 ──
const renderTasks = (showCompleted) => {
  dv.container.empty();

  const top = dv.container.createEl("div", {
    cls: "task-panel-top",
  });

  top.createEl("div", {
    cls: "task-panel-title",
    text: "Tasks",
  });

  createToggle(top, showCompleted, (val) => renderTasks(val));

  let allTasks = dv
    .pages()
    .where(
      (p) =>
        p.file.tasks && !String(p.file.path).startsWith(CONFIG.excludePath),
    ).file.tasks;

  if (!showCompleted) {
    allTasks = allTasks.where((t) => !t.completed);
  }

  const groupedTasks = groupAndSortTasks(allTasks);

  if (groupedTasks.length === 0) {
    dv.container.createEl("div", {
      cls: "task-empty",
      text: showCompleted ? "暂无任务" : "没有未完成任务",
    });
    return;
  }

  groupedTasks.forEach((group) => {
    const header = dv.container.createEl("div", {
      cls: "task-group-title",
    });

    header.createEl("span", {
      text: group.displayDate,
    });

    header.createEl("span", {
      cls: "task-group-count",
      text: group.tasks.length,
    });

    dv.taskList(group.tasks, false);
  });
};

renderTasks(false);
```
