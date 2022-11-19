kernel/src/lib.rs的L11应被注释掉，此外部分应当可以编译。

可以注意到rboot文件夹里面没有Cargo.lock，这使得它不能被正确编译。假如有一个正确的Cargo.lock或许可行。

若正确编译，请去郝子胥等的仓库里面找到ebpf2rv和rvjit的模块

如何解决某些经典问题：

error: "/home/phlilp_alapa/.rustup/toolchains/nightly-2020-05-01-x86_64-unknown-linux-gnu/lib/rustlib/src/rust/Cargo.lock" does not exist, unable to build with the standard library, try:
        rustup component add rust-src

请找到rcore的最新教学文档，输入以下内容

rustup target add riscv64gc-unknown-none-elf

cargo install cargo-binutils

rustup component add llvm-tools-preview

rustup component add rust-src

error[E0635]: unknown feature 'proc_macro_span_shrink'
92 | feature(proc_macro_span, proc_macro_span_shrink)
| ^^^^^^^^^^^^^^^^^^^^^^

降低版本即可，假如可以降低的话

简要的说，假如发现feature不对，就调整rustc的版本就行。依赖的版本不对就去lock里面找它的上级依赖，看看是什么把版本锁了。