#  Git 学习感想
> 付家瑞
## 学习内容(请尽可能不要摘抄, 用自己的语言描述出来)

### 一、Git 到底是什么：把"仓库"想象成小作坊

一开始我分不清 Git 仓库和 GitHub 仓库，后来用"小作坊给总部供货"的比喻才理清：

- **工作区 = 书桌**：在这里改文件，东西随便摊着
- **暂存区 = 打包台**：`git add` 就是把这次要交的东西放上打包台，可以挑着放
- **本地仓库 = 我家仓库**：`git commit` 是封箱、贴标签、登记在账本上
- **远程仓库 = 总部仓库**：`git push` 是装车发货

Git 仓库和 GitHub 仓库其实是同一个东西的两份拷贝：Git 仓库在我电脑上（文件夹里的 `.git`），GitHub 仓库在云端，靠 `clone / push / pull` 保持同步。我走的是"照着现成房子复制一套"的路线（`git clone`），而不是从零盖房子（`git init`）——虽然我后来为了练习，也用 `git init` 造了一个本地仓库，这才发现它和 clone 出来的仓库行为不一样。

### 二、完整流程：clone → add → commit → push

我用自己真实敲过的命令走一遍：

```bash
git clone https://github.com/741Ray/2026-Geek-Autumn.git   # 把云端仓库搬到本地
cd 2026-Geek-Autumn                                          # 进入仓库
echo "# Hello 付家瑞" > hello.md                            # 在工作区创建文件
git add hello.md                                             # 放上打包台
git commit -m "add hello.md"                                 # 封箱存档
git push origin main                                         # 发货到远程
```

`git status` 是我随时用的"查看进度"按钮：红色 = 还在书桌没打包，绿色 = 已上打包台。迷路时敲一下就知道文件卡在哪一步。

还有个细节：`git push` 永远推给 `origin`，而 `origin` 是你 `git clone` 时 Git 自动记下的"上游是谁"。所以 clone 的对象直接决定了 push 能不能成功——这也是为什么必须先 Fork 再 Clone：直接 clone 组织仓库的话，我没有写权限，push 会被拒。

### 三、今天踩过的坑：每一个都值得记下来

我把今天遇到的报错都记进了"问题解决记录"，这里挑几个印象最深的：

1. **`git config` 配置了却没生效**：我输的是 `git config --global user.name"741Ray"`，漏了空格。Git 把 `user.name741Ray` 当成完整键名去读取，找不到就静默返回了，看起来像成功其实没有。教训：用 `git config --global --list` 验证，看到输出才算数。

2. **`git commit` 忘了 `-m`，掉进 vim 编辑器**：Git 自动打开编辑器让我写提交说明，我按教程按 `i` 却多按了一次，第一行多了个孤零零的 `i`。最后用 `Esc` → `:q!` 退出，改用 `git commit -m "说明"` 一步到位。教训：提交时永远写完整的 `-m`。

3. **`ls` 命令找不到**：`bash: ls: command not found`。我用的是 MSYS2 的 UCRT64 终端，环境变量没配好，找不到外部的 `ls` 程序。教训：用 `git status` 代替查看文件状态，Git 自带的命令不受影响。

4. **SSH 端口不通**：`ssh: connect to host github.com port 22: Connection refused`。我一开始用 `git@github.com:...` 的 SSH 地址，22 端口在当前网络被挡。教训：改用 HTTPS 地址（`git remote set-url origin https://...`），走 443 端口就通了。

5. **push 成功却看不到文件**：推到 `Tasks` 仓库后，网页默认显示 `main` 分支，而我推的是 `master` 分支。教训：分支名要核对，网页上能切换分支查看。

6. **PR 提不了**：我在 Tasks 仓库想用 PR 合并 master 到 main，GitHub 却提示 `There isn't anything to compare`，因为那两个分支一个是 `git init` 新建的、一个是仓库原有的，没有共同祖先。教训：PR 要求两条分支有共同起点，这就是为什么必须在 fork 仓库里提 PR——fork 完整继承了原仓库的历史。

### 四、Fork 与 PR：第一次体验开源协作

用"交作业"的比喻，我现在这样理解：

- **Fork = 去前台复印一份题目册**，封面写上我的名字，复印件归我，我可以在上面随便写
- **Clone = 把复印件拿回书桌**，本地才有文件可以改
- **PR = 给老师发的纸条**："我写好了，请看看要不要收进原件"

关键是这是**请求**而不是直接写入：老师能看到内容、能留言、能拒绝、也能点 Merge 收进原件。我作为贡献者没有组织仓库的写权限，只能通过这种方式贡献——这既保护了原仓库不被搞乱，也让每次改动可追溯、可讨论。

提 PR 的方向也容易搞反：**base 是目标（组织仓库），compare 是来源（我的 fork）**。口诀是"从我的 fork 拿，往组织仓库放"。

### 五、命令行 vs 图形化：我的选择

任务 2 问"Git Bash 提交麻烦吗？有没有更方便的方式？"

我的回答是：**一开始麻烦，理清逻辑后其实挺顺手**。

- Git Bash 命令行的优点是快、通用、能看到每一步发生了什么；缺点是命令要记，出错时提示不友好
- 更方便的方式有：VS Code 内置源代码管理（点按钮就能 add/commit，改动文件用颜色标出）、GitHub Desktop、GitHub 网页直接编辑
- 我的选择是**日常练习坚持用命令行**：任务要求用 Git Bash，而且理解了"工作区→暂存区→本地仓库→远程仓库"这条链之后，敲命令反而比找按钮快。图形化工具我用来辅助检查"到底改了哪些文件"

友情提示里说"避免过度依赖图形化操作"，我现在有点理解这句话了：图形化工具把细节藏起来了，出了问题不知道发生了什么；命令行虽然一开始难，但每一步都是透明的。

### 六、最想安利和最想吐槽的

最想安利的是 `git status`——迷路时敲一下，它就会告诉我文件卡在哪一步，对新手特别友好。还有 Learn Git Branching 那个闯关网站，把抽象的分支概念变成了可以动手玩的关卡。

最想吐槽的是各种"看起来成功了其实没成功"的静默失败：漏空格的 `git config`、换行符警告、无端掉进 vim 编辑器。这些坑踩过一次就记住了，也都写进了问题解决记录——现在想想，这些恰恰是我学得最扎实的部分。

最后，关于 Geek 精神：我觉得 Geek 不只是"会敲命令"，而是遇到问题愿意一层层挖下去，把"为什么"搞明白，然后把经验记录下来帮到别人。今天我从"GitHub 打不开"一路查到 hosts 劫持和运营商出口，从"PR 提不了"查到共同祖先的原理，这个过程比单纯记住几条命令更有价值。

## 参考资料

1. [廖雪峰的 Git 教程](https://liaoxuefeng.com/books/git/index.html)
2. [Learn Git Branching 关卡挑战](https://learngitbranching.js.org/?locale=zh_CN)
3. [GitHub 官方指南](https://docs.github.com/zh/get-started)
4. [git 命令逻辑梳理图](https://my-note-drawing-bed-1322822796.cos.ap-shanghai.myqcloud.com/picture/202405192337199.png)
5. 考核任务书中的友情提示，以及 mentor 和 AI 助手的答疑
