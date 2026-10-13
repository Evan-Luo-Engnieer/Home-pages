# Mini YouTube 标题预览隐私说明

> 生效日期：2026-10-12（Asia/Singapore）
>
> 政策版本：youtube-pilot-privacy-2026-10-12-v2
>
> 本说明适用于一项单请求只读 API 诊断和一项本机标题预览。两者仅可由 Evan 在自己的 macOS Terminal 手动运行；只有在本说明已公开、本机政策门控与本版本一致且 Evan 已明确接受当前 YouTube Terms 与本说明后，才可发出 API 请求。

## 适用范围

本说明适用于 Evan 手动发起的两类只读功能：

1. **API 连通性诊断。** 调用 YouTube Data API v3 的 `channels.list`，以公开 handle `@mattpocockuk` 请求 `part=id`、`fields=items(id)`。单次诊断最多发送 1 个 API 请求，不自动重试；成功响应中的完整响应只在进程内存中短暂处理后丢弃，不读取或展示具体频道 ID。
2. **标题预览。** 检查 Matt Pocock（@mattpocockuk）频道的公开上传视频，并在 Evan 本机终端显示原始标题、发布时间、YouTube 链接、当次列表编号和来源标签。关闭预览屏幕时，程序清屏并退出；预览不会写入日志或本地文件。

API 诊断和实时预览都必须由 Evan 在自己的 macOS Terminal 中直接运行，并在使用 key 前输入 `LOCAL TERMINAL ONLY`，确认请求及任何本机显示不会经 Codex task terminal 捕获。若文件缺失而终端无法隐藏 API key 输入，程序会中止而不退回明文输入。不要在 Codex 管理的终端、PTY 或云端任务中运行：程序能检查是否有交互终端，但无法判断该终端是否由任务编排器捕获。

此范围不包括定时日报、消息回复、用户选中后读取视频正文、摘要、字幕或转录，也不包括写入 Obsidian 笔记。若增加这些功能，须先更新隐私说明并重新取得同意。

## 处理的信息

连通性诊断只请求 `channels.list` 中的频道 ID 字段，用于确认该只读 API 调用能否完成；完整响应不展示、不写入数据库或日志。标题预览会请求或处理频道 ID、uploads playlist ID、视频 ID、频道名称、原始标题、发布时间及视频当前公开状态。预览 API 请求不请求视频描述、评论、缩略图、观看或订阅统计、字幕、音频或视频内容。

API 响应和预览条目只在运行进程内存中短暂处理，并通过本机终端的临时 alternate screen 展示。应用清除该屏幕后不保证对进程内存进行物理擦除；程序不把预览标题、发布时间、视频 ID、频道 ID 或链接写入该试点数据库、stdout、stderr 或应用日志。

专用本地试点数据库只保存配置的来源标识、由本机应用时钟生成的 UTC 首次扫描基线和最近一次成功扫描时间，以及用户接受的隐私政策版本和时间。预览会拒绝复用含有持久 YouTube 元数据、视频去重 ID 或活动编号映射的状态库。首次基线取发起扫描时的应用 UTC 时间；预览条目的编号只用于当次列表定位，不是持久 ID，YouTube 链接包含稳定的视频 ID。

Mini 不读取用户的 YouTube 登录凭据，也不要求 YouTube OAuth 授权。API key 是 Evan 为该 API Client 配置的开发者凭据，不是用户的 YouTube 密码。Evan 自行将 key 保存于本机固定私有文件 `~/Library/Application Support/Mini-YouTube-Pilot.jIKdeO/private/youtube-pilot.env`，字段名为 `MINI_YOUTUBE_DATA_API_KEY`；目录应为当前用户所有且权限为 `0700`，文件应为当前用户所有且权限为 `0600`。该文件是本机明文凭据文件，权限位不提供加密。Mini 程序只读取这个固定文件，不执行文件内容、不展开 shell 表达式、不读取进程环境变量或 Keychain；缺文件时才退回隐藏输入，文件类型、属主、权限或格式不安全时失败关闭。Mini 不创建、覆盖或删除该文件；文件会保留到 Evan 自行删除。

## 处理目的、接收方与输出

YouTube/Google 接收发送到 YouTube Data API v3 的请求、请求参数和开发者 API key，以提供所请求的公开频道/视频数据。Evan 保存的 key 由 Mini 本机进程读取到内存，并仅在用户手动发起请求时通过 HTTPS `X-Goog-Api-Key` 请求头发送给 Google/YouTube。Mini 不将 key 写入其他文件、数据库、命令行参数、日志、stdout/stderr 或任务云端输出。

