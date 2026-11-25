# Antigravity conversation

## User

可以看到我的 AI 订阅有很多账号， /using-superpowers 但是我现在需要频繁切换账号很影响我的开发效率，虽然 cc-switch 的故障路由功能很不错但是仍然需要我手动登陆切换账号。我觉得我需要一个工具，我先在这个工具上登陆我所有的 gpt 账号以及 gemini 账号，在我用 codex 以及 agy cli 时能够快速切换

## Activity

    read_file: /Users/a86198/.agents/skills/using-superpowers/SKILL.md
    read_file: /Users/a86198/.agents/skills/brainstorming/SKILL.md
    read_file: /Users/a86198/.gemini/antigravity-cli/builtin/skills/antigravity_guide/SKILL.md
    run_command: which codex agy cc-switch; codex --help 2>&1 | head -n 40; echo "---AGY---"; agy --help 2>&1 | head -n 60
    update_steps({"steps": [{"status": "in_progress", "title": "调研本地 CLI 认证存储与项目环境"}, {"status": "pending", "title": "通过提问明确工具形态与切换需求"}, {"status": "pending", "title": "对比技术方...)
    read_file: /Users/a86198/.gemini/antigravity-cli/builtin/skills/antigravity_guide/references/cli.md
    run_command: ls -la ~/.codex ~/.gemini ~/.gemini/antigravity-cli 2>/dev/null; ls -d /Applications/*[Ss]witch* ~/.cc* ~/Library/Application\ Support/*cc-switch* ~/Library/Application\ Support/*Switch* 2>/dev/null; codex login --help 2>&1; ls -d ~/Projects ~/Code ~/dev ~/workspace ~/github ~/git ~/src ~/work ~/*project* 2>/dev/null; ls -la ~ | grep '^d' | grep -v '^\.'
    read_file: /Users/a86198/.gemini/antigravity-cli/brain/08ca8d93-08e9-4829-8efc-d52e1201261e/.system_generated/steps/9/output.txt
    run_command: ls -la ~/.gemini/config ~/.cc-switch ~/.antigravity_cockpit 2>/dev/null; echo "---DOC---"; curl -sL https://antigravity.google/docs/cli/reference.md | head -n 180
    read_file: /Users/a86198/.gemini/antigravity-cli/brain/08ca8d93-08e9-4829-8efc-d52e1201261e/.system_generated/steps/15/output.txt
    run_command: ls -1 /Applications ~/Applications 2>/dev/null | grep -iE 'antigravity|cockpit|switch|codex|claude|gemini'; sqlite3 ~/.cc-switch/cc-switch.db ".tables" 2>/dev/null
    run_command: curl -sL https://antigravity.google/llms.txt; echo "---GETTING-STARTED---"; curl -sL https://antigravity.google/docs/getting-started.md | head -n 120; echo "---CODEX-DEBUG---"; codex debug --help 2>&1
    run_command: curl -sL https://antigravity.google/docs/install.md; echo "---CODEX-REPO---"; ls -la ~/codex 2>/dev/null | head -n 30

## Assistant

正在拉取官方文档以确认认证与凭据存储机制，稍等命令完成后继续。

## Activity

    read_file: /Users/a86198/.gemini/antigravity-cli/brain/08ca8d93-08e9-4829-8efc-d52e1201261e/.system_generated/steps/23/output.txt
    run_command: ls -la ~/codex/docs; grep -rn "CODEX_HOME" ~/codex/docs 2>/dev/null | head -n 30
    run_command: find ~/codex/codex-rs -maxdepth 4 -name "*auth*" 2>/dev/null; ls -la ~/.gemini/antigravity-cli/implicit ~/.gemini/antigravity-cli/presence 2>/dev/null
    run_command: ls -la ~/codex/codex-rs/login/src ~/codex/codex-rs/login/src/auth
    read_file: /Users/a86198/codex/codex-rs/login/src/auth/storage.rs
    read_file: /Users/a86198/.agents/skills/brainstorming/visual-companion.md
    run_command: curl -sL --max-time 6 https://antigravity.google/docs/settings.md | head -n 140
    update_steps({"steps": [{"status": "done", "title": "调研本地 CLI 认证存储与项目环境"}, {"status": "in_progress", "title": "通过提问明确工具形态与切换需求"}, {"status": "pending", "title": "对比技术方案并确...)
    ask_question({"questions": [{"is_multi_select": false, "options": ["(Recommended) 纯终端 CLI + 交互式 TUI 选择器（不离开终端即可一键切号或快捷键上下选号）", "macOS 顶部菜单栏应用（Menu Bar）+ CLI 命令行双端联动", "本地...)
    run_command: grep -n -C 8 "refresh" ~/codex/codex-rs/login/src/auth/manager.rs | head -n 90
    read_file: /Users/a86198/codex/docs/authentication.md
    read_file: /Users/a86198/codex/docs/config.md
    read_file: /Users/a86198/.gemini/antigravity-cli/brain/08ca8d93-08e9-4829-8efc-d52e1201261e/.system_generated/steps/21/output.txt

## User

如图

## Activity

    ask_question({"questions": [{"is_multi_select": false, "options": ["(Recommended) 纯净防风控模式：只管多账号官方凭证隔离与秒切，零后台请求（用量直接进 CLI 内看 /usage）", "按需手动模式：平时零后台轮询，仅在你手动触发时通过官方 CLI 命令查...)

## Assistant

「提前存好凭证、直接切换」**这件事本身风控风险很低**，但像部分第三方工具那样的实现细节里确实藏着 **3 个容易踩雷的风控/掉线点**：

1. **后台轮询未公开配额接口（中高风控风险 — 最需警惕）**
   - 你截图里 Cockpit Tools 展示的 `5h 100% / Weekly 43%`，本质是工具自己拿你的 OAuth Token 在后台请求未公开的内部接口查额度，甚至用第三方提取的 `client_id` 自己刷新 Token。
   - **Google（Antigravity / Gemini）** 近期对非官方客户端调用内部接口查得很严（易 403 或限制账号）；**OpenAI** 侧你第一张图里又是连号的 ProtonMail 邮箱（`3506456886@...`、`...861@...`），如果同一个非官方程序在后台同时高频轮询这批账号，很容易把多个账号关联风控。
2. **Refresh Token 重放冲突（极易强制掉线 / 触发安全告警）**
   - 从你本地 Codex 源码 [manager.rs](file:///Users/a86198/codex/codex-rs/login/src/auth/manager.rs#L87-L92) 和 [storage.rs](file:///Users/a86198/codex/codex-rs/login/src/auth/storage.rs#L33-L48) 可以看到，OpenAI 启用了严格的 **Refresh Token 单次轮换与重放检测（Reuse Detection）**。
   - 如果切号工具只是“备份登录时的旧 `auth.json` 再直接覆盖回去”，一旦使用期间 CLI 自动刷新过 Token 而没回写保存（或面板与终端同时刷新），旧 `refresh_token` 再次提交会立即触发 `refresh_token was already used`，整条登录链直接作废，频繁触发还会被判定为凭证被盗。
3. **IP 节点跳变（常规风控点）**
   - 切号瞬间如果顺带切换了不同地区或不纯净的代理节点，容易触发异地验证；只要**同一台 Mac + 固定纯净节点 + 串行使用官方原版 CLI**，和浏览器多开 Profile 一样安全。

**安全结论**：只要我们做到 **① 100% 走原版 `codex` / `agy` 官方登录流程落盘凭证、② 用独立 Profile 目录隔离（或切出前原子回写最新凭证）防 Token 重放、③ 不在后台偷偷轮询内部接口**，就能兼顾「存好凭证直接秒切」与「近乎零额外风控风险」。

---

基于这个安全边界，我们先确认你对**防风控与额度查看**的取舍：

## User

我要不还是先用 cockpit tools 吧，但是为什么我登陆失败？

## Activity

    update_steps({"steps": [{"status": "done", "title": "调研本地 CLI 认证存储与项目环境"}, {"status": "in_progress", "title": "排查 Cockpit Tools 授权失败根因"}, {"status": "pending", "title": "...)
    read_file: /Users/a86198/.agents/skills/systematic-debugging/SKILL.md
    run_command: ls -lt ~/.antigravity_cockpit/logs; tail -n 50 ~/.antigravity_cockpit/logs/* 2>/dev/null | tail -n 80; echo "---PROXY---"; scutil --proxy; echo "---COCKPIT-CONFIG-KEYS---"; python3 -c 'import json, os; p=os.path.expanduser("~/.antigravity_cockpit/config.json"); d=json.load(open(p)); print({k: v for k, v in d.items() if "key" not in k.lower() and "token" not in k.lower() and "secret" not in k.lower()})' 2>/dev/null
    read_file: /Users/a86198/.gemini/antigravity-cli/brain/08ca8d93-08e9-4829-8efc-d52e1201261e/.system_generated/steps/59/output.txt
    run_command: env | grep -i proxy; dscacheutil -q host -a name oauth2.googleapis.com; dscacheutil -q host -a name www.googleapis.com; dscacheutil -q host -a name chatgpt.com; curl -I -sS -w "DIRECT: code=%{http_code} ip=%{remote_ip} time=%{time_total}\n" --noproxy '*' --max-time 5 https://oauth2.googleapis.com/token -o /dev/null 2>&1 || true; curl -I -sS -w "DEFAULT: code=%{http_code} ip=%{remote_ip} time=%{time_total}\n" --max-time 5 https://oauth2.googleapis.com/token -o /dev/null 2>&1 || true
    run_command: grep -iE "proxy|oauth|google|error|warn|antigravity_cli|agy" ~/.antigravity_cockpit/logs/app.log.2026-11-25 | tail -n 50; echo "---TEST-GOOGLEAPIS---"; for url in "https://oauth2.googleapis.com/token" "https://www.googleapis.com/oauth2/v2/userinfo"; do echo "Testing $url"; curl -sS -o /dev/null -w "  TUN(noproxy): code=%{http_code} ip=%{remote_ip} dns=%{time_namelookup} tcp=%{time_connect} tls=%{time_appconnect} total=%{time_total}\n" --noproxy '*' --max-time 8 "$url" 2>&1 || true; curl -sS -o /dev/null -w "  PROXY(7897):  code=%{http_code} ip=%{remote_ip} dns=%{time_namelookup} tcp=%{time_connect} tls=%{time_appconnect} total=%{time_total}\n" -x http://127.0.0.1:7897 --max-time 8 "$url" 2>&1 || true; done

## Assistant

正在检测本地代理与谷歌认证接口的网络连通性，稍等测试完成后汇总结果。

## Activity

    run_command: lsof -nP -iTCP:7897 -sTCP:LISTEN 2>/dev/null; ls -la ~/.cockpit_tools 2>/dev/null
    update_steps({"steps": [{"status": "done", "title": "调研本地 CLI 认证存储与项目环境"}, {"status": "done", "title": "排查 Cockpit Tools 授权失败根因"}, {"status": "done", "title": "验证网络代理配置与 ...)

## Assistant

结合 Cockpit Tools 日志 [app.log.2026-11-25](file:///Users/a86198/.antigravity_cockpit/logs/app.log.2026-11-25)、配置文件 [config.json](file:///Users/a86198/.antigravity_cockpit/config.json) 和实测网络请求，已经定位到明确根因：

### 为什么浏览器显示「✅ 授权成功」，但应用里报错？

整个登录分两步：
1. **第一步（成功）**：你在浏览器点同意后，跳转回本机 `http://localhost:51913/oauth-callback`。因为走的是本机回环地址，Cockpit Tools 立刻拿到了授权码 `code`，并给浏览器返回了「✅ 授权成功」页面。
2. **第二步（超时失败）**：Cockpit Tools 后端拿到 `code` 后，需要向 `https://oauth2.googleapis.com/token` 和 `https://www.googleapis.com/oauth2/v2/userinfo` 发起 HTTPS 请求换取 Token。
   - **日志证据**：
     - 第一次（`16:55:47`）换 Token 花了 `4.7s` 勉强成功，但紧接着拉取 `www.googleapis.com/oauth2/v2/userinfo` 卡了 **`12.6s` 超时失败**；
     - 第二次（`16:56:24` → `16:56:39`）请求 `oauth2.googleapis.com/token` 直接卡满 **`15.0s` 超时断开**（同时间段连 `github.com` 检查更新也超时失败，而 `chatgpt.com` 走 Phoenix 节点是通的）。
   - **实测复现**：你当前本机开启了 TUN Fake-IP（`oauth2.googleapis.com` 解析为 `198.18.1.117`，本地代理端口 `127.0.0.1:7897`），实测请求 `https://oauth2.googleapis.com/token` 时 **TLS 握手阶段就卡了 7~8 秒以上直至超时**——即当前代理里命中 **Google API 分流规则的节点严重丢包/断流**（浏览器能打开网页是因为复用了已有连接或走了 HTTP/3 QUIC）。同时 Cockpit Tools 内置的 `global_proxy_enabled` 也是关闭状态。

---

### 解决步骤（2 分钟搞定）

1. **切换 Google 代理节点**
   打开你的代理软件（端口 `7897` 对应的 Clash / Mihomo 客户端），检查 **Google / 全局分流组**，换一个低延迟、稳定的节点。换完后可以在终端跑下面这行命令验证（只要在 **1~2 秒内返回 `HTTP/2 404` 或 `405`** 就说明通了）：
   ```bash
   curl -I -x http://127.0.0.1:7897 --max-time 5 https://oauth2.googleapis.com/token
   ```
2. **在 Cockpit Tools 内显式填好代理**
   打开 Cockpit Tools 左下角 **设置 → 网络 / 全局代理**，开启代理并填入：
   ```text
   http://127.0.0.1:7897
   ```
   （显式走 HTTP 代理比让 Rust `reqwest` 裸跑在 Fake-IP TUN 虚拟网卡上稳得多。）
3. **重新点击一次「开始 OAuth 授权」**（注意每次报错后旧的 `code` 已失效，不能点「我已授权，继续」重试旧链接，需要重新发起一次）。

---

### 顺手在日志和配置里发现的 2 个提醒

- **有一个旧 Codex 账号 Token 已失效**：日志 `16:43:36` 报了 `401 Unauthorized (invalid_refresh_token)`（对应仪表盘 Codex 右上角 `+1` 里的另一个已存账号），顺手在 Codex 列表里点一下重新授权即可恢复。
- **检查 `agy` CLI 路径与兼容性**：在 [config.json](file:///Users/a86198/.antigravity_cockpit/config.json) 里看到 `antigravity_cli_path` 目前为空，你的 CLI 路径是 `/Users/a86198/.local/bin/agy`，可以在设置里补上。另外新版 `agy` CLI 使用 macOS 系统钥匙串（Keychain）存储登录态——等你登好两个 Gemini 账号后可以测试一下在 Cockpit 里切号能否直接让终端 `agy` 生效；如果它只切了 IDE 没切动 `agy` CLI，随时告诉我，我可以给你补一个直接联动 `agy` / `codex` 的轻量切号脚本。
