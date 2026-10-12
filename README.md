# Job Application Tracker · 求职进度

一个单文件的求职进度追踪网页：记录每天投递了哪些公司、拿到了哪些 OA 和面试，同时跟踪 **New Grad** 和 **Intern** 两条线，还带**毕业倒计时**和 **F-1 OPT 时间线提醒**。不需要账号和服务器，数据只保存在你自己的浏览器里。

> **English:** A single-file web app for tracking job applications for new-grad and internship recruiting: daily application count, OA / interview pipeline, upcoming deadlines, a graduation countdown, and an F-1 OPT timeline with unemployment-day tracking. No backend, no account — data stays in your browser's localStorage (with JSON export/import). The UI is in Chinese.

## 功能

- **每天投递**：今日投递数和每日目标、近 7 天、日均、连续投递天数。支持单个添加，也支持一次粘贴多行批量添加（`公司, 职位`）。
- **进展漏斗**：投递 → OA → 面试 → Offer 的数量和比例，另有暂无回音、已拒、主动撤回。
- **最近 21 天图表**：每天投递数（New Grad / Intern 分色），OA 和面试出现的日期有标记。
- **待办 OA 和面试**：按截止日期排序，逾期标红；没填日期的 OA 也会列出来提醒你补。
- **投递记录**：按投递日期分组，可搜索、按状态和类型筛选；每条投递有进展时间线（OA、电话面、技术面、Offer、拒信、撤回）。
- **粘贴邮件解析**：把招聘邮件贴进来，识别公司、职位、邮件类型和日期，确认后再保存。见下文。
- **毕业倒计时 + OPT 追踪**：自己填毕业日期，显示倒计时；F-1 学生可以开启 OPT 追踪。见下文。
- 深色模式，手机宽度可用。

## 怎么用

**直接打开**：下载 `index.html`，用浏览器打开即可。

**发布成网页（GitHub Pages）**：在你的仓库 *Settings → Pages* 里选 *Deploy from a branch*，分支选 `main`、目录选 `/ (root)`，保存后用页面给出的网址访问。每个人打开后看到的是自己浏览器里的数据，互不影响。

## 数据与隐私

- 数据保存在浏览器的 `localStorage`（键名 `job-application-tracker:v1`），**不会上传到任何服务器**。页面唯一的外部请求是 Google Fonts 的字体。
- 换设备、换浏览器、清理浏览器数据都会让数据消失或看不到。页面底部有**导出备份 / 导入备份**（JSON），换设备前请先导出。
- 无痕模式下浏览器可能不允许保存，页面会提示。

## 粘贴邮件解析

页面本身不联网，所以不会替你读邮件，也不会把邮件发给任何人。流程是：

1. 点「粘贴邮件」，贴入邮件（带上发件人和主题更准；多封邮件之间用单独一行 `---` 隔开），或上传 `.eml` / `.txt`。
2. 点「生成提示词并复制」，把提示词发给你常用的 AI（ChatGPT、Claude、Gemini 等）。
3. 把 AI 返回的 JSON 贴回来，点「读取结果」。
4. 页面列出识别结果，你可以改，确认后保存：公司已有投递就添加进展，没有就新建一条。

邮件内容是不可信的输入：页面只把返回结果当作数据，所有字段都会校验和转义，并且保存前一定要你确认。

## 毕业倒计时与 OPT 追踪

在「毕业与 OPT」卡片里填毕业日期（OPT 请填 I-20 上的 *Program End Date*），会显示倒计时。勾选「我是 F-1 学生，开启 OPT 追踪」后，做到哪一步填哪一步的日期，页面会算出每一步的截止日，并在临近时标黄、逾期时标红。

追踪的内容（毕业后的 post-completion OPT）：

| 步骤 | 页面使用的规则 |
|---|---|
| 向 DSO 申请 OPT 推荐 | 最早毕业前 90 天可申请；学校常要求更早，以 DSO 为准 |
| 向 USCIS 递交 I-765 | 最早毕业前 90 天、最晚毕业后 60 天；并且要在 DSO 在 SEVIS 里做 OPT 推荐后 30 天内送达 |
| Receipt Notice、获批、EAD | 记录日期；EAD 开始日期之前不能工作 |
| OPT 到期 | 未填 EAD 结束日时，按开始日期推算 12 个月 |
| 失业天数 | 从 EAD 开始日起算，上限 90 天；STEM 延期获批后合计 150 天 |
| STEM OPT 延期 | 24 个月；最早 OPT 到期前 90 天可递交，必须在到期前递交，并且在 DSO 的 STEM 推荐后 60 天内 |

**这些日期只作提醒，不是法律意见。** 规则会变化，每个学校的流程也不同，请以你学校 DSO 的要求和官方说明为准：

- [Study in the States：Applying for Practical Training](https://studyinthestates.dhs.gov/students/work/applying-for-practical-training)
- [USCIS：STEM OPT 延期](https://www.uscis.gov/working-in-the-united-states/students-and-exchange-visitors/optional-practical-training-extension-for-stem-students-stem-opt)
- [USCIS Policy Manual：Practical Training](https://www.uscis.gov/policy-manual/volume-2-part-f-chapter-5)

换工作或换地址后，别忘了在 10 天内向 DSO 报告。

## 数据结构

存储是一个简单的文档库，路径形如 `集合/文档`：

```text
apps/<id>         一条投递
  company, role, track ("ng" | "intern"), appliedOn (YYYY-MM-DD),
  link, location, notes, createdAt, updatedAt,
  events: [{ id, kind, date, due?, note?, done }]
          kind: oa | screen | interview | offer | rejected | withdrawn
meta/settings     设置
  dailyGoal, gradDate,
  opt: { on, dsoDate, filedDate, receiptDate, approvedDate, startDate, endDate,
         jobStart, extraDays, stemOn, stemDso, stemFiled, stemApproved }
```

投递的当前状态由最后一个进展推出，不单独存储。导出的备份文件格式是 `{ format: "job-application-tracker", version: 1, apps: [...], settings: {...} }`。

## 在 Claude 里运行

同一个页面也可以作为 claude.ai 的 Artifact 运行：把 `index.html` 交给 Claude，让它发布成 Artifact，并启用 `db` 和 `sample` 两个能力。页面检测到 `window.claude` 后，会自动把数据存进 Artifact 自己的数据库（页面底部的说明会随之变化），邮件解析也变成一键完成（通过你自己的 Claude 账号）。其他情况下自动使用浏览器本地存储。

## 参与

欢迎提 Issue 和 PR。想做的方向：英文界面、日历（.ics）导出、更多国家的签证 / 工作许可时间线。

## License

[MIT](LICENSE)
