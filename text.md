

---

# Ubuntu APT 包管理器完全指南



## 目录
1.  [什么是 APT？](#什么是-apt)
2.  [更新软件源列表](#1-更新软件源列表)
3.  [升级已安装的包](#2-升级已安装的包)
4.  [搜索软件包](#3-搜索软件包)
5.  [安装软件包](#4-安装软件包)
6.  [卸载软件包](#5-卸载软件包)
7.  [查看软件包信息](#6-查看软件包信息)
8.  [重要 更换软件源（换源）](#重要-更换软件源换源)
9.  [清理与维护](#7-清理与维护)

---

## 什么是 APT？

APT (Advanced Package Tool) 是 Debian 和 Ubuntu 系列操作系统的核心包管理工具。它用于自动从**软件源**（Repository）下载、配置、安装、卸载 `.deb` 格式的软件包，并自动处理依赖关系。

*   `apt`：新一代的命令行工具，推荐使用，输出更友好。
*   `apt-get`：旧版的命令行工具，功能依旧强大且被广泛支持。
    > **建议**：在日常使用中，优先使用 `apt`。

---

## 1. 更新软件源列表

**命令：`sudo apt update`**

*   **作用**：从配置的软件源服务器（如 `sources.list` 中列出的地址）获取最新的软件包列表信息（包括版本、依赖关系等）。**这个命令不会安装或更新任何已安装的软件本身。**
*   **解释**：这个操作相当于刷新你的“软件商店货架”，让你知道有哪些软件的最新版本可用。这是执行安装或升级操作前**推荐首先执行**的命令。
*   **示例**：
    ```bash
    sudo apt update
    ```
*   **输出解读**：
    *   `命中:URL`：连接成功。
    *   `获取:URL`：正在下载包列表信息。
    *   `已读取软件包列表完毕`：更新完成。底部会显示**有多少个可升级的软件包**。

---

## 2. 升级已安装的包

**命令：`sudo apt upgrade`**

*   **作用**：将所有已安装的软件包升级到最新可用的版本。该命令**必须**在 `apt update` 之后执行。
*   **解释**：根据 `update` 刷新后的“货架”，把系统上所有旧版本的软件更新到新版本。
*   **示例**：
    ```bash
    sudo apt upgrade
    ```
*   **注意事项**：
    *   它会列出所有将要升级的包，并询问你是否继续 (`Do you want to continue? [Y/n]`)。输入 `Y` 并按回车确认。
    *   如果需要升级的软件包涉及系统核心组件或需要重启服务，可以使用更强大的命令：
        ```bash
        sudo apt full-upgrade
        ```
        （旧版中为 `apt-get dist-upgrade`）此命令会智能地处理依赖关系的变化，可能会安装新包或删除旧包。

---

## 3. 搜索软件包

**命令：`apt search <关键词>`**

*   **作用**：在所有可用的软件源中搜索包含指定关键词的软件包。
*   **解释**：当你不知道要安装的软件包确切全名时，可以用此命令查找。
*   **示例**：搜索与 `python3` 相关的开发包
    ```bash
    apt search python3-dev
    ```
*   **输出解读**：会列出所有包名或描述中包含 `python3-dev` 的软件包。`python3-dev` 通常会出现在最前面。

---

## 4. 安装软件包

**命令：`sudo apt install <包名>`**

*   **作用**：下载并安装指定的软件包及其所有依赖项。
*   **解释**：这是最常用的安装命令。
*   **示例**：安装 `curl` 和 `git`（可以一次性安装多个包）
    ```bash
    sudo apt install curl git
    ```
*   **其他用法**：
    *   **安装特定版本**：使用 `=` 指定版本
        ```bash
        sudo apt install package-name=version-number
        ```
    *   **模拟安装（干跑）**：使用 `-s` 参数模拟安装过程，不会实际安装，用于检查会发生什么。
        ```bash
        sudo apt install -s package-name
        ```

---

## 5. 卸载软件包

### 卸载软件（保留配置文件）

**命令：`sudo apt remove <包名>`**

*   **作用**：卸载指定的软件包，但会保留其配置文件。这样以后重装该软件时，你的设置还在。
*   **示例**：卸载 `curl`（保留其配置）
    ```bash
    sudo apt remove curl
    ```

### 彻底卸载软件（删除配置文件）

**命令：`sudo apt purge <包名>`**

*   **作用**：卸载指定的软件包，并**同时删除**其配置文件。
*   **示例**：彻底卸载 `curl` 及其所有配置
    ```bash
    sudo apt purge curl
    # 或者使用 remove 和 purge 的组合效果
    sudo apt remove --purge curl
    ```

### 自动移除不再需要的依赖

**命令：`sudo apt autoremove`**

*   **作用**：自动卸载那些为了满足其他软件包依赖而自动安装，但现在不再被任何程序所需要的软件包。
*   **解释**：这是一个很好的系统清理习惯。在执行 `upgrade` 或 `remove` 后，可以运行一下此命令。
*   **示例**：
    ```bash
    sudo apt autoremove
    ```

---

## 6. 查看软件包信息

**命令：`apt show <包名>`**

*   **作用**：显示指定软件包的详细信息。
*   **信息包括**：版本、大小、依赖关系、描述、主页、下载大小等。
*   **示例**：查看 `curl` 包的详细信息
    ```bash
    apt show curl
    ```

**命令：`apt list [选项]`**

*   **作用**：列出符合条件的软件包。
*   **常用选项**：
    *   `--installed`：列出所有**已安装**的软件包。
        ```bash
        apt list --installed
        ```
    *   `--upgradable`：列出所有**可升级**的软件包（需要先执行 `apt update`）。
        ```bash
        apt list --upgradable
        ```

---

## **[重要] 更换软件源（换源）**

默认的软件源服务器可能在国外，访问速度较慢。将其替换为国内的镜像源可以**极大提升下载速度**。

**警告：操作源列表文件需要谨慎！**

### 推荐镜像源
*   **清华大学 TUNA**: https://mirrors.tuna.tsinghua.edu.cn/
*   **阿里云**: https://mirrors.aliyun.com/
*   **华为云**: https://mirrors.huaweicloud.com/

### 换源步骤（以切换至清华大学源为例）

1.  **备份原来的源列表文件**
    ```bash
    sudo cp /etc/apt/sources.list /etc/apt/sources.list.backup
    ```

2.  **编辑源列表文件**
    ```bash
    # 使用 nano 编辑器（推荐新手）
    sudo nano /etc/apt/sources.list

    # 或者使用 vim
    # sudo vim /etc/apt/sources.list
    ```

3.  **替换文件内容**
    *   访问清华大学开源软件镜像站：[Ubuntu 镜像使用帮助](https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/)
    *   根据你的 Ubuntu 版本（如 22.04 LTS Jammy Jellyfish），选择对应的配置说明。
    *   **删除** `sources.list` 文件中所有的内容，将镜像站提供的**全部内容**复制粘贴到文件中。
    *   **注意**：通常需要取消 `source` 和 `universe` 等仓库的注释（即删除行首的 `#`）。

4.  **保存并退出编辑器**
    *   **nano**：按 `Ctrl+X`，然后输入 `Y` 确认保存，最后按 `Enter` 确认文件名。
    *   **vim**：按 `Esc` 后输入 `:wq`，再按 `Enter`。

5.  **更新软件源列表**
    ```bash
    sudo apt update
    ```
    此时，所有的URL都应该显示为 `mirrors.tuna.tsinghua.edu.cn`，表示换源成功。之后你再执行安装更新，速度就会快很多。

---

## 7. 清理与维护

*   **清理已下载的软件包缓存**：`apt` 会将下载的 `.deb` 包保存在 `/var/cache/apt/archives/` 中，以便后续重装。定期清理可以释放磁盘空间。
    ```bash
    # 清理所有已下载的安装包
    sudo apt clean

    # 清理过时的安装包（更常用、更安全）
    sudo apt autoclean
    ```

---
**总结命令流**：
```bash
sudo apt update         # 刷新列表
sudo apt upgrade        # 升级系统
apt search [软件名]     # 搜索软件
sudo apt install [软件名] # 安装软件
sudo apt remove [软件名]  # 卸载软件
sudo apt autoremove     # 清理孤儿包
```