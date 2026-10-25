# Job Application Tracker · 求职进度

一个单文件的求职进度追踪网页：记录每天投递了哪些公司、拿到了哪些 OA 和面试，同时跟踪 **New Grad** 和 **Intern** 两条线，还带**毕业倒计时**和 **F-1 OPT 时间线提醒**。不需要账号和服务器，数据只保存在你自己的浏览器里。

> **English:** A single-file web app for tracking job applications for new-grad and internship recruiting: daily application count, OA / interview pipeline, upcoming deadlines, a graduation countdown, and an F-1 OPT timeline with unemployment-day tracking. No backend, no account — data stays in your browser's localStorage (with JSON export/import). The UI is in Chinese.

## 功能

- **每天投递**：今日投递数和每日目标、近 7 天、日均、连续投递天数。支持单个添加，也支持一次粘贴多行批量添加（`公司, 职位`）。
- **New Grad / Intern 自动区分**：只看职位名，名字里有 intern / internship / co-op / 实习 才算 Intern，其余（包括没写职位名的）都算 New Grad（全职）。批量添加和邮件解析都按这条规则，也可以手动改。
- **进展漏斗**：投递 → OA → 面试 → Offer 的数量和比例，另有暂无回音、OA 过期、已拒、主动撤回。
- **进度图**：点「进度图」，把「投递 → 有回音 / 没回音 → OA → 面试 → Offer / 被拒 / 撤回 / 进行中」的流向画成一张 Sankey 图，可以按类型和投递日期筛选，图里的文字可选英文或中文，保存成 PNG 图片分享。见下文。
- **最近 21 天图表**：每天投递数（New Grad / Intern 分色），OA 和面试出现的日期有标记。
- **待办 OA 和面试**：按截止日期排序，逾期标红；没填日期的 OA 也会列出来提醒你补。OA 过了截止日期还没做的，旁边有「过期没做」：点了它就不再出现在待办里，这条投递的状态变成「OA 过期」（可以按它筛选，漏斗和进度图里也单独算一类）；后来公司又给了新进展，状态会跟着变。点「完成」后会接着问你「接下来怎么样了」：又一轮 OA / 面试、Offer、拒信、撤回，都可以直接记下来；还没消息就选「还没消息」，它会留在下面的「等结果」里，写着已经等了几天，之后随时点「更新进展」补上，不会消失。
- **投递记录**：按投递日期分组，可搜索、按状态和类型筛选，也可以改成「按最近进展」排序，新的拒信、OA、面试排在最前面（选「已拒」「OA」「面试中」等状态时会自动这样排）；每条投递有进展时间线（OA、电话面、技术面、Offer、拒信、撤回）。
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

## 进度图（Sankey）

点页面顶部的「进度图」，选类型（默认跟随页面上的筛选）、投递日期范围和图中文字（English / 中文），预览满意后点「保存图片」，得到一张 2400 × 1400 的 PNG。图和页面里的数据同步生成，不会联网，也不会上传。

每个投递按它的进展走一条路径：

1. 没有任何进展 → **没回音**。
2. 收到过 OA、面试、Offer 或拒信 → **有回音**，再按经过的环节依次走 OA、面试，最后落到 **Offer**（只要拿过 Offer 就算）、**被拒**、**撤回** 或 **进行中**（最后一个进展是 OA 或面试，还没有结果）。
3. 只有「主动撤回」、没有任何其他进展的，从投递直接到撤回，不算有回音。

百分比是占全部投递的比例。图下面的「查看数据表」列出每一段流向的数量，和图里的数字一致。颜色：环节用同一色系由浅到深，Offer 用绿色、被拒用红色，没回音和撤回用灰色。

在 Claude 里运行时，保存图片需要启用 `downloads` 能力，保存前 Claude 会让你确认。如果当前环境不允许下载，页面会把图片显示出来，长按（电脑上右键）图片另存即可。

## 毕业倒计时与 OPT 追踪

