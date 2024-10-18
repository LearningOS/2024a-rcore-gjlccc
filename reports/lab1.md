# 1.

感觉我是实现的应该是很错啊，但是他过了。

在mod.rs 里建了个全局数组，用来记录函数调用次数，

sys_task_info 时传进去，然后把要查询的更新了。

然后退出前再置为0.

# 2.



## 简答作业

1. 正确进入 U 态后，程序的特征还应有：使用 S 态特权指令，访问 S 态寄存器后会报错。 请同学们可以自行测试这些内容（运行 [三个 bad 测例 (ch2b_bad_*.rs)](https://github.com/LearningOS/rCore-Tutorial-Test-2024A/tree/master/src/bin) ）， 描述程序出错行为，同时注意注明你使用的 `sbi` 及其版本。

`sbi`版本：`RustSBI` version 0.3.0-alpha.2, adapting to RISC-V SBI v1.0.0

报错信息如下

第一个是

后两个是非法指令，分别尝试调用 `rset` 和`csrr`

1. 深入理解 [trap.S](https://github.com/LearningOS/rCore-Camp-Code-2024A/blob/ch3/os/src/trap/trap.S) 中两个函数 `__alltraps` 和 `__restore` 的作用，并回答如下问题。

   1. L40：刚进入 `__restore` 时，`a0` 代表了什么值。请指出 `__restore` 的两种使用情景。

      a0代表内核栈的栈顶

      使用场景 ：

      1. 用来启动程序
      2. 用来在处理trap后，恢复上下文

   2. L43-L48：这几行汇编代码特殊处理了哪些寄存器？这些寄存器的的值对于进入用户态有何意义？请分别解释。

      ```
      ld t0, 32*8(sp)
      ld t1, 33*8(sp)
      ld t2, 2*8(sp)
      csrw sstatus, t0
      csrw sepc, t1
      csrw sscratch, t2
      ```

      处理了sstatus，sepc，sscratch寄存器，

      sstatus给出trap发生之前cpu在处理哪个特权级的信息

      sepc记录 Trap 发生之前执行的最后一条指令的地址

      sscratch保存 进入 Trap 之前的状态并指向内核栈栈顶

   3. L50-L56：为何跳过了 `x2` 和 `x4`？

      ```
      ld x1, 1*8(sp)
      ld x3, 3*8(sp)
      .set n, 5
      .rept 27
         LOAD_GP %n
         .set n, n+1
      .endr
      ```

      x2指向内核栈

      x4一般不会用到

   4. L60：该指令之后，`sp` 和 `sscratch` 中的值分别有什么意义？

      ```
      csrrw sp, sscratch, sp
      ```

      该指令后sp指向用户栈，sscratch指向内核栈

   5. `__restore`：中发生状态切换在哪一条指令？为何该指令执行之后会进入用户态？

      在sret那一条，因为之前在sstatus中保存了之前的特权级为u，然后就切换了。

   6. L13：该指令之后，`sp` 和 `sscratch` 中的值分别有什么意义？

      ```
      csrrw sp, sscratch, sp
      ```

      该指令后sp指向内核栈，sscratch指向用户栈

   7. 从 U 态进入 S 态是哪一条指令发生的？

      L13行吧



# 荣誉准则

1. 在完成本次实验的过程（含此前学习的过程）中，我曾分别与 **以下各位** 就（与本次实验相关的）以下方面做过交流，还在代码中对应的位置以注释形式记录了具体的交流对象及内容：

   > 陈宏毅：教我怎么用git
   >
   > 陶稼辉：中途我出现不知道某种原因导致的无法运行吧，帮我稍微看了看。

2. 此外，我也参考了 **以下资料** ，还在代码中对应的位置以注释形式记录了具体的参考来源及内容：

   > [第三章：多道程序与分时多任务 - rCore-Camp-Guide-2024A 文档 (learningos.cn)](https://learningos.cn/rCore-Camp-Guide-2024A/chapter3/index.html)
   >
   > [数组 - Rust语言圣经(Rust Course)](https://course.rs/basic/compound-type/array.html)：一下子没想起来数组怎么用了

3. 我独立完成了本次实验除以上方面之外的所有工作，包括代码与文档。 我清楚地知道，从以上方面获得的信息在一定程度上降低了实验难度，可能会影响起评分。

4. 我从未使用过他人的代码，不管是原封不动地复制，还是经过了某些等价转换。 我未曾也不会向他人（含此后各届同学）复制或公开我的实验代码，我有义务妥善保管好它们。 我提交至本实验的评测系统的代码，均无意于破坏或妨碍任何计算机系统的正常运转。 我清楚地知道，以上情况均为本课程纪律所禁止，若违反，对应的实验成绩将按“-100”分计。