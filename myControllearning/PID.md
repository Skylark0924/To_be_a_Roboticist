## PID

### 公式：

![{\mathrm  {u}}(E:\Academic_information\To_be_a_Roboticist\6d0715de877e00cd15a9231f9182c17d9869ed1c.svg)={\mathrm  {MV}}(t)=K_{p}{e(t)}+K_{{i}}\int _{{0}}^{{t}}{e(\tau )}\,{d\tau }+K_{{d}}{\frac  {d}{dt}}e(t)](https://wikimedia.org/api/rest_v1/media/math/render/svg/6d0715de877e00cd15a9231f9182c17d9869ed1c)

其中

![K_{p}](E:\Academic_information\To_be_a_Roboticist\81d33ac9af047e93746da20160e9952cdfad0d17.svg)：比例增益，是调适参数

![K_{i}](E:\Academic_information\To_be_a_Roboticist\719736a1feb0bd7e73bb1425641a61229f55bb6d.svg)：积分增益，也是调适参数

![K_{d}](E:\Academic_information\To_be_a_Roboticist\af467d41e25cebc9640cba0240778eaae4057181.svg)：微分增益，也是调适参数

![e](E:\Academic_information\To_be_a_Roboticist\cd253103f0876afc68ebead27a5aa9867d927467-1539439536328.svg)：误差=设定值（SP）- 回授值（PV）

![t](E:\Academic_information\To_be_a_Roboticist\65658b7b223af9e1acc877d848888ecdb4466560.svg)：目前时间

![\tau ](E:\Academic_information\To_be_a_Roboticist\38a7dcde9730ef0853809fefc18d88771f95206c.svg)：积分变数，数值从0到目前时间![t](E:\Academic_information\To_be_a_Roboticist\65658b7b223af9e1acc877d848888ecdb4466560.svg)

![img](E:\Academic_information\To_be_a_Roboticist\006y3QVegw1farbz0sa0nj318k0ki77i.jpg)



**==PID控制器的比例单元P、积分单元I和微分单元D分别反映的是当前误差、过去误差及未来误差。==**



### 比例控制 P ：

​        比例控制就好比是通过水桶往水缸加水或者从水缸舀水。假设我们需要把水平面稳定在A平面，而实际水平面在B平面，那么水平面差值Err=A-B，那这个时候我们需要往里面加水的量就是Kp*Err，Kp就是我们的比例控制系数。
​        如果A>B，Err为正，就往水缸里面加水；如果A<B，Err为负，就从水缸里面舀水出来。那么只要预期水平面和实际水平面有差值，我们都会通过水桶去加减水来调整系统。同时Kp的大小也有对系统的性能有影响。如果Kp的值比较大，优点是从B平面达到A平面的速度快，缺点是在B平面已经接近A平面的时候系统会产生比较大的震荡。如果Kp的值比较小，优点是B平面在接近A平面的时候系统震荡小，缺点是从B平面达到A平面的速度慢。
​        这里也许有人会有疑问，如果这里把比例控制系数Kp直接设置成1，然后加水的量直接为Err=A-B不就可以了。然而实际上很多系统是做不到这点的。比如温度控制系统，实际温度为10度，我要通过加热把温度提升到40度，这里难道我们能一次性准确的给系统加30度？显然这是做不到的。那么比例控制的最终结果是Err的值趋向于0。比例控制部分公式即为：

$$K_pe(t)$$



比例控制是一种最简单的控制方式。**其控制器的输出与输入误差信号成比例关系。**

若比例增益大，在相同误差量下，会有较大的输出，但若比例增益太大，会使系统不稳定。相反的，若比例增益小，若在相同误差量下，其输出较小，因此控制器会较不敏感的。若比例增益太小，当有干扰出现时，其控制信号可能不够大，无法修正干扰的影响。

![不同微分增益Kp下，受控变数对时间的变化 (E:\Academic_information\To_be_a_Roboticist\006y3QVegw1farcblu8ulj30gf0cq74v.jpg)](https://ww1.sinaimg.cn/large/006y3QVegw1farcblu8ulj30gf0cq74v.jpg)

​                          不同微分增益Kp下，受控变数对时间的变化 （$K_i$和$K_d$保持定值）

在无人机上，比例增益$K_p$反映了飞手在操控遥控器摇杆时，飞机响应控制的快慢。

当P增益足够大时，飞机会很高效地响应打杆动作；而如果P增益太小，飞机的响应幅度就会不足。（此处指的并不是响应延迟）

然而，过大的P增益也会带来一个严重的问题，飞机会在飞行时高频率地振动。原因就是，飞机响应的力度过大，总是转动过头，因而反复调整修正误差。



### 积分控制 I :

在积分控制中，**控制器的输出与输入误差信号的积分成正比关系。**

积分控制会加速系统趋近设定值的过程，并且消除纯比例控制器会出现的稳态误差。积分控制会累计过去所有的误差，积分增益越大，趋近设定值的速度越快，但可能会使**回授值出现过冲**的情况。

![不同微分增益Ki下，受控变数对时间的变化 (E:\Academic_information\To_be_a_Roboticist\006y3QVegw1farcdqnbovj30cx0a7q3q.jpg)](https://ww4.sinaimg.cn/large/006y3QVegw1farcdqnbovj30cx0a7q3q.jpg)

​                         不同微分增益$K_i$下，受控变数对时间的变化 （$K_p$和$K_d$保持定值）

### 微分控制 D :

在微分控制中，**控制器的输出与输入误差信号的微分（即误差的变化率）成正比关系**，反映了被控量变化的趋势。

![不同微分增益Kd下，受控变数对时间的变化 (E:\Academic_information\To_be_a_Roboticist\006y3QVegw1farcf07cdxj30cx0a8dgl.jpg)](https://ww2.sinaimg.cn/large/006y3QVegw1farcf07cdxj30cx0a8dgl.jpg)

​                           不同微分增益$K_d$下，受控变数对时间的变化 （$K_p$和$K_i$保持定值）

在无人机上，D的作用是增加飞机飞行的平稳度。D检测着飞机对你的每一个操作响应时出现的误差，如果这个误差是快速较小的，那么D就会减小飞机矫正误差的力度，从而磨平飞机的振动。

如果D增益过小，那无论P增益和I增益设置得多么完美，飞机依然会在飞行中表现出轻微的振动；但如果D增益过大，就会严重影响到飞机的响应速度，出现深陷泥潭的感觉。此外，过高的D增益值也会使电机过热，甚至烧坏电机。



### PID的精髓！

![此图是PID的精髓！](E:\Academic_information\To_be_a_Roboticist\006y3QVegw1farcgd3vung30b408cdqp-1539443416132.gif)

