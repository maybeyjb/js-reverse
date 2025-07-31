
# xd课程：

# 89：逆向

### js

玩js逆向一般都是搞爬虫的



![image-20250526163538120](https://raw.githubusercontent.com/maybeyjb/maybe/main/img/202506112307475.png)

开始演示：

有时候有些数据搜索没有搜到，可能是第一次加载不全的原因，刷新或者退到主页重新进入。这里就根据登录时的数据包里的路由地址全局搜索的。

![image-20250526165057818](https://raw.githubusercontent.com/maybeyjb/maybe/main/img/202506112307476.png)

作用域分为全局和本地，有时还有一个叫闭包的。全局是包含这整个站点的，本地就是只包括当前网页数据。

![image-20250526165338378](https://raw.githubusercontent.com/maybeyjb/maybe/main/img/202506112307477.png)

下来可以就一步一步调试，看作用域里的密码什么时候被加密了。

经典申通

![image-20250527095809886](https://raw.githubusercontent.com/maybeyjb/maybe/main/img/202506112307478.png)

都为加密数据。然后分析其加载过程调用文件：

![image-20250527100036166](https://raw.githubusercontent.com/maybeyjb/maybe/main/img/202506112307479.png)

ajax就是前后端传输用的，所以猜测加密是在传入后端前的，那也就是在ajax前传入的，那么猜测加密算法就在ajax或者login这两个调用堆栈附件。

![image-20250527100823521](https://raw.githubusercontent.com/maybeyjb/maybe/main/img/202506112307480.png)

点击匿名文件，进入js分析，发现调用加密函数：

![image-20250527101325444](https://raw.githubusercontent.com/maybeyjb/maybe/main/img/202506112307481.png)





DOM断点：

![image-20250527152903097](https://raw.githubusercontent.com/maybeyjb/maybe/main/img/202506112307482.png)

然后在

![image-20250527152929608](https://raw.githubusercontent.com/maybeyjb/maybe/main/img/202506112307483.png)

地址断点，这种断下来一般就是在其上面寻找加密的函数。因为断到地址一般加密都已经结束了：

![image-20250527154622373](https://raw.githubusercontent.com/maybeyjb/maybe/main/img/202506112307484.png)

修改返回包也是js逆向里的

# 90:反调试



![image-20250528135718316](https://raw.githubusercontent.com/maybeyjb/maybe/main/img/202506112307486.png)

自带反调试，一打开f12就断下来了。

![image-20250528135907229](https://raw.githubusercontent.com/maybeyjb/maybe/main/img/202506112307487.png)

### 绕过

![image-20250528140541568](https://raw.githubusercontent.com/maybeyjb/maybe/main/img/202506112307488.png)



停用所有的断点，但是你自己也打不了断点了：



![image-20250528140609975](https://raw.githubusercontent.com/maybeyjb/maybe/main/img/202506112307489.png)

#### 局部断点：

![image-20250528140729606](https://raw.githubusercontent.com/maybeyjb/maybe/main/img/202506112307490.png)



停用2处断点，成功绕过



![image-20250528141116316](https://raw.githubusercontent.com/maybeyjb/maybe/main/img/202506112307491.png)

#### 条件断点：

和局部类似：

![image-20250528141516601](https://raw.githubusercontent.com/maybeyjb/maybe/main/img/202506112307492.png)



上面几个还是针对简单的，下面这几种才是常用的：

#### 替换文件执行

在替换处选择一个空文件夹：

![image-20250528143536705](https://raw.githubusercontent.com/maybeyjb/maybe/main/img/202506112307493.png)

然后定位到他反调试打码处，替换内容到我们的文件夹中：

![image-20250528143637371](https://raw.githubusercontent.com/maybeyjb/maybe/main/img/202506112307494.png)

然后将其调试代码删除或者替换即可：

![image-20250528142507570](https://raw.githubusercontent.com/maybeyjb/maybe/main/img/202506112307495.png)

有的需要更改多处调试代码：

![image-20250528142522205](https://raw.githubusercontent.com/maybeyjb/maybe/main/img/202506112307496.png)

最后可以执行调试。

有的不仅仅是js里的Debugger技术，还有监听你的键盘。

![image-20250528144719460](https://raw.githubusercontent.com/maybeyjb/maybe/main/img/202506112307497.png)

这里多按几次f12就出来了，但是里面还是有debug调试代码。

针对关键字搜索，但是没有搜到，这时候就有可能是在代码里进行加密了，或者是可以加载出来，尝试多访问点目录：

![image-20250528153516485](https://raw.githubusercontent.com/maybeyjb/maybe/main/img/202506112307498.png)

查找到关键字调试后，然后找哪个函数方法掉用，发现为endebug，进行

![image-20250528154412037](https://raw.githubusercontent.com/maybeyjb/maybe/main/img/202506112307499.png)

还是替换后，删除endebug ，但是还是不行	

![image-20250528155017598](https://raw.githubusercontent.com/maybeyjb/maybe/main/img/202506112307500.png)

因为这里再php文件中还有一段加密的调试代码。

![image-20250528160716309](https://raw.githubusercontent.com/maybeyjb/maybe/main/img/202506112307501.png)

这也需要替换更改。



#### Burp修改匹配

![image-20250528161252571](https://raw.githubusercontent.com/maybeyjb/maybe/main/img/202506112307502.png)

这是原本的：

![image-20250528161758478](https://raw.githubusercontent.com/maybeyjb/maybe/main/img/202506112307503.png)

然后配置好bp后，开代理：

![image-20250528161538360](https://raw.githubusercontent.com/maybeyjb/maybe/main/img/202506112307504.png)

这边就会发现，成功绕过

![image-20250528161551098](https://raw.githubusercontent.com/maybeyjb/maybe/main/img/202506112307505.png)

有时候也需要将方法置空，所以看网站本身，还要看其流量里的js走不走bp代理。



#### 油管猴插件

本质是写一个js脚本，将endebug替换置空。



# 91：HOOK

注入劫持--hook，

![image-20250531144407960](https://raw.githubusercontent.com/maybeyjb/maybe/main/img/202506112307506.png)



在浏览器的控制台中执行自己的代码调试实现hook。



![image-20250531154512511](https://raw.githubusercontent.com/maybeyjb/maybe/main/img/202506112307507.png)

js-hook脚本编写规则。