在「毕业与 OPT」卡片里填毕业日期（OPT 请填 I-20 上的 *Program End Date*），会显示倒计时。勾选「我是 F-1 学生，开启 OPT 追踪」后，做到哪一步填哪一步的日期，页面会算出每一步的截止日，并在临近时标黄、逾期时标红。

追踪的内容（毕业后的 post-completion OPT）：

| 步骤 | 页面使用的规则 |
|---|---|
| 向 DSO 申请 OPT 推荐 | 最早毕业前 90 天可申请；学校常要求更早，以 DSO 为准 |
| 向 USCIS 递交 I-765 | 最早毕业前 90 天、最晚毕业后 60 天；并且要在 DSO 在 SEVIS 里做 OPT 推荐后 30 天内送达。可以记下 I-765 上申请的开始日期，毕业后 OPT 的开始日期最晚是毕业后 60 天 |
| 加急（Premium Processing） | 可以在递交时、也可以在 I-765 还在处理中时另交 I-907。USCIS 要在 **30 个工作日**内处理，**从 USCIS 收到 I-907 起算**，不是 I-765 的递交日；收到补件通知（RFE）会停表，回复被收到后重新计 30 个工作日；超过期限要退还加急费。页面按「去掉周末和美国联邦假日」估算期限，只是估算。没有加急的显示「普通处理」和已经等了几天，没有固定期限 |
| 指纹 / 拍照（Biometrics） | 不是每个人都有：收到 USCIS 的预约通知（I-797C）才需要去，填上预约日期会倒计时；缺席可能被拒，去不了要在预约日之前改期。USCIS 官网没写加急会不会等指纹，以通知为准 |
| Receipt Notice、获批、EAD 卡 | 记录日期。要**同时**满足三件事才能开始工作：USCIS 已批准、EAD 卡到手、卡上的开始日期已到，页面会写出「现在还不能工作，还差……」。加急只会让审批更快，能不能工作仍要看卡上的开始日期 |
| OPT 到期 | 未填 EAD 结束日时，按开始日期推算 12 个月 |
| 失业天数 | 从 EAD 开始日起算，上限 90 天；STEM 延期获批后合计 150 天 |
| STEM OPT 延期 | 24 个月；最早 OPT 到期前 90 天可递交，必须在到期前递交，并且在 DSO 的 STEM 推荐后 60 天内 |

**这些日期只作提醒，不是法律意见。** 规则会变化，每个学校的流程也不同，请以你学校 DSO 的要求和官方说明为准：

