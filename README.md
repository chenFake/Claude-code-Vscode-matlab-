该项目主要展示了一般的claude code配置方法，并结合官方插件，将claude code嵌入vscode中，此外，实现了claude code与matlab之间的通信，并集成在vscode中，从而达到自动matlab代码编辑，脚本生成，结果保存，代码自动debug等功能

# Claude Code 从零配置指南（阿里云百炼云）

> 本文记录从零开始配置 Claude Code 的完整流程，基于阿里云百炼云 Coding Plan 套餐。

---

## 目录

1. [前置条件](#前置条件)
2. [安装 Node.js](#安装-nodejs)
3. [安装 Claude Code CLI](#安装-claude-code-cli)
4. [配置 API](#配置-api)
5. [配置 VS Code 插件](#配置-vs-code-插件)
6. [接入 MATLAB](#接入-matlab)
7. [完成配置](#完成配置)
8. [参考资料](#参考资料)

---

## 一、前置条件

本机已安装以下软件：
- Git Bash
- VS Code
- Python

---

## 二、安装 Node.js

1. 访问 [Node.js 官网](https://nodejs.org/) 下载 Node.js

   ![Pasted image 20260228022136.png](附件/Pasted%20image%2020260228022136.png)

2. 运行安装程序，一路默认选项完成安装

   ![Pasted image 20260228022224.png](附件/Pasted%20image%2020260228022224.png)

3. 安装完成后可选择**不安装** "Install necessary tools" 选项，不影响后续使用

---

## 三、安装 Claude Code CLI

### 3.1 换源（可选）

为避免网络问题导致下载中断，可提前切换 npm 镜像源：

```shell
npm config set registry https://registry.npmmirror.com
```

### 常用 npm 镜像源

| 镜像源 | 地址 |
|--------|------|
| npm 官方 | https://registry.npmjs.org/ |
| 淘宝 NPM | http://registry.npmmirror.com |
| 阿里云 | https://npm.aliyun.com |
| 腾讯云 | https://mirrors.cloud.tencent.com/npm/ |
| 华为云 | https://mirrors.huaweicloud.com/repository/npm/ |
| 网易 | https://mirrors.163.com/npm/ |
| 中科大 | http://mirrors.ustc.edu.cn/ |
| 清华大学 | https://mirrors.tuna.tsinghua.edu.cn/ |

> 腾讯、华为、阿里的镜像站基本上比较全

### 3.2 安装步骤

1. 打开 PowerShell，运行安装命令：

```shell
npm install -g @anthropic-ai/claude-code
```

2. 如遇到权限问题，重新打开 PowerShell，**用管理员模式运行**

   ![Pasted image 20260228023136.png](附件/Pasted%20image%2020260228023136.png)

3. 输入以下命令，检查输出：

```shell
node --version
npm --version
```

如果输出正常，则安装成功。

4. 调回普通用户模式，仍然报错，可能为部分组件未能完全更新的原因，此时可尝试重启电脑

5. 在管理员模式下运行，安装 claude

   ![Pasted image 20260228023747.png](附件/Pasted%20image%2020260228023747.png)

6. 如遇到以下问题，重启 PowerShell

   ![Pasted image 20260228023801.png](附件/Pasted%20image%2020260228023801.png)

   > 另外也有可能是安装包失误的问题，重新检查 npm 命令是否正确

7. 运行 `claude`，如显示以下内容，安装成功，进入下一步

   ![Pasted image 20260228024523.png](附件/Pasted%20image%2020260228024523.png)

---

## 四、配置 API

本次使用的是阿里云的百炼云 Coding Plan，因此主要参考其官方文档，其他产品可参考 API 供应商的官方文档。

**参考资料：**
- [阿里云百炼控制台](https://bailian.console.aliyun.com/cn-beijing/?tab=model#/efm/coding_plan)
- [接入教学文档](https://bailian.console.aliyun.com/cn-beijing/?tab=doc#/doc/?type=model&url=3023078)

### 方法一：设置环境变量（不推荐）

> ⚠️ 该方法依赖环境变量，VS Code 插件支持较差，稳定性不足。

**步骤：**

1. 在 CMD 中运行以下命令，设置环境变量：

```powershell
setx ANTHROPIC_AUTH_TOKEN "YOUR_API_KEY"
setx ANTHROPIC_BASE_URL "https://coding.dashscope.aliyuncs.com/apps/anthropic"
setx ANTHROPIC_MODEL "qwen3.5-plus"
```

2. 打开一个新的 CMD 窗口，运行以下命令，检查环境变量是否生效：

```powershell
echo %ANTHROPIC_AUTH_TOKEN%
echo %ANTHROPIC_BASE_URL%
echo %ANTHROPIC_MODEL%
```

   ![Pasted image 20260228025321.png](附件/Pasted%20image%2020260228025321.png)

3. 编辑或新增 `C:\Users\%USERNAME%\.claude.json` 文件，将 `hasCompletedOnboarding` 字段的值设置为 `true`，并保存文件。

4. 重新运行 claude

   ![Pasted image 20260228025916.png](附件/Pasted%20image%2020260228025916.png)

5. 选择 yes，继续往下，显示运行成功

   ![Pasted image 20260228030019.png](附件/Pasted%20image%2020260228030019.png)

这时可以提问，运行。

---

### 方法二：编辑 settings.json 文件（推荐）

> ✅ 推荐方式，配置稳定且 VS Code 插件支持良好。

**步骤：**

1. 创建并打开配置文件 `C:\Users\您的用户名\.claude\settings.json`

2. 编辑配置文件。将以下文本内容复制进 settings.json 文件中，将 YOUR_API_KEY 替换为自己的 [API Key](https://bailian.console.aliyun.com/cn-beijing/?tab=model#/efm/coding_plan)。保存配置文件，重新打开一个终端即可生效。

```json
{
    "env": {
        "ANTHROPIC_AUTH_TOKEN": "YOUR_API_KEY",
        "ANTHROPIC_BASE_URL": "https://coding.dashscope.aliyuncs.com/apps/anthropic",
        "ANTHROPIC_MODEL": "qwen3.5-plus"
    }
}
```

3. 编辑或新增 `C:\Users\您的用户名\.claude.json` 文件，将 `hasCompletedOnboarding` 字段的值设置为 `true`，并保存文件。

```json
{
  "hasCompletedOnboarding": true
}
```

4. 在命令行窗口运行 claude，即可完成设置

---

## 五、配置 VS Code 插件

### 5.1 安装插件

在 VS Code Extension 中，搜索 claude code，下载插件

![Pasted image 20260228030324.png](附件/Pasted%20image%2020260228030324.png)

> ⚠️ **注意**：最近的插件在安装时可能存在兼容性错误，建议使用回退版本的插件。本次测试中，**2.1.56 版本**是可用的。

![Pasted image 20260228031446.png](附件/Pasted%20image%2020260228031446.png)

### 5.2 插件配置

1. 这里会显示需要登录，如果不要登录界面，可以在 settings 中添加以下设置：

```json
"claudeCode.disableLoginPrompt": true
```

此时即避免了登录

![Pasted image 20260228031615.png](附件/Pasted%20image%2020260228031615.png)

2. 在 setting 文件中添加所使用的模型名称：

```json
"claudeCode.selectedModel": "qwen3.5-plus"
```

此时即可在 UI 中与模型问答

![Pasted image 20260228032222.png](附件/Pasted%20image%2020260228032222.png)

### 5.3 全局记忆配置

在 `~/.claude` 文件夹下写 `CLAUDE.md` 文件作为全局记忆，在里面添加"用中文回答用户问题"，可避免 claude 反复用英文回答。

![Pasted image 20260303002106.png](附件/Pasted%20image%2020260303002106.png)

---

## 六、接入 MATLAB

### 6.1 添加 MATLAB 到环境变量

1. Win+S 搜索"编辑环境变量"

   ![Pasted image 20260302235837.png](附件/Pasted%20image%2020260302235837.png)
   ![Pasted image 20260303000013.png](附件/Pasted%20image%2020260303000013.png)

2. 添加环境变量

   ![Pasted image 20260303000203.png](附件/Pasted%20image%2020260303000203.png)

3. 点击新建，把 matlab 的位置添加到系统变量 → Path 中

   **找到 matlab 位置的方法：**

   右键 matlab 程序，点击打开文件所在的位置

   ![Pasted image 20260303000336.png](附件/Pasted%20image%2020260303000336.png)

4. 复制位置到新建环境变量

   ![Pasted image 20260303000510.png](附件/Pasted%20image%2020260303000510.png)

5. 打开 CMD（快捷键 Win+R，输入 cmd，回车）

   ![Pasted image 20260303000624.png](附件/Pasted%20image%2020260303000624.png)

6. 在命令行窗口输入 `matlab`，回车

   ![Pasted image 20260303000658.png](附件/Pasted%20image%2020260303000658.png)

7. 若无报错，并且等待后 matlab 启动，则 matlab 已被添加至环境变量中

   ![Pasted image 20260303000759.png](附件/Pasted%20image%2020260303000759.png)

---

### 6.2 安装 MATLAB MCP Server

1. 进入 [MATLAB MCP Server GitHub](https://github.com/matlab/matlab-mcp-core-server)

   ![Pasted image 20260303000858.png](附件/Pasted%20image%2020260303000858.png)

2. 下载服务器的二进制文件（.exe 应用），记住文件所在位置

   ![Pasted image 20260303004433.png](附件/Pasted%20image%2020260303004433.png)

3. 点击运行，显示已连接到 matlab

   ![Pasted image 20260303004510.png](附件/Pasted%20image%2020260303004510.png)

4. 这里可以将程序文件移到 matlab/bin 路径下，方便管理和查找

   ![Pasted image 20260303004620.png](附件/Pasted%20image%2020260303004620.png)

   记下文件地址，例如：
   ```
   D:\Program Files\MATLAB\R2024b\bin\matlab-mcp-core-server-win64.exe
   ```

---

### 6.3 添加 MCP 工具

**命令行配置：**

在命令行（CMD）中，输入以下命令：

```bash
claude mcp add --transport stdio --scope user matlab -- "D:\Program Files\MATLAB\R2024b\bin\matlab-mcp-core-server-win64.exe"
```

> `--scope user` 指令会让该 mcp 工具对所有项目生效
> 注意：`""` 内要放入自己的 mcp 程序位置

**验证：**

此时打开命令行窗口，输入 `claude`，在 claude 聊天界面中输入 `/mcp`，查看工具状态，若显示 **matlab connected** 则连接成功。

此时，可通过对话调用 matlab

![Pasted image 20260303010158.png](附件/Pasted%20image%2020260303010158.png)
![Pasted image 20260303010324.png](附件/Pasted%20image%2020260303010324.png)
![Pasted image 20260303010508.png](附件/Pasted%20image%2020260303010508.png)

---

### 6.4 VS Code 插件配置

**终端设置：**

注意改 terminal 为 CMD，Ctrl+Shift+P，搜索 `Terminal: Select Default Profile`

![Pasted image 20260303005645.png](附件/Pasted%20image%2020260303005645.png)

**settings.json 配置：**

在 settings.json 文件中，添加以下字段：

```json
{
  "mcpServers": {
    "matlab": {
      "command": "D:\\Program Files\\MATLAB\\R2024b\\bin\\matlab-mcp-core-server-win64.exe",
      "args": [],
      "transport": "stdio"
    }
  }
}
```

> 注意：与前面的 `env` 字段中间要有逗号

**完整的 settings.json 配置示例：**

```json
{
  "env": {
    "ANTHROPIC_AUTH_TOKEN": "YOUR_API_KEY",
    "ANTHROPIC_BASE_URL": "https://coding.dashscope.aliyuncs.com/apps/anthropic",
    "ANTHROPIC_MODEL": "qwen3.5-plus"
  },
  "mcpServers": {
    "matlab": {
      "command": "D:\\Program Files\\MATLAB\\R2024b\\bin\\matlab-mcp-core-server-win64.exe",
      "args": [],
      "transport": "stdio"
    }
  }
}
```

保存文件，在 vscode 中运行

![Pasted image 20260303010726.png](附件/Pasted%20image%2020260303010726.png)

此时可以看到，vscode 的官方插件的 UI 中也保存了 matlab 的 mcp，此时可以在 UI 界面中与 claude code 对话。

**使用官方插件的优点：**
- 不仅可以文字对话，也可以结合图片与模型对话
- 集成性更强

---

## 七、完成配置

配置完成后，可在 VS Code 中通过 Claude Code 插件与模型对话，并调用 MATLAB 功能。

### VS Code + Claude + MATLAB 完整演示

[点击观看演示视频](https://yc.tuchung.cc/uploads/video_5ccc7b05dafc43dd9cd3046b4c74c862.mp4)

---

## 参考资料

- [阿里云百炼控制台](https://bailian.console.aliyun.com/cn-beijing/?tab=model#/efm/coding_plan)
- [阿里云百炼接入文档](https://bailian.console.aliyun.com/cn-beijing/?tab=doc#/doc/?type=model&url=3023078)
- [MATLAB MCP Server GitHub](https://github.com/matlab/matlab-mcp-core-server)


