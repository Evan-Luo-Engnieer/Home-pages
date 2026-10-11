# Mini YouTube 标题预览隐私说明

> 生效日期：2026-10-12（Asia/Singapore）
>
> 当前实时 API 预览尚未启用。只有在本说明及相应条款公开、Mini 的本机政策门控与已发布版本一致、用户明确同意后，才可由用户在自己的 macOS Terminal 手动运行预览。

## 适用范围

本说明仅适用于由 Evan 手动发起的一次只读预览：通过 YouTube Data API v3 检查 Matt Pocock（@mattpocockuk）频道的公开上传视频，并在 Evan 本机终端显示原始标题、发布时间、YouTube 链接、当次列表编号和来源标签。关闭预览屏幕时，程序清屏并退出；预览不会写入日志或本地文件。

实时预览必须由 Evan 在自己的 macOS Terminal 中直接运行，并在输入 API key 前输入 `LOCAL TERMINAL ONLY`，确认预览不会经 Codex task terminal 捕获。若终端无法隐藏 API key 输入，程序会中止而不退回明文输入。不要在 Codex 管理的终端、PTY 或云端任务中运行：程序能检查是否有交互终端，但无法判断该终端是否由任务编排器捕获。通过这类终端运行时，预览画面仍可能被捕获。

此范围不包括定时日报、消息回复、用户选中后读取视频正文、摘要、字幕或转录，也不包括写入 Obsidian 笔记。若增加这些功能，须先更新隐私说明并重新取得同意。

## 处理的信息

为定位和核验公开上传，预览会请求或处理频道 ID、uploads playlist ID、视频 ID、频道名称、原始标题、发布时间及视频当前公开状态。API 请求不请求视频描述、评论、缩略图、观看或订阅统计、字幕、音频或视频内容。

API 响应和预览条目只在运行进程内存中短暂处理，并通过本机终端的临时 alternate screen 展示。应用清除该屏幕后不保证对进程内存进行物理擦除；程序不把预览标题、发布时间、视频 ID、频道 ID 或链接写入该试点数据库、stdout、stderr 或应用日志。

专用本地试点数据库只保存配置的来源标识、由本机应用时钟生成的 UTC 首次扫描基线和最近一次成功扫描时间，以及用户接受的隐私政策版本和时间。预览会拒绝复用含有持久 YouTube 元数据、视频去重 ID 或活动编号映射的状态库。首次基线取发起扫描时的应用 UTC 时间；预览条目的编号只用于当次列表定位，不是持久 ID，YouTube 链接包含稳定的视频 ID。

本预览不读取用户的 YouTube 登录凭据，也不要求 YouTube OAuth 授权。API key 是 Evan 为该 API Client 配置的开发者凭据，不是用户的 YouTube 密码。

## 处理目的、接收方与输出

YouTube/Google 接收发送到 YouTube Data API v3 的请求、请求参数和开发者 API key，以提供所请求的公开频道/视频数据。API key 通过 HTTPS `X-Goog-Api-Key` 请求头发送；本机预览进程仅在运行时使用，不将它写入数据库、命令行、日志或输出。

任务协调器可见的命令输出仅包含完成/失败状态和客户端尝试发起的 API 请求次数；若连接失败，该次数不能证明 YouTube 已收到请求。技术测试报告只包含合成 fixture 的通过/失败结果。不会返回公开视频数量、标题、发布时间、视频或频道 ID、链接、摘要、完整 API JSON、原始异常响应或 API key。当前实现不把 YouTube 元数据发送到 Codex/dot 云端任务或向 Evan 的云端报告中。

如果 Evan 通过 Codex 管理的终端运行实时预览，终端屏幕可能被该任务执行器捕获；因此本流程要求在本机 Terminal 直接运行，任务执行器只接收安全状态摘要。若无法确认本机终端没有向任务回传画面，应停止实时预览。

## 本地保存期限与删除

本机预览进程不持久保存 YouTube API Data；所显示的数据随本地预览关闭而退出显示，不生成预览文件。用户可以对明确指定的试点数据库运行删除命令，以清除其中的扫描状态和同意记录；该操作不会删除 YouTube 上的原视频。

YouTube API Services Developer Policies 第 III.E.4.d 节允许有限的未授权数据仅为功能所需暂存、且不得超过 30 个日历日；到期必须删除或刷新。当前独立的离线 digest state core 另有可持久保存公开元数据和去重视频 ID 的路径：元数据缓存与 ID 去重期限最长为 30 日，到期清除元数据和 ID，并保留不含视频/频道 ID 的编号墓碑。此清理目前是按后续操作惰性触发或由用户手动运行，没有周期清理器；该路径没有用于本机实时预览，也不能在清理时限得到保证前用于真实 API 日报。

一般隐私问题或删除请求可通过 [Home-pages GitHub Issues](https://github.com/Evan-Luo-Engnieer/Home-pages/issues) 联系 Evan。Issues 内容是公开的。请勿在 Issue 中填写 API key、密码、账户凭据、私密个人信息或其他敏感内容。若请求需要私密信息，请先只发起不含敏感细节的联系请求，再通过 Evan 提供的私下渠道处理。涉及 Mini 已保存的数据，Evan 应尽快处理并在收到请求后 7 个日历日内完成；当前预览流程不把 YouTube 元数据持久写入本地数据库或任务报告。

## 本说明页面

本说明作为 Markdown 文件托管在 Evan 的公开 GitHub 仓库 [Home-pages](https://github.com/Evan-Luo-Engnieer/Home-pages) 中。访问该页面时，GitHub 会按 [GitHub General Privacy Statement](https://docs.github.com/en/site-policy/privacy-policies/github-general-privacy-statement) 处理网站访问相关信息。GitHub 对页面访问的处理与 Mini 向 YouTube 发出的 API 请求不同。

## YouTube 条款

Mini 使用 YouTube API Services。请阅读 [YouTube Terms of Service](https://www.youtube.com/t/terms)、[Google Privacy Policy](https://policies.google.com/privacy) 和 [YouTube API Services Developer Policies](https://developers.google.com/youtube/terms/developer-policies)。使用 Mini 的 YouTube API 功能前，用户必须先看到并同意本隐私说明；Mini 自己的使用条款还须说明：使用该功能即表示用户同意受 YouTube Terms of Service 约束。

---

最后更新：2026-10-12（Asia/Singapore）