- [Study in the States：Applying for Practical Training](https://studyinthestates.dhs.gov/students/work/applying-for-practical-training)
- [USCIS：How Do I Request Premium Processing?](https://www.uscis.gov/forms/all-forms/how-do-i-request-premium-processing)
- [USCIS：Form I-765](https://www.uscis.gov/i-765)
- [USCIS：STEM OPT 延期](https://www.uscis.gov/working-in-the-united-states/students-and-exchange-visitors/optional-practical-training-extension-for-stem-students-stem-opt)
- [USCIS Policy Manual：Practical Training](https://www.uscis.gov/policy-manual/volume-2-part-f-chapter-5)

换工作或换地址后，别忘了在 10 天内向 DSO 报告。

## 哪些算一次投递

「今天投递」、近 7 天、日均、连续天数、最近 21 天图、进展漏斗和进度图，数的都是**你投出去的申请**。只有两种方式会新增一次投递：

- 用「+ 记录投递」添加（单个或批量）。
- 邮件同步或粘贴邮件时，识别出的「投递确认」邮件。

OA、面试、拒信都是对某次申请的**回复**，不是新的申请，所以不会加到投递数里。**Offer 是例外**：拿到 Offer 说明你一定投过，所以记下 Offer 的记录会自动计入投递数，漏斗和进度图里都会有。

- 它们会先去找对应的投递（公司和职位都对得上），找到就只给它添加一条进展。
- 找不到对应的投递（比如那份申请当时没记录），也会记下来，但标成「不计入投递数」。邮件同步里是这样，手动点「+ 添加待办」给一家新公司加 OA 或面试也是这样。这样的记录放在投递记录最下面的「没有投递记录」里，不进上面那些统计，但仍会出现在「待办 OA 和面试」里提醒你。
- 拒信不会因此消失：没有投递记录的拒信仍然显示在投递记录里（按投递日期排时放在最下面，并有提示；按最近进展排时和其他记录混在一起，职位后面标「无投递记录」），漏斗里的「已拒」也会写出「另有 N 封没有投递记录」。进度图只画计入投递数的记录，没画进去的条数会在图下面的说明里写出来。
- 点完「完成」后记下一步时，如果这家公司没有投递记录，弹窗里有一个「投递日期」，填了就直接计入投递数（选 Offer 时不填也会计入，日期用记录创建的那天，可以之后改）。
- 点进这样的记录，可以勾上「计入投递数」并填真实的投递日期；反过来，任何一条投递也可以取消勾选，不再计入。

## 数据结构

存储是一个简单的文档库，路径形如 `集合/文档`：

```text
apps/<id>         一条投递
  company, role, track ("ng" | "intern"), appliedOn (YYYY-MM-DD),
  link, location, notes, src?, uncounted?, createdAt, updatedAt,
  events: [{ id, kind, date, due?, note?, src?, done, doneOn?, missed? }]
          kind: oa | screen | interview | offer | rejected | withdrawn
          src: 来源邮件 ID（同步邮箱时记录，用来跳过已经处理过的邮件）
          doneOn: 标为完成的日期（「等结果」里算已经等了几天）
          missed: true 表示这条 OA 过了截止日期没做（同时 done=true；最后一个进展是它时状态为「OA 过期」）
          uncounted: true 表示不计入投递数（见下）
meta/mailsync     同步邮箱的进度
  lastSync: 上次「完整」同步的日期（「自上次同步」从这天往后搜）
  skip: 看过、不用再看的邮件 ID（不是求职邮件，或投递确认已经在记录里），最多留最近 600 个
meta/settings     设置
  dailyGoal, gradDate,
  opt: { on, dsoDate, filedDate, reqStart, pp, ppDate, rfeDate, receiptDate, bioDate, bioDone,
         approvedDate, cardDate, startDate, endDate, jobStart, extraDays,
         stemOn, stemDso, stemFiled, stemApproved }
       reqStart: I-765 上申请的开始日期；pp: 是否加急；ppDate: USCIS 收到 I-907 的日期；
       rfeDate: RFE 回复被收到的日期；bioDate / bioDone: 指纹拍照预约日期 / 是否去过；cardDate: 收到 EAD 卡的日期
```

投递的当前状态由最后一个进展推出，不单独存储。导出的备份文件格式是 `{ format: "job-application-tracker", version: 1, apps: [...], settings: {...} }`。

## 在 Claude 里运行

同一个页面也可以作为 claude.ai 的 Artifact 运行：把 `index.html` 交给 Claude，让它发布成 Artifact，并启用 `db`、`sample` 和 `downloads`（保存进度图用）三个能力。页面检测到 `window.claude` 后，会自动把数据存进 Artifact 自己的数据库（页面底部的说明会随之变化），邮件解析也变成一键完成（通过你自己的 Claude 账号）。如果你在 claude.ai 里连接了 Gmail，还会多出「同步邮箱」按钮：页面只读地搜索求职相关的邮件，解析后列出来，你确认才保存（需要给页面读取 Gmail 的权限）。其他情况下自动使用浏览器本地存储。

**同步邮箱怎么往前走：** 一次最多处理 150 封还没处理过的邮件（已经保存过的、看过不是求职邮件的、投递确认已经在记录里的，都不再占名额，搜索会一直翻页直到凑够）。邮件更多时会提示「这次先处理最新的 N 封」，保存后再点一次「开始搜索」会接着处理剩下的。只有这个时间范围里的邮件都处理完了，「自上次同步」的日期才会往前走；没有新邮件、或者没识别出求职邮件，也算处理完。

## 参与

欢迎提 Issue 和 PR。想做的方向：英文界面、日历（.ics）导出、更多国家的签证 / 工作许可时间线。

## License

[MIT](LICENSE)
