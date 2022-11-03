# 工具介绍和使用(加修复)说明

## rvjit

代码问题不大，但是文档写的一团糟，我给他好好修了文档。在文档里面具体加了些使用说明。这两份文档铁定是中文机翻的，真是一团乱。给文档重构了一番。

## ebpf2rv

还没有改完

2022/10/20

正在试图在rvjit中添加F扩展，但还没有摸清楚该怎么修改新的寄存器（FSCSR），要修改对应的riscv-decode库，此外，M扩和I扩区别属实不大。

    if inst_name in ['csrrw', 'csrrs', 'csrrc', 'csrrwi', 'csrrsi', 'csrrci']:
        return ""

    if inst_name in ['fence', 'fence.i']:
        return ""

考虑到原本就没有处理csr有关的寄存器，我强烈怀疑FSCSR有关的伪指令也是处理不掉的。不过就是有一个好处，文档里面写的是The fcsr register can be read and written with the FRCSR and FSCSR instructions, which are assembler pseudoinstructions built on the underlying CSR access instructions.也就是说可以不用专门处理CSR有关内容，直接放掉也不影响使用。但愿写完后后不会因为它出错吧。

其它的修改还在改。

https://github.com/riscv/riscv-opcodes
从这里查询opcode，我另说一句，相对于这个官方库，现在的做法有些太造轮子了。


2022/10/22
问题在于，真的需要复现吗？ebpf2rv和rvjit都不需要复现唉，只要它们能够起到正常的插入作用，它们就是有效的。

2022/10/26
难搞的地方在于，我不知道该使用哪一个rust版本才能正常运行，甚至连gcc该使用什么版本也不是很确定

第一次寄掉：
make: rcore-fs-fuse: Command not found
Installing rcore-fs-fuse
    Updating git repository `https://github.com/rcore-os/rcore-fs`
warning: spurious network error (2 tries remaining): failed to connect to github.com: Connection refused; class=Os (2)
warning: spurious network error (1 tries remaining): failed to connect to github.com: Connection refused; class=Os (2)
error: failed to fetch into: /home/phlilp_alapa/.cargo/git/db/rcore-fs-7fdf258332f6146d

Caused by:
  failed to connect to github.com: Connection refused; class=Os (2)
make: *** [Makefile:277: rcore-fs-fuse] Error 101

我现在挂了梯子，但是wsl貌似不是很认我的梯子。
wsl虽然认我的梯子
PING github.com (127.0.0.1) 56(84) bytes of data.
64 bytes from localhost (127.0.0.1): icmp_seq=1 ttl=64 time=0.234 ms
64 bytes from localhost (127.0.0.1): icmp_seq=2 ttl=64 time=0.020 ms
64 bytes from localhost (127.0.0.1): icmp_seq=3 ttl=64 time=0.025 ms
64 bytes from localhost (127.0.0.1): icmp_seq=4 ttl=64 time=0.021 ms
64 bytes from localhost (127.0.0.1): icmp_seq=5 ttl=64 time=0.022 ms
64 bytes from localhost (127.0.0.1): icmp_seq=6 ttl=64 time=0.020 ms
64 bytes from localhost (127.0.0.1): icmp_seq=7 ttl=64 time=0.022 ms
本地反代现场

修复说明，挂VPN，不要挂代理，代理会遭443。但VPN就很正常，令人感慨。
我当时采用的是steam++带的代理

失败记录二：rcore-user里面没有记录当前适用的版本！

在make文档里面注意最后一行
error: "/home/phlilp_alapa/.rustup/toolchains/nightly-2020-05-01-x86_64-unknown-linux-gnu/lib/rustlib/src/rust/Cargo.lock" does not exist, unable to build with the standard library, try:
        rustup component add rust-src
它实际上指定了一个rust版本-nightly-2020-05-01
但是，我们现在rcore作业要求的是最新版本，它是rustc 1.66.0-nightly (c97d02cdb 2022-10-05)
git clone https://github.com/rcore-os/rcore-fs.git
Failed to connect to github.com port 443: Connection refused

为了解决这个问题，我决心直接删除了这个rustup

最逆天的还是这个预编译，这个指定版本号。不知道是哪一行编译做出了这种烂事。

理论上啊，rust是支持迁移的，我从官方文档里面翻到了这个工作该怎么做
https://doc.rust-lang.org/edition-guide/editions/transitioning-an-existing-project-to-a-new-edition.html
但是我没法修。因为我还没有找到如何装低版本的rust，各地文档里面都没有找到。

文档写的也乐：
目前用于操作系统实验开发的 rustc 编译器的版本不局限在 1.46.0 这样的数字上，你可以选择更新版本的 rustc 编译器。但注意只能用 rustc 的 nightly 类型的版本。

但rust如何回滚版本呢？恐怕也只可以去重装了，不知道如何去办。


昨天倒腾的，今天才补上

全麻现场：

有一个名为proc-macro2的库
它的报错实例在这里

error[E0635]: unknown feature `proc_macro_span_shrink`

  --> /home/phlilp_alapa/.cargo/registry/src/github.com-1ecc6299db9ec823/proc-macro2-1.0.47/src/lib.rs:92:30
  
   |
   
92 |     feature(proc_macro_span, proc_macro_span_shrink)

   |                              ^^^^^^^^^^^^^^^^^^^^^^

error: aborting due to previous error

https://github.com/google/OpenSK/issues/549
现在编译出错会报此issue里面的错

https://github.com/dtolnay/proc-macro2/commit/cc97264f9f324ade9b820a1629f379b2cbb6e632
找到这个库，发现是出在这个地方，当时的解决方案是锁版本到1.0.43

但是现在已经是11月2日了，现在的版本已经是1.0.47，考虑其它包的版本依赖，最低不能低于1.0.46.
这个编译错误又一次地发生了...
得集体指定版本

看了另一个文档，没遇见过这个问题
然后我查了另一个报错实例https://substrate.stackexchange.com/questions/5379/how-do-i-fix-a-failed-build-error-e0635-unknown-feature-proc-macro-span-shri
它的要求是拉高版本，可是拉高版本后又会发生什么，我不知道。

果然出现新麻烦了
error[E0658]: const generics are unstable
   --> /home/phlilp_alapa/.cargo/registry/src/github.com-1ecc6299db9ec823/volatile-0.4.5/src/lib.rs:686:21
    |
686 | impl<R, A, T, const N: usize> Volatile<R, A>
    |                     ^
    |
    = note: see issue #44580 <https://github.com/rust-lang/rust/issues/44580> for more information
    = help: add `#![feature(const_generics)]` to the crate attributes to enable
   这又是什么东西——原来是21版本的原罪，12月1日修的


不得不升高版本，然后出现了高版本特有毛病：
error[E0658]: use of unstable library feature 'asm': inline assembly is not stable enough for use and is subject to change
  --> /home/phlilp_alapa/.cargo/registry/src/github.com-1ecc6299db9ec823/x86_64-0.14.10/src/registers/xcontrol.rs:57:9
   |
57 |     use core::arch::asm;
   |         ^^^^^^^^^^^^^^^
   |
   = note: see issue #72016 <https://github.com/rust-lang/rust/issues/72016> for more information
   = help: add `#![feature(asm)]` to the crate attributes to enable
但另一位同学的解决方法是：
因为想用 asm! 取代 llvm_asm! 所以把整个依赖以及 toolchain 胡乱升级了一遍。 最后到了 shell 出现提示符，但是不能输入内容，现在还在顺着 async 链来找是哪出了问题。

我疑惑究竟发生了什么
