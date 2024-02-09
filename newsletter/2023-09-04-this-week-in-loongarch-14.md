---
slug: this-week-in-loongarch-14
title: 每周一龙：第 14 期
authors: [xen0n]
date: 2023-09-05T01:39:31+08:00  # 当晚没写完
tags: [每周一龙]
---

每周一都为大家报道 LoongArch 社区最前线的第一手新鲜资讯！

<!-- truncate -->

如无特别说明，文中提及的日期、时间都为北京时间（UTC+8）。

## 先「马」再看

本栏目的内容具有一定延续性，将持续追踪报道 LoongArch 领域的重要或长期项目（坑）。

### Linux {#linux-6.6-loongarch-changes}

截至发稿时，Linux 6.6 的合并窗口工作正风生水起，子系统维护者们都在忙着整理代码、提交 <ruby>PR<rt>拉取请求</rt></ruby>。
合并窗口预计在美西时间的本周日下午（9 月 10 日；中国时间 9 月 11 日）关闭；
本开发周期的 `arch/loongarch` 变更已由 Huacai Chen 整理完成并[打好了 tag](https://git.kernel.org/chenhuacai/linux-loongson/l/loongarch-6.6)，
主要包含以下内容：

:::info arch/loongarch in Linux 6.6
1. 允许在内核态使用 LSX、LASX（Huacai Chen，应笔者的 SIMD 优化需求而设计）；
2. 添加 SIMD 优化的 RAID5/RAID6 例程（笔者）；
3. 添加龙芯二进制翻译扩展（LBT）支持（Qi Hu、Huacai Chen）；
4. 添加 KGDB、KDB 的基本支持；
    * 框架、核心功能：Qing Zhang；该同学是前龙芯员工，在龙芯时期完成了此工作。
    * 断点、单步：Binbin Zhou。
    * 细节改进调整：Hui Li。
    * 构建错误修复打磨：Randy Dunlap；活跃的上游社区开发者同学之一。
5. 支持开启 KCov（内核的代码覆盖率收集机制）构建内核（Feiyang Chen）；
6. 添加 KFENCE (Kernel Electric-Fence) 支持（Enze Li；该同学是麒麟员工）；
7. 添加 KASAN (Kernel Address Sanitizer) 支持（Qing Zhang）；
8. bug 修复与其他较小变更（Bibo Mao、Weihao Li、Tiezhu Yang）；
9. 默认配置更新。

该信息翻译自 [tag 说明文字](https://git.kernel.org/pub/scm/linux/kernel/git/chenhuacai/linux-loongson.git/tag/?h=loongarch-6.6)，
相关作者信息由笔者整理添加。
除笔者外，未提及工作单位信息的同学都是龙芯员工。
:::

8 月 31 日，Tianrui Zhao、Bibo Mao 等同学开发的龙架构 <ruby>KVM<rt>内核虚拟机</rt></ruby>
补丁系列已[迭代至](https://lore.kernel.org/loongarch/20230831083020.2187109-1-zhaotianrui@loongson.cn/)第 20 版。
目前预计该功能正式露面的首个上游内核版本是 Linux 6.7（下个开发周期），
但我们同时预计社区发行版们将较快跟进支持（提前集成这些补丁）。

:::info 值得一提的是
随着上游的内核 <ruby>CI<rt>持续集成</rt></ruby> 设施的龙架构工具链升级到最新版，
此堆代码中写死的、不利于维护的指令编码终于消失了。
这将不会影响下游未升级工具链版本的发行版集成该功能：这些发行版总能为其工具链单独加入相关指令支持。
:::

8 月 17 日，lihui
[贴出了](https://lore.kernel.org/loongarch/20230817032825.22974-1-lihui@loongson.cn/)第 3 版的龙架构 <ruby>KGDB<rt>内核 GDB</rt></ruby> 支持补丁，
但 :ta: 把补丁原作者、前龙芯员工 Qing Zhang 的作者信息替换成了 :ta: 自己。
（在补丁内容本身并未发生本质改变的前提下，作者信息不应被替换）。
补丁的原作者 Qing Zhang 在 29 号使用腾讯的邮箱地址[吐槽了](https://lore.kernel.org/loongarch/ea1126e8-690c-424e-ab52-ef5ebbc9707a@tencent.com/)这件事，
截至发稿时尚未有后续进展。
本周报将持续为您追踪。

:::info 吐槽的具体点
一切从一个未被初始化即被使用的变量 `cj_val` 开始：
这意味着这部分代码的作者未作任何测试，甚至连测试编译都没进行，便提交了代码。
且不论这种行为哪怕在任何公司内部，恐怕也不可接受，遑论社区范畴；
作为补丁原作者，看到接手的同学这样对待自己的劳动成果，吐槽两句也合情合理吧。

Qing Zhang 还提到：补丁的本体是 :ta: 编写的，后续修补工作主要是 Binbin Zhou 做的，
并且「不改变作者信息」这件事似乎是 :ta: 从龙芯离职前与 lihui 内部协调过的事项。
我们在没有获得更多消息之前，相信沟通双方本质上都基于善意：也许 lihui
或其部门有什么其他原因导致 :ta: 不能维持原作者信息了。
希望这场小风波能最终平稳解决！
:::

### 工具链

#### gcc

9 月 2 日，Lulu Cheng 依照 [2018 年 RISC-V 的同等优化](https://gcc.gnu.org/git/?p=gcc.git;a=commitdiff;h=7bbce9b50302959286381d9177818642bceaf301)，
也给龙架构[优化了](https://gcc.gnu.org/pipermail/gcc-patches/2023-September/629155.html) `switch` 语句选择支是 8 位量的符号扩展结果时的代码生成。

同一天，guojie [优化了](https://gcc.gnu.org/pipermail/gcc-patches/2023-September/629157.html)向形如 `MEM[base + index]` 的位置存入浮点 `0.0` 这种行为的代码生成。

9 月 1 日，Lulu Cheng 合并了 :ta: 和 guojie 6 月 29 日做的一处代码生成错误修复（GCC bug [PR110484](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=110484)，
在开启 <ruby>LTO<rt>链接时优化</rt></ruby> 编译 SPEC2017 541.leela\_r 用例时发现）。

:::info LTO 是跑分专享优化吗？有用吗？会反而造成性能劣化吗？
简单的回答：不是；有用；不大可能，不必担心。

复杂的回答：取决于具体发行版和软件作者的编译参数选择；取决于具体代码写法；具体情况具体分析。
:::

8 月 31 日，chenxiaolong [提交了](https://gcc.gnu.org/pipermail/gcc-patches/2023-August/628895.html)第 5 版的
128 位浮点操作内建函数支持。

同一天，Lulu Cheng [优化了](https://gcc.gnu.org/pipermail/gcc-patches/2023-August/628950.html)从内存中载入定点数并马上将其转为浮点数的操作：
每次这种情况都省一条指令（不用非得先装进 GPR 再搬进 FPR，可以直接装进 FPR 去）。

#### glibc

8 月 26 日，Xi Ruoyao [清理了](https://sourceware.org/pipermail/libc-alpha/2023-August/151146.html)自从
glibc 要求的最低 binutils 版本抬升至 2.41 之后不再需要的兼容代码。

8 月 24 日，dengjianbo [贴出了](https://sourceware.org/pipermail/libc-alpha/2023-August/151091.html)第二批 LSX、LASX 实现的字符串函数。
随后 28 日 :ta: 又[贴出了](https://sourceware.org/pipermail/libc-alpha/2023-August/151156.html)第三批。

截至发稿时，上述改进都已进入主线了。（感谢 Xi Ruoyao 的线索投递。）

#### LLVM

8 月 31 日，hev 把 [RISC-V 的一项特定有用原子操作优化](https://reviews.llvm.org/D156801)，[移植到了](https://reviews.llvm.org/D159252)龙架构。
这将[有利于 Rust](https://github.com/rust-lang/rust/pull/114034) 生成更好的代码。

同一天，hev 还[优化掉了](https://reviews.llvm.org/D159272)龙架构 LL/SC 操作序列中不必要的栅障动作、修复了其 acquire 语义。

8 月 30 日，SixWeining [优化了](https://reviews.llvm.org/D159183)LLVM intrinsic `llvm.is.fpclass` 的代码生成。

#### Go

（这是前几期遗漏内容的补充）8 月 22 日，Guoqi Chen 领衔的龙芯 Go 团队[贴出了](https://go-review.googlesource.com/c/go/+/521790)龙架构的 <ruby>regabi<rt>寄存器 ABI</rt></ruby> 适配补丁。
笔者在此工作的早期亦有贡献。
此适配完整合并、启用后，将为龙架构的 Go 程序普遍带来 10% 到 20% 左右的性能优化。

## 杂闻播报

8 月 29 日，Xi Ruoyao [修复了](https://sourceware.org/pipermail/elfutils-devel/2023q3/006362.html)
elfutils 项目在龙架构 binutils 2.41 上的测试失败（遗漏了新添加的重定位类型常量）。（感谢 Xi Ruoyao 的线索投递。）

8 月 28 日，Jiajie Chen 为 QEMU TCG 的 LoongArch64 移植添加了向量支持：
可以将被模拟架构的一些 SIMD 操作映射到龙架构的对应 SIMD 操作了。
截至 9 月 2 日，该补丁系列已[迭代至第 3 版](https://patchwork.ozlabs.org/project/qemu-devel/list/?series=371423)。

## 社区整活:儿:

本栏目接受任何网友的投稿，只要内容与 LoongArch 有关，并可以为读者带来价值，
无论严肃贡献（整的大活:儿:）或是博君一笑都一概欢迎！

已知有同学在进行 AMD ROCm 套件的龙架构移植工作了，
也有同学在龙架构上初步跑起了 NixOS。
期待 :ta: 们带着整理好的代码正式出道！:smirk_cat:

笔者仍在不（bǎi）慌（máng）不（zhī）忙（zhōng）继续 3A6000/LA664 的测试工作。
截至目前：

* 证实到了 LA664 二进制翻译加速单元的吞吐提升，
* 发现前几期提到的硬件页表遍历加速单元（PTW）被固件关闭了（可能有 bug 导致不能用），
* 仍未确认到 LA664 相比 LA464 新增的任何指令。

此外，LA664 的 `CPUCFG` 数据中有不少未知位域被置了 `1`，但目前并无任何方法能获知它们的名字与含义。

## 张贴栏

本栏目可供张贴公益性质的各种信息。

* 本周报[持续接受网友投稿][call-for-submissions]。欢迎来上游坐坐！

[call-for-submissions]: https://github.com/loongson-community/areweloongyet/issues/16
