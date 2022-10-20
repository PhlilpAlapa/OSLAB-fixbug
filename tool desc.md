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
