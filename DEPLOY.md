# 装到 Android 手机上

**已上线：<https://zhangsixiang875-sudo.github.io/study/>**

现在是「学习台」：一个装内容的容器，不只是一个 FX 练习 App。
内容格式见 [CONTENT.md](CONTENT.md)，加新内容不用改引擎。

---

## 手机安装（30 秒）

1. 手机 Chrome 打开上面的地址
2. 右上角 `⋮` → **添加到主屏幕** → 安装
3. 桌面出现「学习台」图标，点开全屏、无地址栏、断网可用

装好之后即使电脑关机也无所谓：页面、图标、内容包都缓存在手机里。

---

## 改内容后怎么更新

仓库：<https://github.com/zhangsixiang875-sudo/study>

```
cd gh-pages
git add -A && git commit -m "更新内容" && git push
```

GitHub Pages 会自动重建（约 1 分钟）。手机上重开 App 就是新内容。

如果改了 `index.html`，要顺手把 `sw.js` 里的 `CACHE` 版本号 +1，
否则手机的 service worker 会一直用旧缓存。

---

## 局域网（不联网时用）

```
py -3 fxapp/serve.py
```

手机连同一 WiFi，打开打印出的地址。这个方式下 App 会自动从 `packs/` 拉内容。

---

## 学习台的结构

```
目录（学习台）
 ├ 复习 N 个          ← 全课程到期考点汇总
 ├ 课程卡片            ← 每门课一个，显示进度和待复习数
 ├ 内容包              ← 导入/删除/自检
 └ 学习记录            ← 正确率、每个考点的错因和下次复习
```

进度按 `包id/课程id/单元id` 存，加新内容包不会覆盖旧记录。

---

## 改内容 / 加内容

见 [CONTENT.md](CONTENT.md)。改完必须：

```
cd fxapp/tools
py -3 build.py
```

看到 `golden: 42 bad: 0` 和 `内容包：...` 两行都正常才算通过。

---

## 已知限制

1. 模板（随机出数）只有 FX 那一套；别的资料只能用静态题（`items`）。
2. 手机和电脑的进度是两套，只能导出 JSON 人工对照。
3. 没有推送提醒。
4. 内容包和进度存在浏览器 localStorage，清浏览器数据会丢；重要进度记得导出。
5. 部署账号是 `zhangsixiang875-sudo`（不是 `lenana254`）。
