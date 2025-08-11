---

title:  山河 Linux 使用指南：软件包管理技术基础（一）：Nix常用选项说明
author: 白柳子夏
index:  true
---

（*本文以及之后的章节摘自[Nix 2.28参考手册第8章：Command Reference](https://nix.dev/manual/nix/2.28/command-ref/)*）

山河Linux操作系统基于NixOS构建，采用了Nix包管理器作为核心的软件包管理技术。本文将介绍Nix的基本概念和常用命令，帮助用户更好地理解和使用Nix。

### 1.常用选项

大多数 Nix 命令接受以下命令行选项：

`--help`

打印出命令语法的摘要并退出。

`--version`

在标准输出上打印出 Nix 版本号并退出。

`--verbose/-v`

增加在标准错误上打印的诊断消息的详细程度。对于每个 Nix 操作，在标准输出上打印的信息都是明确定义的；任何诊断信息都会打印在标准错误上，而不会打印在标准输出上。

此选项可以重复指定。目前，存在以下详细级别：

`0` “Errors Only”
仅打印解释 Nix 调用失败原因的消息。

`1` “Informational”
打印有关 Nix 正在执行的操作的有用消息。这是默认设置。

`2` “Talkative”
打印更多信息消息。

`3` “Chatty”
打印更多信息消息。

`4` “Debug”
打印调试信息。

`5` “Vomit”
打印大量调试信息。

`--quiet`

降低标准错误中打印的诊断消息的详细程度。这是`-v / --verbose`的逆选项。

此选项可以重复指定。请参阅前面的详细级别列表。

`--log-format` *format*

此选项可用于更改日志格式的输出，格式为以下之一：

- `raw`

    这是 `nix-build` 输出的原始格式。


- `internal-json`

    以结构化的方式输出日志。

### 警告

虽然模式本身相对稳定，但错误消息（即字段msg）的格式可能会在版本之间发生变化。

- `bar`

    仅在构建期间显示进度条。

- `bar-with-logs`

    显示原始日志，底部显示进度条。

- `--no-build-output/-Q`

    默认情况下，构建器写入标准输出和标准错误的输出将回显到 Nix 命令的标准错误中。此选项可抑制此行为。请注意，构建器的标准输出和错误始终写入日志文件中prefix/nix/var/log/nix。

- `--max-jobs/-j 数字`

    将 Nix 并行执行的最大构建作业数设置为指定数量。指定auto使用系统中的 CPU 数量。默认值由max-jobs配置设置指定，该设置本身默认为1。更高的值在 SMP 系统上或利用 I/O 延迟时很有用。

    将其设置为0不允许在本地机器上构建，这在您希望构建仅在远程构建器上进行时很有用。

- `--cores`

    NIX_BUILD_CORES在构建器调用中设置环境变量的值。构建器可以自行决定使用此变量来控制最大并行度。例如，在 Nixpkgs 中，如果 derivation 属性enableParallelBuilding设置为true，构建器会将该-jN标志传递给 GNU Make。如果已设置，则默认为配置设置的值cores，否则为默认值1。该值0表示构建器应使用系统中所有可用的 CPU 核心。

- `--max-silent-time`

    设置构建器在标准输出或标准错误上不产生任何数据的最大秒数。默认值由max-silent-time配置设置指定。 0表示无超时。

- `--timeout`

    设置构建器可以运行的最大秒数。默认值由timeout配置设置指定。 0表示无超时。

- `--keep-going/-k`

    在构建失败的情况下，尽可能继续运行。也就是说，如果构建某个派生类的输入失败，Nix 仍会构建其他输入，但不会构建派生类本身。如果没有此选项，则任何构建失败（替代类构建除外）时，Nix 都会停止，这可能会终止正在进行的构建（在并行或分布式构建的情况下）。

- `--keep-failed/-K`

    /tmp指定在构建失败的情况下，不应删除构建所在的临时目录（通常位于）。构建目录的路径将作为参考消息打印出来。

- `--fallback`

    每当 Nix 尝试构建一个已知每个输出路径的替代品的推导，但通过替代品实现输出路径失败时，就会回过头去构建推导。

    最常见的情况是，当我们注册了替代品以便从网络存储库（例如网络存储库）执行二进制分发时，此选项非常有用。如果存储库关闭，派生的实现将失败。指定此选项后，Nix 将改为构建派生。因此，从二进制文件安装将回退到从源代码安装。此选项不是默认选项，因为通常不希望获取替代品的瞬时失败导致从源代码进行完整构建（并消耗相关的资源）。

- `--readonly-mode`

    使用此选项时，不会尝试打开 Nix 数据库。大多数 Nix 操作都需要数据库访问，因此这些操作将会失败。

- `--arg 名称 值`

    `nix-env`、`nix-instantiate`、`nix-shell` 和 `nix-build` 均接受此选项。在评估 Nix 表达式时，表达式评估器会自动尝试调用遇到的函数。它可以自动调用每个参数都有[默认值（default value）](https://nix.dev/manual/nix/2.28/language/syntax#functions)的函数（例如，{argName ? defaultValue}: ...）。
    使用--arg，你还可以调用参数没有默认值的函数（或覆盖默认值）。也就是说，如果求值器遇到一个参数名为name的函数，它将使用值value来调用它。

    例如，Nixpkgs 中的顶层default.nix实际上是一个函数：

    ```nix
    { # The system (e.g., `i686-linux') for which to build the packages.
    system ? builtins.currentSystem,
    ...
    }: ...
    ```

    因此，如果您调用此 Nix 表达式（例如，当您执行 `nix-env --install --attr pkgname` 时），该函数将使用值 `[builtins.currentSystem](https://nix.dev/manual/nix/2.28/language/builtins)` 作为系统参数自动调用。您可以使用 `--arg` 覆盖此设置，例如，`nix-env --install --attr pkgname --arg system \"i686-freebsd\"`。（请注意，由于参数是 Nix 字符串文字，因此必须对引号进行转义。）

- `--arg-from-file` *name* *path*

    将文件路径的内容作为参数名称传递给 Nix 函数。

- `--arg-from-stdin` *name*

    将 stdin 的内容作为参数名称传递给 Nix 函数。

- `--argstr` *name* *value*

    这个选项类似于--arg，只是值不是 Nix 表达式而是字符串。因此，`--argstr system \"i686-linux\"` 你可以这样写:（外层引号是为了满足 Shell 的要求）`--argstr system i686-linux`。


- `--attr/-A` *attrPath*

    从正在评估的顶级 Nix 表达式中选择一个属性。（仅限 `nix-env`、`nix-instantiate`、`nix-build` 和 `nix-shell`。）*属性路径 attrPath* 是由点分隔的一系列属性名称。例如，给定顶级 Nix 表达式 *e*，属性路径 `xorg.xorgserver` 将导致使用表达式 `e.xorg.xorgserver`。有关具体示例，请参阅 `[nix-env --install](https://nix.dev/manual/nix/2.28/command-ref/nix-env/install)`。

    除了属性名称之外，您还可以指定数组索引。例如，属性路径 `foo.3.bar` 表示选择顶级表达式的 `foo` 属性中数组第四个元素的 `bar` 属性。

- `--eval-store` *storeURL*

    用于评估的 Nix 存储的 URL，即存储派生项（ .drv 文件）和它们引用的输入的位置。

- `--expr/-E`

    将命令行参数解释为要解析和评估的 Nix 表达式列表，而不是 Nix 表达式的文件名列表。（仅限 `nix-instantiate`、`nix-build` 和 `nix-shell`。）

    对于 `nix-shell`，此选项通常用于提供一个 shell，您可以在其中构建表达式返回的包。如果您想要一个包含已构建包的 shell，请将表达式设置为 `nix-shell --packages` 便捷标志。

- `-I / --include` *path*

    向用于解析查找路径的搜索路径列表中添加一个条目。此选项可以多次指定。

    通过 添加的路径-I优先于nix-path配置设置和NIX_PATH环境变量。

- `--impure`

    允许访问可变路径和存储库。

- `--option` *name* *value*

    将 Nix 配置选项name设置为value。这将覆盖 Nix 配置文件中的设置（参见 `nix.conf(5)`）。


- `--repair`

    通过重新下载或重建损坏或缺失的存储路径来修复它们。请注意，此方法速度较慢，因为它需要在构建闭包中计算每个路径内容的加密哈希值。另请注意 下的警告nix-store --repair-path。

> 笔记
>>
>> 请参阅man nix.conf使用命令行标志覆盖配置设置。