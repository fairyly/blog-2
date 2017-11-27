# Linux Command : chmod

## 改变文件或目录的权限

普通用户只能修改自己的文件的权限位,超级用户可以使用 chmod 修改任意用户任意文件的权限。
chmod 命令有两种方式改变文件或目录的权限。

1. 符号类型改变权限
   - 格式 : chmod [OPTION]... MODE[,MODE]... FILE...
     - MODE : [ugoa...][[-+=][perms...]...]
       - ugoa
         - u(owner) : 文件属主， g : 文件属组， o : 其他用户 (u，g 之外的)， a : 所有用户 (当不指定任何一个的时候，a 是默认值)
       - -+=
         - = : 赋值权限， + : 增加权限， - : 删除权限
       - perms : rwxst
         - r : 读权限
           - 文件 : 读文件的内容
           - 目录 : 列出该目录下所有的文件和子目录，即可以使用 ls 命令
         - w : 写权限
           - 文件 : 新增, 修改, 删除文件内容
           - 目录 : 增加、删除、或者重命名(即移动文件)该目录下的文件或目录，即可以使用 mkdir, rm, mv 命令。
         - x : 执行权限
           - 文件 : 执行文件
           - 目录 : 进入目录的权限，即可以使用 cd 命令。
           * ※ x 对应的位置（执行位）还可能是其它字符。
             - 对于文件属主 (u) 或 文件属组 (g) 的执行位
               - 若为 s，表示该文件的 set-user-id 或者 set-group-id 和 执行权限 x 被同时置位。
               - 若为 S，表示该文件的 set-user-id 或者 set-group-id 被置位，而执行权限 x 不被置位。
               * ※ set-user-id，set-group-id 参考下面
             - 对于其他用户(o) 的执行位
               - 若为 t，表示该文件的粘滞位（sticky bit）和 执行权限 x 均被置位。
               - 若为T，表示粘滞位被置位，而执行权限 x 不被置位。
             - 例子
               ```
               # [-wrx------] => [-wrs------]
               $ chmod u+s file

               # [-wr-------] => [-wrS------]
               $ chmod u+s file

               # [-wrs------] => [-wrS------]
               $ chmod u-x file

               # [-wrs------] => [-wrx------]
               $ chmod u-s file
               ```
   - 例子
     ```
     $ chmod u+x file # 给文件属主加执行权限
     $ chmod u=rwx,g=rx,o=x file # 给文件属主加读写执行权限，给文件属组加读执行权限，给其他用户加执行权限
     $ chmod =r file # 给所有用户读权限
     $ chmod a=r # 等价于以上
     $ chmod a-wx,a+r file # 给所有用户减去写和执行权限，给所有用户加读权限
     $ chmod -R u+r directory # 递归 directory 的子文件或目录加读权限
     ```

2. 数字类型改变权限
   - 格式 : chmod [OPTION]... OCTAL-MODE FILE...
     - OCTAL-MODE (八进制数)
       - 由 4 个 八进制数组成，每个八进制数的值的范围是 0 ~ 7
       - 开头的 0 可以省略，例子 : 777 等价于 0777, 77 等价于 0077
       - 八进制数是如下方式计算得来
         - 第 1 个八进制数的权限与数字的关联 : set-user-id : 4，set-group-id : 2， t : 1
         - 其余 3 个八进制数的权限与数字的关联 : w = 4, r = 2, x = 1
       - 例子
         - [-rwxr-xr--] 的八进制数为 754 (owner = 5, group = 5, other = 4)
         - [-rwsrwSr-T] 的八进制数为 7764
   - 例子
     ```
     # 使 test_file 的权限为 [-rwxr-xr--]
     $ chmod 754 test_file
     ```

## set-user-id, set-group-id

- user-id, group-id (一个用户有这两个 id)
  这两个 id 被保存在 /etc/passwd 文件中，创建用户时被写入，每当用户登录时由登录程序来读取该文件，并设定相应用户的 id，OS 以用户的用户 id 和组来判断你是否具有相应的权限来执行特定的操作。
  - user-id (对于一个用户唯一的)
  - group-id (是默认组 id : 用户创建的文件默认隶属的组 (一个用户可以隶属于多个不同的组))

- real-user-id, real-group-id  
   指的是执行程序的用户 (process owner)，即登录 OS 的用户
  - real-user-id, real-group-id 登录 OS 的用户的 user-id, group-id
    ```
    real-user-id = user-id (process owner) 
    real-group-id = group-id (process owner)
    ```

- effective-user-id, effective-group-id  
  - effective-user-id, effective-group-id 是对于一个被执行的程序（进程）来说的。当用户执行一个程序时有两种真实存在的用户 : 1. 执行该程序的用户 : process owner，2. 该程序文件属主 : file owner
    - 通常情况用户登录 OS 后
      ```
      effective-user-id = real-user-id
      effective-group-id = real-group-id
      ```
    - 但当该程序文件的 set-user-id 或者 set-group-id 被置位时
      ```
      effective-user-id = user-id (file owner)
      effective-group-id = group-id (file owner)
      ```
   - effective-user-id, effective-group-id 的作用
     在一个进程试图去访问一种资源时，比如读、写某个文件，OS（内核）会判断此进程对该文件是否具有相应的权限。判断的依据就是 effective-user-id, effective-group-id。
     即通过判断 effective-user-id, effective-group-id 跟资源文件的 user-id，和 group-id 是否匹配来获得相应的读写执行(rwx) 权限。

## 参考

- [[命令技巧]chmod & Set-User-ID & Set-Group-ID](http://blog.csdn.net/pi9nc/article/details/14139465)
- [chmod----改变一个或多个文件的存取模式(mode)](https://www.cnblogs.com/younes/archive/2009/11/20/1607174.html)
- [Linux文件属性及如何修改文件属性](https://www.cnblogs.com/Linux--rookie/p/6662367.html)