`api-diagnose` 的 stdout/stderr 仅显示 `api_diagnose_status`、客户端尝试请求数、静态阶段和错误码；Evan 控制的本机终端还可显示 `api_diagnose_diagnostic`，其字段严格限于请求次数、HTTP 状态（`2xx`、允许列表中的错误码或 `other`）和 Google 错误 reason（精确允许列表值、`none` 或 `unclassified`）。该计数代表客户端尝试次数，不证明 YouTube 已收到请求。标题预览的 stdout/stderr 同样只含安全状态与尝试次数。不会输出频道或视频元数据、链接、摘要、完整 API JSON、原始异常正文或 API key；技术测试报告只含合成 fixture 的通过/失败结果。

如果 Evan 通过 Codex 管理的终端运行实时预览，终端屏幕可能被该任务执行器捕获；因此本流程要求在本机 Terminal 直接运行，任务执行器只接收安全状态摘要。若无法确认本机终端没有向任务回传画面，应停止实时预览。

## 本地保存期限与删除

本机 API 诊断不持久保存 YouTube API Data；诊断响应只在进程内存中处理后丢弃，不生成文件或更改试点数据库。标题预览数据随本地预览关闭而退出显示，不生成预览文件。用户可以对明确指定的试点数据库运行删除命令，以清除其中的扫描状态和同意记录；该操作不会删除 YouTube 上的原视频，也不会删除单独保存的本机 key 文件。Evan 不再使用该 API Client 时，应自行删除 key 文件并在 Google Cloud Console 撤销 key。

YouTube API Services Developer Policies 第 III.E.4.d 节允许有限的未授权数据仅为功能所需暂存、且不得超过 30 个日历日；到期必须删除或刷新。当前独立的离线 digest state core 另有可持久保存公开元数据和去重视频 ID 的路径：元数据缓存与 ID 去重期限最长为 30 日，到期清除元数据和 ID，并保留不含视频/频道 ID 的编号墓碑。此清理目前是按后续操作惰性触发或由用户手动运行，没有周期清理器；该路径没有用于本机实时预览，也不能在清理时限得到保证前用于真实 API 日报。

