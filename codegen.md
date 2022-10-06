# RvJIT Code Generation Template

*This document is only for developer*

# Templates

RvJIT utilizes a template (templates) to generate codes and test suits. Additional support for extra instruction sets could be generated via template(s).

Templates are located at `script` folder. `generate.py` focus on generating each instruction, while `lib.py` generates test suits. Those text(`*.txt`) files are templates.

You can see that the(se) template(s) general(ly) follows(follow) the form of lines of instructions. Each instruction should at least(No need these two words) describe its name and type in the last two columns. Other necessary informations(information) include(s) `funct3` (and) `funct7`, registers are also required to (be) provided.

It's a pity that there's no standard template for every instruction set so there needs to be some modification before you could create your own template. In `generate.py`, generating functions are provided so that you could modify them to fit into your own code templates. Then, modify `lib.py` to generate the corresponding test suits. Testing is done using the `riscv-decode` crate.

It's a pity that we have not yet make this tool available for every instrution set. If you want to make your own template, you should make your own function to translate your instrution into machine code.

You can look at how we use funtions like `gen_rv32i_inst` in `generate.py`. In these functions ,we generate Rust functions from riscv instrution by split these instrutions into func_signature and func_body (may with some extar paraments).

In `lib.py`, we generate test suits, you can simply modified that in the same way.

<!-- This paragraph is too hard to be read... -->

It's not a good idea to modify those old templates (at least)(expect) there's mistake found in that.

# Tools

There're few functions you could use to generate immediate number or target type instructions. In `types.rs`, functions are provided to construct instructions with given type. All you need (to do) is to call these functions and fill in fields.