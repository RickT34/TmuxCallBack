# TmuxCallback

让 tmux 中的 Agent 在长任务结束后自动继续工作。

使用 Codex 等终端 Agent 运行训练、构建或批处理时，任务可能持续几小时。TmuxCallback 在后台执行指定命令，等命令退出后，向 Agent 所在的 tmux pane 发送一段文本，再**单独按下 Enter 键**，省去手动回来发送“请继续”的步骤。

## 使用方法

需要 macOS 或 Linux、Python 3 和 tmux，无第三方 Python 依赖。下载仓库后即可使用。

在 tmux 中运行 Agent，让它通过以下命令启动长任务（替换脚本路径和任务内容）：

```bash
/path/to/TmuxCallback/tmux_callback \
  -c 'python train.py > train.log 2>&1' \
  -m '训练命令已退出，请检查 train.log 和产物并继续。'
```

默认后台运行，立即返回进程 PID；自动定位启动时所在的 tmux pane。命令在启动目录执行，无论成功还是失败退出，都会发送唤醒消息。命令末尾不要加 `&`。

日志默认保存在**脚本所在目录的 `tmux-callback.log`**，追加记录唤醒时间（含时区）、发送文本、目标 pane、命令退出码和发送结果。未重定向的后台命令输出也保存在这里。

可选参数：

- `-t work:0.0`：指定接收消息的 pane。
- `--socket /path/to/socket`：指定 tmux 服务器。
- `--log /path/to/file.log`：自定义日志路径。
- `--foreground`：前台运行，便于调试。

可以将 [AGENT_USAGE.md](AGENT_USAGE.md) 提供给 Agent，作为使用提示词。

## 注意

保持目标 pane 中的 Agent 等待输入；消息必须为单行文本。脚本只负责发送文本和 Enter，不判断 Agent 是否已恢复，也不判断任务是否成功。关闭 tmux 服务器或重启机器后，不能保证任务与唤醒继续执行。
