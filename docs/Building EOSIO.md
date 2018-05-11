##编译 EOSIO

    编译EOSIO是通过运行一个自动化脚本完成的。编译工具的信息主要是存放在eos/build文件夹中。生成的可执行文件可以在 eos/build/programs文件夹中找到。

###自动化编译脚本
     
    自动化编译脚本首先会检查和安装需要的依赖库，然后进行编译EOSIO。 脚本支持如下所示的操作系统，我们会在将来的版本中支持其他的Linux/Unix版本。

     1.Amazon 2017.09 and higher.
     2.Centos 7.
     3.Fedora 25 and higher (推荐Fedora 27).
     4.Mint 18.
     5.Ubuntu 16.04 (推荐Ubuntu 16.10).
     6.MacOS Darwin 10.12 and higher (推荐MacOS 10.13.x).

###系统需求（针对所有系统）

    •需要8GB  内存
    •需要20GB 磁盘空间

###运行脚本
    
    在eos文件夹中运行脚本,命令如下：
        cd eos
        ./eosio_build.sh

###编译验证
    
    我们可以通过一套测试程序进行一些编译结果的验证。 为了在编译结束之后进行整套测试，需要先启动 mongod再启动测试。
    首先执行：
       在Linux平台:
           ~/opt/mongodb/bin/mongod -f ~/opt/mongodb/mongod.conf &
       在 MacOS:
           /usr/local/bin/mongod -f /usr/local/etc/mongod.conf &
    然后执行如下命令（所有平台）:
        cd build
        make test

###安装可执行文件

    为了简化合约开发，其内容可以通过使用 make install 命令 存放在/usr/local 文件夹中。 这个步骤中命令是在编译文件夹中运行。 因为需要做够的权限来执行安装命令，因此我们使用sudo命令获取权限来执行。
        cd build
        sudo make install
 