---
title: 实验一 语音信号 MFCC 特征提取
date created: 2022-09-16 20:40:52
date modified: 2022-11-13 20:05:26
---

# 实验一 语音信号 MFCC 特征提取

## 1.wav 实验各阶段结果分析

1. 信号提取及预加重

   - ```matlab
     %提取信号、预加重
     [x,Fs]=audioread('1.wav');
     x=filter([1 -0.97],1,x);%通过预加重滤波器

 - ![image-20220916204814587](/Users/jinqiao/Library/Application Support/typora-user-images/image-20220916204814587.png)
   - 从图中可以看出预加重明显地放大了高频部分预，得以
	 - 平衡频谱，因为高频通常比低频小
	 - 避免傅立叶变换操作期间出现数值问题
	 - 还可以提高信噪比（SNR）

2. 分帧与加窗

   - ```matlab
     fs=Fs/1000;%1ms的采样数
     wlen=25*fs;%帧长
     inc=10*fs;%帧移
     overlap=wlen-inc;%每帧重叠部分
     nf=fix((length(x)-overlap)/inc);%帧数
     win=0.54-0.46*cos(2*pi*(1:wlen)/(wlen-1));%窗函数
     y=enframe(x,win,inc);%分帧并加窗

 - ![image-20220916205442738](/Users/jinqiao/Library/Application Support/typora-user-images/image-20220916205442738.png)
   - 可以看出加窗后分帧信号的高频分量有明显的衰减

3. 梅尔滤波器、DCT

   - ```matlab
     bank=melbankm(40,512,Fs,0,0.5,'t'); %梅尔滤波器的阶数为40，FFT变换的长度为512
     bank=full(bank);%稀疏矩阵转为正常矩阵
     bank=bank/max(bank(:));%归一化
     dctcoef=zeros(13,40);%余弦变换系数矩阵
     for k=1:13
         n=0:39;
         dctcoef(k,:)=cos((2*n-1)*k*pi/(2*40));
     end
     ```
   - ![image-20220916210021742](/Users/jinqiao/Library/Application Support/typora-user-images/image-20220916210021742.png)
   - 用 voicebox 中的 melbankm 函数设置梅尔滤波器组，阶数设为 40，最高频率设为采样频率的一半，采用三角窗。归一化并变换滤波器组的存储形式，以便后续进行矩阵计算。
   - 余弦变换系数矩阵直接套用公式两层 for 循环可得。

4. 频谱、MFCC 参数

   - ```matlab
     fourier=zeros(512,nf);
     fbank=zeros(nf,40);
     mfcc=zeros(13,nf);
     for i=1:nf
         y1=y(i,:);%取出一帧信号
         Y=abs(fft(y1,512));%FFT快速傅里叶变换
         fourier(:,i)=Y;
         P=Y'.^2/512;%取平方求功率
         fbank(i,:)=bank*P(1:257);%频谱通过梅尔滤波器
         mfcc(:,i)=dctcoef*10*log(fbank(i,:)');%对数运算和DCT
     end
     ```
   - 先初始化 fourier、fbank、mfcc 三个矩阵，分别用于存放信号频谱，梅尔滤波后的功率谱及 MFCC 参数。而后进行分帧帧数 nf=584 次 for 循环，每次取出之前加窗后的一帧，依次进行 fft 快速傅里叶变换、求功率谱、过梅尔滤波器、求 MFCC 参数，并依次存放。由于语音信号为实矩阵，所以 fft 结果共轭对称，功率谱及频谱只取一半即可。
   - 频谱![image-20220916211700885](/Users/jinqiao/Library/Application Support/typora-user-images/image-20220916211700885.png)
	 - 可以看出频谱图中颜色较亮的部分与初始的语音信号高频部分对应良好。
   - fbank![image-20220916211739404](/Users/jinqiao/Library/Application Support/typora-user-images/image-20220916211739404.png)
	 - 经过梅尔滤波器后高频分量明显减少
   - mfcc![image-20220916211751481](/Users/jinqiao/Library/Application Support/typora-user-images/image-20220916211751481.png)
	 - 可以看出 mfcc 参数在频率很低时值很小，频率增大一点后达到峰值，而后振荡式分布
	 - 三维图![image-20220916212537220](/Users/jinqiao/Library/Application Support/typora-user-images/image-20220916212537220.png)

   ## 2.wav 运行耗时

   ![image-20220916222527763](/Users/jinqiao/Library/Application Support/typora-user-images/image-20220916222527763.png)

   - 使用 MATLAB 自带的 tic/toc 计时，用时 4s。

   ## fbank 与 mfcc 比较

   - fbank 特征更多是希望符合声音信号的本质，拟合人耳的接收特性。DCT 是线性变换，会丢失语音信号中原本的一些高度非线性成分。
   - 在深度学习之前，受限于算法，mfcc 配 GMMs-HMMs 是 ASR 的主流做法。当深度学习方法出来之后，由于神经网络对高度相关的信息不敏感，mfcc 不是最优选择，经过实际验证，其在神经网络中的表现也明显不如 fbank。
