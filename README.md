# 智能长卷 · 健身房耳机版（自托管）

同一个单文件应用，三种部署方式；部署到自己的域名后，「听」视图里填入 Anthropic API key 即可启用 **Claude 实时语音问答**（claude.ai 的 Artifact 沙盒禁止页面外联，所以实时问答只在自托管版可用）。

## 口令保护

`index.html` 是**加密后的解锁门**：全部内容以 AES-256-GCM 加密（PBKDF2-SHA256 · 31 万次迭代），仓库与网址上只有密文，口令不经过任何服务器。输对口令即在浏览器内解密展开，勾选「记住这台设备」后仅需输入一次（存的是派生密钥，清浏览器数据即失效）。

- 口令存放在 `~/test/.gym-password`（仓库之外，勿提交）；也可用环境变量 `GYM_PASSWORD` 覆盖
- 改口令：编辑该文件 → `./build-gym-app.sh` → commit + push
- 解密需要 HTTPS 或 localhost（WebCrypto 限制）；纯 HTTP 局域网地址会提示不可用，可用 SSH 端口转发走 localhost

## 重新打包
```bash
./build-gym-app.sh          # 从 ../llm-changjuan.html 生成加密版 llm-gym-app/index.html
```

## 方式一 · GitHub Pages（推荐，手机随时能开）
```bash
cd llm-gym-app
git init -b main && git add . && git commit -m "gym app"
gh repo create llm-gym-app --public --source=. --push
gh api -X POST repos/{owner}/llm-gym-app/pages -f build_type=legacy -f 'source[branch]=main' -f 'source[path]=/'
# 约 1 分钟后：https://<你的用户名>.github.io/llm-gym-app/
```
注意：Pages 站点对外可访问（URL 不公开即可）。页面本身不含任何密钥，key 只存在你手机浏览器的 localStorage 里。

## 方式二 · 局域网 / 本机预览
```bash
cd llm-gym-app && python3 -m http.server 8787
# 同一 Wi-Fi 下手机打开 http://<Mac 的局域网 IP>:8787/
```
注意：iOS Safari 的麦克风（语音识别）要求 HTTPS 或 localhost；HTTP 局域网下只能打字提问，朗读不受影响。

## 方式三 · Cloudflare Pages / Vercel / Netlify
把 `llm-gym-app/` 目录拖进任一静态托管即可，无需构建。

## iPhone 使用建议
- 用 Safari 打开（不要「添加到主屏幕」后再开：iOS 的独立 web app 模式不支持语音识别）。
- 设置 → 辅助功能 → 朗读内容 → 声音 → 中文，下载「婷婷（增强）」或 Siri 声音，朗读质量提升明显。
- 按 ▶ 后锁屏，耳机的播放/暂停/下一曲键可控制课程；若锁屏后朗读停止，解锁即自动续播。
- 语音问答：按住「按住说话」说完松手；说「暂停」「下一课」「慢一点」「我的进度」「下一步学什么」是直接指令。