一般隐私问题或删除请求可通过 [Home-pages GitHub Issues](https://github.com/Evan-Luo-Engnieer/Home-pages/issues) 联系 Evan。Issues 内容是公开的。请勿在 Issue 中填写 API key、密码、账户凭据、私密个人信息或其他敏感内容。若请求需要私密信息，请先只发起不含敏感细节的联系请求，再通过 Evan 提供的私下渠道处理。涉及 Mini 已保存的数据，Evan 应尽快处理并在收到请求后 7 个日历日内完成；当前预览流程不把 YouTube 元数据持久写入本地数据库或任务报告。

## 本说明页面

本说明作为 Markdown 文件托管在 Evan 的公开 GitHub 仓库 [Home-pages](https://github.com/Evan-Luo-Engnieer/Home-pages) 中。访问该页面时，GitHub 会按 [GitHub General Privacy Statement](https://docs.github.com/en/site-policy/privacy-policies/github-general-privacy-statement) 处理网站访问相关信息。GitHub 对页面访问的处理与 Mini 向 YouTube 发出的 API 请求不同。

## YouTube 条款

Mini 使用 YouTube API Services。请阅读 [YouTube Terms of Service](https://www.youtube.com/t/terms)、[Google Privacy Policy](https://policies.google.com/privacy) 和 [YouTube API Services Developer Policies](https://developers.google.com/youtube/terms/developer-policies)。使用 Mini 的 YouTube API 功能前，用户必须先看到并同意本隐私说明；Mini 自己的使用条款还须说明：使用该功能即表示用户同意受 YouTube Terms of Service 约束。

---

最后更新：2026-10-12（Asia/Singapore），版本 `youtube-pilot-privacy-2026-10-12-v2`


---

# Mini YouTube Agent 单次状态试点隐私说明（v3）

政策版本：`youtube-pilot-agent-privacy-2026-10-13-v3`。

本段说明 Agent 非交互运行一次公开元数据样本的处理规则。上面的 v2 手动模式说明继续保留；已有 v2 同意不自动覆盖本段。公开本说明不代表用户已接受，也不代表本机功能已经启用。

## 适用范围与用户控制

用户先阅读本说明和 YouTube Terms，分别确认接受，再明确登记一次 Matt Pocock（@mattpocockuk）首个页面样本许可。用户可以把许可 ID 交给 Agent 调用本机程序；执行时不要求再次打开交互终端。

本功能只验证一次有界的公开元数据请求及响应，不是完整增量扫描、日报、持续监控、视频内容整理或素材笔记功能。许可不包含定时启用或向其他渠道投递视频信息。

## 请求边界与处理的信息

每次许可最多调用 `channels.list` 一次、`playlistItems.list` 一次，以及候选非空时的 `videos.list` 一次，总计最多 3 次请求尝试、50 个候选。只读取第一个页面，不翻页、不拆批、不自动重试；下一页标记仅说明样本不完整。

程序处理公开频道信息、uploads playlist、视频和频道 ID、原始标题、发布时间及当前公开状态，仅用于来源、候选完整性和公开状态校验。历史样本可以包含在内；成功状态不表示完整每日新增列表已验证。

不请求视频描述、摘要、评论、统计、字幕、缩略图、音频或视频内容。公开元数据只在运行进程内存中短暂处理后丢弃，不显示给用户终端或模型，不保存到内容文件、数据库、扫描水位、缓存、去重记录或编号映射。应用退出不保证对进程内存进行物理擦除。

## 凭据、接收方与输出

用户自行在 Google Cloud 管理专用 API key，并保管本机私有凭据文件。本机程序仅内部读取固定凭据来源；不执行文件内容，不接受命令参数中的 key 或任意凭据路径覆盖。缺少凭据或安全检查失败时直接停止，不提示输入。凭据文件是本机明文存储，文件权限不是加密；本功能不创建、覆盖或删除 key。

Google/YouTube 接收请求参数和通过 HTTPS 请求头发送的开发者 API key，用于认证并提供公开元数据。key 不进入模型输入、命令行参数、程序输出或应用日志。Google 的处理适用其隐私政策和服务条款。

Agent 和任务系统仅收到固定的安全 JSON：完成或失败状态、客户端请求尝试数、执行阶段、静态错误码、允许列表中的 HTTP 状态类别及错误原因。未知错误原因只返回固定分类。不返回视频数量、标题、时间、ID、链接、请求头、原始异常或响应正文。请求尝试数不能证明 Google 已收到请求。

任务客户端可能保存这份安全回执，其保存期限和访问权限由该客户端管理，本功能不设定或承诺具体期限，也不额外建立内容日志或向其他渠道发送视频信息。

## 一次许可账本与保存

本机私有批准账本保存随机许可 ID、记录版本、固定范围、政策版本、Terms/Privacy 接受标志、批准和到期时间、一次执行／3 请求／50 候选预算及消费时间，不保存 key 或 API 元数据。

许可有效期为 15 分钟。程序在读取凭据或调用 API 前原子消费许可；正常入口中的并发调用最多一个可消费。凭据失败、HTTP 错误、进程崩溃或结果未知也不会恢复许可，后续执行需要用户重新批准。

已消费记录保留作防重放证据；当前没有自动删除任务或固定删除期限。用户可以停止登记新许可。删除整个账本会使其中现存许可失效，不会自动恢复旧许可，也不会删除 Google 上的内容或撤销 API key。

该账本与原手动试点状态分离。Agent 入口不读取或修改原扫描基线、水位、API 缓存、编号映射或 Obsidian。本机账户和文件权限是账本的信任基础，不能隔离同一操作系统用户下任意代码或文件修改。

## 停止条件与手动模式

许可缺失、格式错误、过期、已消费、范围或版本不符时拒绝；认证、网络、响应格式、大小、候选上限、频道或 ID 一致性校验失败时立即停止，不重试。当前公开状态不满足的条目不进入内存样本结果。

手动 `sample` 仍采用 v2 和本人终端确认，并在本机短暂展示标题；`agent-sample` 使用本段的新许可，自动读取固定凭据，仅返回安全状态。用户未接受本说明或未登记有效许可时，Agent 入口不读取凭据。

15 分钟许可、3 请求／50 候选、禁止翻页和重试、仅返回安全状态，都是本项目的试点控制，不是 YouTube 要求每次进行人工终端确认或只允许这些请求。

## 条款、联系与页面托管

Mini 使用 YouTube API Services。使用本功能前，请阅读并接受本说明及 [YouTube Terms of Service](https://www.youtube.com/t/terms)；使用本功能即表示同意受 YouTube Terms 约束。相关数据处理规则见 [Google Privacy Policy](https://policies.google.com/privacy) 和 [YouTube API Services Developer Policies](https://developers.google.com/youtube/terms/developer-policies)。

一般隐私问题可通过 [Home-pages GitHub Issues](https://github.com/Evan-Luo-Engnieer/Home-pages/issues) 联系。Issues 是公开的，请勿填写凭据、私密个人信息或其他敏感内容；需要私下处理时，先只提交不含敏感细节的联系请求。

本说明继续托管在现有公开 GitHub 文件页，页面访问适用 [GitHub General Privacy Statement](https://docs.github.com/en/site-policy/privacy-policies/github-general-privacy-statement)。本次告知没有建立新的公开执行服务。
