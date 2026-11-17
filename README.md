# github-cicd-practice

CI/CD 课程实验一：静态网页自动校验并部署到 GitHub Pages。

- 页面入口：`dist/index.html`（手写静态页，只放演示信息）
- 工作流：`.github/workflows/deploy-pages.yml`
- 发布方式：`peaceiris/actions-gh-pages@v4` → 推到 `gh-pages` 分支
- 线上地址：`https://linsen77.github.io/github-cicd-practice/`

## 一、部署前必须做的两项设置

1. **Settings → Pages → Source**：选 `Deploy from a branch`，分支选 `gh-pages`，目录选 `/ (root)`。
   > 首次推送时 `gh-pages` 分支还不存在，可以先推送代码、等第一次 Action 跑完，再回来设置。
2. **Settings → Actions → General → Workflow permissions**：选 `Read and write permissions`。
   > 不设置会报 `403`，日志里通常出现 `Permission to ... denied` 或 `remote: Permission to ... denied`。

## 二、操作流程

```bash
# 1. 首次推送（仓库已在 GitHub 上创建为空仓库）
git remote add origin https://github.com/Linsen77/github-cicd-practice.git
git push -u origin main
```

3. 打开仓库 **Actions** 页，点开 `Deploy to GitHub Pages` 这次运行：
   - `Validate publish entry` 步骤应打印「校验通过」；
   - `Deploy to gh-pages` 步骤应显示 `Changes committed to gh-pages`；
   - 运行结束后仓库会多出 `gh-pages` 分支。
4. 到 Pages 设置里确认来源（第一次跑完才有 `gh-pages` 分支可选）。
5. 打开 `https://linsen77.github.io/github-cicd-practice/`，确认标题、作者、版本文字都在。
6. **改一处文字**：把 `dist/index.html` 里的「版本一 · 首次自动发布」改成「版本二 · 第二次自动发布」，提交并推送。
7. 刷新 Actions 页面，等新一次运行变绿；再刷新线上网址，确认文字变成「版本二」。
8. **故障演练**：把 `dist/index.html` 临时改名为 `dist/index.htm`（或清空该文件），提交推送。
   - 预期：`Validate publish entry` 变红，`Deploy to gh-pages` **不执行**（灰色 / skipped），线上仍是版本二。
   - 记录这一步的日志截图——这是实验一验收的关键证据。
9. **修复**：把文件名改回 `dist/index.html`，恢复内容，再提交推送，确认重新变绿、线上恢复。

## 三、验证清单（对应报告 5.3）

| 检查项 | 该看到什么 |
| --- | --- |
| 第一次运行状态 | `Deploy to GitHub Pages` 绿色勾，出现 `gh-pages` 分支 |
| Pages 发布来源 | 分支 `gh-pages`，目录 `/ (root)` |
| 页面访问 | 标题、作者、版本文字全部显示，控制台无 404 |
| 再次提交 | 新一次运行自动触发，线上文字随本次提交变化 |
| 故障演练 | 校验步骤失败，部署步骤被跳过，线上未被覆盖 |

## 四、常见坑

- **404**：`gh-pages` 分支没生成 / Pages 来源没选对 / 刚发布要等 1–2 分钟；也确认仓库是 Public（私有仓库 Pages 需付费方案）。
- **403**：Workflow permissions 没改成 Read and write。
- **工作流完全不触发**：`deploy-pages.yml` 不在仓库根目录的 `.github/workflows/` 下，或文件名变成了 `deploy-pages.yml.txt`。
- **页面样式丢失**：`publish_dir` 指向了仓库根目录而不是 `./dist`。
- 不要把这个目录当成 React 项目：手写练习的入口就放在 `dist/`，React + Vite 项目的源码入口才在根目录。
