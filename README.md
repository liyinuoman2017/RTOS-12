# RTOS-12
嵌入式实时操作系统12——任务间信息交互
**任务间信息交互的动态图：**

![请添加图片描述](https://img-blog.csdnimg.cn/e4186dfe450d42e3886276515c80c079.gif)


## 1.任务间信息交互是什么

操作系统中每一个任务都是一个独立自主的小程序。任务的典型形式如下：

![在这里插入图片描述](https://img-blog.csdnimg.cn/e6ec3f4e59724dc48f65c80a01e905bb.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGl5aW51bzIwMTc=,size_19,color_FFFFFF,t_70,g_se,x_16)

**从形式上看任务可以在自己的资源空间中独立运行，事实上只有小部分任务完全独立于其它任务运行，大部分任务都是需要相互之间进行耦合，共同实现一个设计需求**。

这就和人类社会一样，大部分人和他身边的人都有着千丝万缕的联系，并生活在这个关系网中，只有极少数隐士选择与世人隔离。

![在这里插入图片描述](https://img-blog.csdnimg.cn/a59cea14dc2d410cb0844aed939211d0.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGl5aW51bzIwMTc=,size_20,color_FFFFFF,t_70,g_se,x_16)

任务和任务之间有哪些联系呢？我们举例说明一下：
1、按键任务检测到按键被按下后，需要通知显示任务切换显示界面。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20e7c9c812534845afb1674f957cef5a.png)

2、传感器读取任务更新测量数据后需要进行数据打印，此时任务需要判断打印信息任务是否被占用，如果打印信息任务未被占用则进行数据打印。

![在这里插入图片描述](https://img-blog.csdnimg.cn/623141028de540a19612d2d41ca46c87.png)

3、WIFI任务收到数据后，需要向显示任务发送数据，显示任务根据数据刷新显示界面。

![在这里插入图片描述](https://img-blog.csdnimg.cn/80c7f0f6eb564caeafcdf5477444f2f1.png)

上面涉及到的这些都属于任务间信息交互，不同的操作系统中有着不同的任务间信息交互方式，常用的任务间信息交互分为以下3类：

> 1、同步 
> 2、互斥 
> 3、收发消息

## 2.同步

为了实现多任务之间的合作运行，需要在任务间建立一些关联让多任务之间按照先后顺序执行，**这种关联机制叫做同步**。

**这种机制就像我们生活中交通红绿信号灯**。汽车停在红绿信号灯路口，当红绿信号灯变成绿灯时，汽车启动并通过路口。这种行为逻辑并不是红绿信号灯亮的时候通过光电效应触发汽车的油门让汽车启动，而是因为司机看到了红绿信号灯变为绿灯，司机踩下油门启动汽车通过路口。**司机看到红绿信号灯变为绿灯，随后踩下油门这一组动作就是同步**。

![在这里插入图片描述](https://img-blog.csdnimg.cn/936966e51e3b40b890fcaf077901ceaa.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGl5aW51bzIwMTc=,size_17,color_FFFFFF,t_70,g_se,x_16)

通过同步机制可以实现多个任务合作按照顺序运行，操作系统中通常使用信号量实现任务同步。

例如任务A需要等待信号甲再执行一个特定操作，当信号甲为“无效”时任务A进入休眠状态，操作系统让任务B运行，假设任务A优先级高于任务B,当任务B将信号甲设置为“有效”的那一瞬间，操作系统会暂停任务B运行任务A，使得任务A的特定动作得以执行。机制运行图如下：

![在这里插入图片描述](https://img-blog.csdnimg.cn/9c1cbd30aa884aa8b2fa62a991c743ae.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGl5aW51bzIwMTc=,size_19,color_FFFFFF,t_70,g_se,x_16)

## 3.互斥

一些情况下，某些资源在“同一时间”只能被一个任务使用，当这些资源被某个任务正在使用时，其它准备使用这些资源的任务，只能等待资源使用者放弃使用权后才能使用该资源，这种机制叫**互斥**。

互斥机制就像我们在家里使用卫生间一样，每天早上一家人都起床了，某个人占用了卫生间，其他人就只能等待该使用者离开卫生间后，才能使用卫生间。这种情况由时候会非常痛苦，所以建议大家好好学习提高收入，买个有多卫的大房子。

![在这里插入图片描述](https://img-blog.csdnimg.cn/5ff87690d2114785961ffb08ec8689b0.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGl5aW51bzIwMTc=,size_18,color_FFFFFF,t_70,g_se,x_16)

互斥机制应用在同一个资源被多个任务操作的情形中，互斥机制有效的解决了多个任务共享资源而产生的竞争问题，使得共享资源能够依次让每个任务“独享”这个共享资源。**互斥机制有一个重要的特点就是：先用先得，低优先级任务先使用时，高优先级任务希望使用也必须等待。**

例如现在有两个优先级相同的任务，操作系统采取时间片轮询的方法调度。我们需要用这两个任务以9600的波特率输出信息，任务A输出“i love rtos”，任务B输出“i love money”。由于两个任务共享了一个串口设备出现了竞争现象，导致实际输出了“i lovi lovee monemnoeyy”这串错误的信息。

使用互斥机制就可以避免这种情况，任务A和任务B都能在它们使用共享资源的时候“独享”这个共享资源，而不被人打扰。互斥机制运行图如下：

![在这里插入图片描述](https://img-blog.csdnimg.cn/b966f7fe329c42b0be6cb63f07f7be5f.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGl5aW51bzIwMTc=,size_16,color_FFFFFF,t_70,g_se,x_16)

## 4.收发消息

在使用同步和互斥机制时，任务只需要关注同步和互斥是否有效，而有些情况下任务不仅需要有同步的效果，**还要关注信息机制本身携带的数据**，这时候就需要用到消息队列机制。消息队列可以实现多个任务之间的数据信息通信。

消息队列机制就像我们正在回家的路上，老妈给你发了一个消息“家里酱油用完了，回来的时候顺路买瓶酱油”，这时候你需要按照要求先去超市买一瓶酱油，然后在回家，**打酱油就这样被完成了。**

![在这里插入图片描述](https://img-blog.csdnimg.cn/0af5e4ff17b0484cb9a5277604afc9b4.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGl5aW51bzIwMTc=,size_18,color_FFFFFF,t_70,g_se,x_16)

消息队列机制可以让多个任务之间进行丰富的数据交互，可以让多个任务组合成一个功能丰富的系统。

例如有一个物联网设备平台同4G信号与设备通信，收发4G数据的任务收到数据后，将数通过消息队列发送给逻辑处理任务，逻辑处理任务解析数据后产生指令，然后在利用消息队列将指令分发给其他任务。消息队列机制运行图如下：

![在这里插入图片描述](https://img-blog.csdnimg.cn/1f71731ff74b4abdb7c0b02067d05366.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAbGl5aW51bzIwMTc=,size_19,color_FFFFFF,t_70,g_se,x_16)


## 5.任务间信息交互机制

任务间交互为操作系统提供了多任务之间的合作机制，同步机制实现了控制多任务执行顺序，互斥机制实现了多任务共享资源时不会出现恶性竞争，收发消息实现了多任务之间的数据传递。
任务间信息交互有以下3个特点：

> 1、建立多任务之间的关联，保证多任务之间实现复杂的合作
> 2、保证操作系统运行效率，当任务需要等待信息时，操作系统会让该任务进入等待状态，并运行其他任务
> 3、保证操作系统实现性，当信息被当前任务设置成“有效”时，操作系统会暂停当前任务，运行等待该信息的高优先级任务。

使用一个动态图展示任务间信息交互：

![请添加图片描述](https://img-blog.csdnimg.cn/b0d413ce3478418aa2cf78f07da02704.gif)





> 
> 实时操作系统系列将持续更新…
> 
> 创作不易希望朋友们点赞，转发，评论，关注。
> 
> 您的点赞，转发，评论，关注将是我持续更新的动力
> 
> 作者：李巍
> 
> Github：liyinuoman2017
> 
> CSDN：liyinuo2017
> 
>今日头条：程序猿李巍
