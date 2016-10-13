# Lab 3
## 1.Task 1: 修改example2，让3个square模块变成2个：修改xml的iterator，将3改成2
* 改完的*.dot图：

  ![example2](https://github.com/Katelych/ES2016_14353166/blob/master/assignment/lab3_example2.png?raw=true)

* 修改方法：
  ```
  cd dol/examples/example2
  sudo gedit example2.xml
  ```

  然后在这一个文件将变量N的值由原来的3改成2，这样就迭代2次square模块
  
  ```
  <variable value="2" name="N"/>

  <!-- instantiate resources -->
  <process name="generator">
    <port type="output" name="10"/>
    <source type="c" location="generator.c"/>
  </process>

  <iterator variable="i" range="N">
    <process name="square">
      <append function="i"/>
      <port type="input" name="0"/>
      <port type="output" name="1"/>
      <source type="c" location="square.c"/>
    </process>
  </iterator>

  <process name="consumer">
    <port type="input" name="100"/>
    <source type="c" location="consumer.c"/>
  </process>

  <iterator variable="i" range="N + 1">
    <sw_channel type="fifo" size="10" name="C2">
      <append function="i"/>
      <port type="input" name="0"/>
      <port type="output" name="1"/>
    </sw_channel>
  </iterator>

  <!-- instantiate connection -->
  <iterator variable="i" range="N">
    <connection name="to_square">
      <append function="i"/>
      <origin name="C2">
        <append function="i"/>
        <port name="1"/>
      </origin>
      <target name="square">
        <append function="i"/>
        <port name="0"/>
      </target>
    </connection>

    <connection name="from_square">
        <append function="i"/>
        <origin name="square">
          <append function="i"/>
          <port name="1"/>
        </origin>
        <target name="C2">
          <append function="i + 1"/>
          <port name="0"/>
        </target>
    </connection>
  </iterator>

  <connection name="g_">
    <origin name="generator">
     <port name="10"/>
    </origin>
    <target name="C2"> 
      <append function="0"/>
      <port name="0"/>
    </target>
  </connection>

  <connection name="_c">
    <origin name="C2">
      <append function="N"/>
      <port name="1"/>
    </origin>
    <target name="consumer">
      <port name="100"/>
    </target>
  </connection>

  </processnetwork>
  ```
  
* 对以上代码的解读：定义一个变量N=3,然后是生产者进程，类型是output，名字是10，文件类型是c文件，文件是位置是"generator.c"； 然后用迭代器去产生N个square（这里N为2），每个square有两个端口，一个输入一个输出； 消费者进程是输入类型。 然后用迭代器产生N+1条通道，连接各个进程； 最后迭代N次连接好各个组件。
  因原来是N=3，有三个square，现改成2个square的话就直接n=2就好了，不涉及到后面代码的改变。
  
* 以下是实验结果截图：
    
   ![example2_2](https://github.com/Katelych/ES2016_14353166/blob/master/assignment/lab3_example2_result.png?raw=true)
  
  可看出，0，1，2，3，4，……19的平方的平方分别为0，1，16，81，256，130321.实验结果正确。

## 2.Task 2:修改example1，使其输出3次方数：修改square.c，将i=i\*i改成i=i\*i\*i就好了
* 改完的*.dot图：

  ![example1](https://github.com/Katelych/ES2016_14353166/blob/master/assignment/lab3_example1.png?raw=true)
  
* 修改方法：
  进入dol/examples/example1/src,修改square.c文件，将i=i\*i改成i=i\*i\*i，具体代码如下：
  
  ```
  #include <stdio.h>
  #include <string.h>

  #include "generator.h"

  // initialization function
  void generator_init(DOLProcess *p) {
    p->local->index = 0;
    p->local->len = LENGTH;
  }

  int generator_fire(DOLProcess *p) {

    if (p->local->index < p->local->len) {
        float x = (float)p->local->index;
        DOL_write((void*)PORT_OUT, &(x), sizeof(float), p);
        p->local->index++;
    }

    if (p->local->index >= p->local->len) {
        DOL_detach(p);
        return -1;
    }

    return 0;
  }
  ```
  
  因为原本的代码是输出平方，这里要求输出3次方，那就再多乘一个i就好了。
* 实验结果截图：
  
  ![lab3_example1_2](https://github.com/Katelych/ES2016_14353166/blob/master/assignment/lab3_example1_result.png?raw=true)

  可看出0，1，2，3，……19的3次方分别是0，1，8，27，……6859，实验结果正确。

## 实验感想
通过这次实验我懂得了获得管理员权限的指令（输入nau可通过Tab键补全命令）：
 
`sudo nautilus`

这样就可以将被锁住的文件解锁了，也可以随意删除或者编辑了。
还有这次实验更改example之后要重新编译dol再运行例子，我一开始没有注意到所以跑出来的结果还是修改之前的结果，后来重新编译dol就好了。然后如果还跑不了例子的话，就把build文件中之前跑过的例子的那个文件夹删掉，重跑就好了。