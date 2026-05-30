---
tags: 
status: <% tp.system.suggester(["✘ To-do","♦ In Progress","✔︎ Completed"],["✘ To-do","♦ In Progress","✔︎ Completed"],'status')%>
due: <% tp.date.now() %>
location: 
---


```dataviewjs
/* PROJECT TASKS — Kerwin | CSS: .project-progress-*
 * 用途：当前项目任务与进度（进度条、任务列表）
 * 规则：任务正文含 [[当前项目名称]] */

const projectName = dv.current().file.name;

const tasks = dv.pages()
    .file.tasks
    .where(t =>
        !t.path.startsWith("Templates/") &&
        t.text.includes("[[" + projectName + "]]")
    );

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

## 设备登陆 

| 设备 & 平台 | 访问方式 | IP 地址/域名 | 内网端口 | 外网端口 | 账号  | 密码  |
| :--------- | :----- | :---------- | :------ | :------ | :--- | :--- |
|            |        |             |         |         |      |      |

## 设备互联

| 设备 | 本端端口 | 本端 IP | 对端设备 | 对端端口 | 对端 IP | 所属 VLAN |
| :--- | :----- | :----- | :------ | :------ | :----- | :------ |
|      |        |        |         |         |        |         |

## 网络规划

| VLAN-NAME | VLAN-ID | Get | IP Address | Gateway | CIDR |
| :-------- | :------ | :-- | :--------- | :------ | :--- |
|           |         |     |            |         |      |

## 网络拓扑

## 项目资料