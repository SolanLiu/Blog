---
layout: post
#标题配置
title:  Image Segmentation
#时间配置
date:   2019-02-03 01:08:00 +0800
#大类配置
categories: notes
#小类配置
tag: Image Algorithm
---

* content
{:toc}





# 图像分割

&emsp;&emsp;所谓图像分割指的是根据灰度、颜色、纹理和形状等特征把图像划分成若干互不交迭的区域，并使这些特征在同一区域内呈现出相似性，而在不同区域间呈现出明显的差异性。

## 1. 基于阈值的分割方法

&emsp;&emsp;阈值法的基本思想是基于图像的灰度特征来计算一个或多个灰度阈值，并将图像中每个像素的灰度值与阈值相比较，最后将像素根据比较结果分到合适的类别中。因此，该类方法最为关键的一步就是按照某个准则函数来求解最佳灰度阈值。  
&emsp;&emsp;全局阈值适合前景灰度值相对集中，光照均匀的图像中。通过设置一个适用于整个图像范围的阈值进行分割。有时候，图像受光照不均的影响，使得待分割的目标区域灰度变换很大，全局阈值不再适用，这时需要通过设置窗口范围，对每个窗口区域使用局部阈值进行分割，最后再将所有局部的分割结果进行融合。利用阈值进行分割，最重要的是阈值的选择。简单图像可以通过固定阈值进行分割，而自适应阈值则介绍以下几种方法：直方图阈值选取、迭代阈值选取、最大类间方差阈值（Ostu）。   

- **Otsu法（最大类间方差法）**

&emsp;&emsp;该算法是日本人Otsu提出的一种动态阈值分割算法。它的主要思想是按照灰度特性将图像划分为背景和目标两部分，划分依据为选取门限值，使得背景和目标之间的方差最大。其主要的实现原理如下：  
&emsp;&emsp;1）建立图像灰度直方图（共有L个灰度级，每个出现概率为p）  
 
&emsp;&emsp;<a href="https://www.codecogs.com/eqnedit.php?latex=N&space;=&space;\sum^{L-1}_{i=0}n_i,&space;p_i&space;=&space;\frac{n_i}{N}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?N&space;=&space;\sum^{L-1}_{i=0}n_i,&space;p_i&space;=&space;\frac{n_i}{N}" title="N = \sum^{L-1}_{i=0}n_i, p_i = \frac{n_i}{N}" /></a> 

&emsp;&emsp;2）计算背景和目标的出现概率，计算方法如下：
  
&emsp;&emsp;<a href="https://www.codecogs.com/eqnedit.php?latex=p_A&space;=&space;\sum^t_{i=0}p_i,&space;p_B&space;=&space;\sum^{L-1}_{i=t&plus;1}p_i&space;=&space;1-p_A" target="_blank"><img src="https://latex.codecogs.com/gif.latex?p_A&space;=&space;\sum^t_{i=0}p_i,&space;p_B&space;=&space;\sum^{L-1}_{i=t&plus;1}p_i&space;=&space;1-p_A" title="p_A = \sum^t_{i=0}p_i, p_B = \sum^{L-1}_{i=t+1}p_i = 1-p_A" /></a>

上式中假设t为所选定的阈值，A代表背景（灰度级为0~N）,根据直方图中的元素可知，Pa为背景出现的概率，同理B为目标，Pb为目标出现的概率。  
&emsp;&emsp;3）计算A和B两个区域的类间方差如下：  

&emsp;&emsp;<a href="https://www.codecogs.com/eqnedit.php?latex=\omega_A&space;=&space;\sum^t_{i=0}i\frac{p_i}{p_A},&space;\omega_B&space;=&space;\sum^{L-1}_{i=t&plus;1}i\frac{p_i}{p_B}" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\omega_A&space;=&space;\sum^t_{i=0}i\frac{p_i}{p_A},&space;\omega_B&space;=&space;\sum^{L-1}_{i=t&plus;1}i\frac{p_i}{p_B}" title="\omega_A = \sum^t_{i=0}i\frac{p_i}{p_A}, \omega_B = \sum^{L-1}_{i=t+1}i\frac{p_i}{p_B}" /></a>  
&emsp;&emsp;<a href="https://www.codecogs.com/eqnedit.php?latex=\omega_0&space;=&space;p_A\omega_A&plus;p_B\omega_B&space;=&space;\sum^{L-1}_{i=0}ip_i" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\omega_0&space;=&space;p_A\omega_A&plus;p_B\omega_B&space;=&space;\sum^{L-1}_{i=0}ip_i" title="\omega_0 = p_A\omega_A+p_B\omega_B = \sum^{L-1}_{i=0}ip_i" /></a>  
&emsp;&emsp;<a href="https://www.codecogs.com/eqnedit.php?latex=\sigma^2&space;=&space;p_A(\omiga_A-\omega_0)^2&plus;p_B(\omega_B-\omega_0)^2" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\sigma^2&space;=&space;p_A(\omiga_A-\omega_0)^2&plus;p_B(\omega_B-\omega_0)^2" title="\sigma^2 = p_A(\omiga_A-\omega_0)^2+p_B(\omega_B-\omega_0)^2" /></a>

第一个表达式分别计算A和B区域的平均灰度值；第二个表达式计算灰度图像全局的灰度平均值；第三个表达式计算A、B两个区域的类间方差。  
&emsp;&emsp;4）以上几个步骤计算出了单个灰度值上的类间方差，因此最佳分割门限值应该是图像中能够使得A与B的类间灰度方差最大的灰度值。在程序中需要对每个出现的灰度值据此进行寻优。  

- **一维交叉熵值法**  

- **二维Otsu法**


## 2. 基于边缘的分割方法

&emsp;&emsp;所谓边缘是指图像中两个不同区域的边界线上连续的像素点的集合，是图像局部特征不连续性的反映，体现了灰度、颜色、纹理等图像特性的突变。通常情况下，基于边缘的分割方法指的是基于灰度值的边缘检测，它是建立在边缘灰度值会呈现出阶跃型或屋顶型变化这一观测基础上的方法。
阶跃型边缘两边像素点的灰度值存在着明显的差异，而屋顶型边缘则位于灰度值上升或下降的转折处。正是基于这一特性，可以使用微分算子进行边缘检测，即使用一阶导数的极值与二阶导数的过零点来确定边缘，具体实现时可以使用图像与模板进行卷积来完成。  
&emsp;&emsp;一阶的梯度算子通过对图像空域上的差分来求取一阶导数，在两块不同灰度区域的边缘上，一阶导数的模值达到最大，通过设定的阈值，将梯度值大于阈值的位置确定为边缘。常见的一阶梯度算子有：Prewitt、Roberts、Sobel。但是，一阶梯度算子存在检出边缘较多的问题，二阶微分梯度算子能取得更好的效果。因为一阶导数的局部极大值对应着二阶导数中的零交叉点，通过找出二阶导数的零交叉点就能确定精确的边缘点。常用的二阶梯度算子有Laplacian、LOG（Laplacian of Gaussian）。John F. Canny于1986年提出的Canny算法在边缘提取中也是效果很好一种方法。Canny算法对边缘处理有以下步骤：  
&emsp;&emsp;1) 用高斯滤波器去除图像的噪声；    
&emsp;&emsp;2) 用一阶偏导数来计算梯度的幅值及其方向；  
&emsp;&emsp;3)对梯度幅值进行非极大值抑制。即比较某像素位置梯度的四邻域，若其不是五个值中的最大值，则将其置0；  
&emsp;&emsp;4) 使用双阈值检测和连接边缘。设置一高一低两个阈值，将梯度值大于高阈值的标记为强边缘，梯度值只大于低阈值的标记为弱边缘。强边缘都是目标边缘，在此基础上，寻找其8邻域内是否存在弱边缘，若有则将其连入边缘中形成最终结果。  
&emsp;&emsp;其他基于边缘的图像分割算法还有小波多尺度边缘检测：利用二进小波变换，在大尺度下抑制噪声，小尺度定位边缘，抗噪能力比较好。形态学的top-hat（顶帽变换）变换，能提取出灰度图像中亮边缘，通过改变形态学模板的大小还能控制边缘的精细程度。

## 3. 基于区域的分割方法

&emsp;&emsp;此类方法是将图像按照相似性准则分成不同的区域，主要包括种子区域生长法、区域分裂合并法和分水岭法等几种类型。  
&emsp;&emsp;种子区域生长法是从一组代表不同生长区域的种子像素开始，接下来将种子像素邻域里符合条件的像素合并到种子像素所代表的生长区域中，并将新添加的像素作为新的种子像素继续合并过程，直到找不到符合条件的新像素为止。该方法的关键是选择合适的初始种子像素以及合理的生长准则。
区域分裂合并法（Gonzalez，2002）的基本思想是首先将图像任意分成若干互不相交的区域，然后再按照相关准则对这些区域进行分裂或者合并从而完成分割任务，该方法既适用于灰度图像分割也适用于纹理图像分割。  
&emsp;&emsp;分水岭法（Meyer，1990）是一种基于拓扑理论的数学形态学的分割方法，其基本思想是把图像看作是测地学上的拓扑地貌，图像中每一点像素的灰度值表示该点的海拔高度，每一个局部极小值及其影响区域称为集水盆，而集水盆的边界则形成分水岭。该算法的实现可以模拟成洪水淹没的过程，图像的最低点首先被淹没，然后水逐渐淹没整个山谷。当水位到达一定高度的时候将会溢出，这时在水溢出的地方修建堤坝，重复这个过程直到整个图像上的点全部被淹没，这时所建立的一系列堤坝就成为分开各个盆地的分水岭。分水岭算法对微弱的边缘有着良好的响应，但图像中的噪声会使分水岭算法产生过分割的现象。  
&emsp;&emsp;区域生长：将一些像素作为区域生长的初始位置，称之为种子。通过相似性准则，计算种子与邻域像素之间的欧式距离，若该值小于某个阈值那么将这个像素吸收到种子区域并将其设置为新的生长点，如此循环实现生长。区域生长的好坏取决于种子点的选取是否能代表该区域特征，相似性准则的选取是否合理。区域分裂：与区域生长相反，区域分裂将整个图像看作一块区域，采用四叉树结构的迭代分裂算法进行分裂。其分裂过程为，设定一个区域算子P，当P满足某一条件时将区域一分为四，对新的区域重新计算，直到所有的区域都不满足分裂条件，那么分裂结束。可以选取区域内像素的方差作为判断是否继续分裂的条件，或者梯度幅值等等。区域合并：区域合并是以相似性准则为基础，将空域上相邻的区域合并为一个区域的过程。在区域生长和区域分裂的过程中往往有区域合并同时进行。将区域生长和区域分裂产生的过多的分割数合并到最符合图像分割要求的区域数量。  

- **传统分水岭算法**

&emsp;&emsp;分水岭比较经典的计算方法是L．Vincent于1991年在PAMI上提出的。传统的分水岭分割方法，是一种基于拓扑理论的数学形态学的分割方法，其基本思想是把图像看作是测地学上的拓扑地貌，图像中每一像素的灰度值表示该点的海拔高度，每一个局部极小值及其影响区域称为集水盆地，而集水盆地的边界则形成分水岭。分水岭的概念和形成可以通过模拟浸入过程来说明。在每一个局部极小值表面，刺穿一个小孔，然后把整个模型慢慢浸人水中，随着浸入的加深，每一个局部极小值的影响域慢慢向外扩展，在两个集水盆汇合处构筑大坝如下图所示，即形成分水岭。

![图像识别的主要过程]({{'/styles/images/image segmentation/图1.png' | prepend: site.baseurl }})

&emsp;&emsp;图 1  传统分水岭算法示意图

- **改进分水岭算法**

&emsp;&emsp;由于基于梯度图像的直接分水岭算法容易导致图像的过分割，产生这一现象的原因主要是由于输入的图像存在过多的极小区域而产生许多小的集水盆地，从而导致分割后的图像不能将图像中有意义的区域表示出来，所以必须对分割结果的相似区域进行合并。OpenCV提供了一种改进的分水岭算法，使用一系列预定义标记来引导图像分割的定义方式。使用OpenCV的分水岭算法cv::wathershed，需要输入一个标记图像，图像的像素值为32位有符号正数（CV\_32S类型），每个非零像素代表一个标签。它的原理是对图像中部分像素做标记，表明它的所属区域是已知的。分水岭算法可以根据这个初始标签确定其他像素所属的区域。传统的基于梯度的分水岭算法和改进后基于标记的分水岭算法示意图如下图所示：

![图像识别的主要过程]({{'/styles/images/image segmentation/图2.png' | prepend: site.baseurl }})

&emsp;&emsp;图 2  传统分水岭算法和基于标记分水岭算法原理图

- **基于标记点的分水岭算法应用**

&emsp;&emsp;1)	封装分水岭算法类    
&emsp;&emsp;2)	获取标记图像  
&emsp;&emsp;3)	将原图和标记图像输入分水岭算法  
&emsp;&emsp;4)	显示结果 

## 4. 基于图论的分割方法

![图像识别的主要过程]({{'/styles/images/image segmentation/图3.png' | prepend: site.baseurl }})

&emsp;&emsp;此类方法把图像分割问题与图的最小割（min cut）问题相关联。首先将图像映射为带权无向图G=<V，E>，图中每个节点N∈V对应于图像中的每个像素，每条边∈E连接着一对相邻的像素，边的权值表示了相邻像素之间在灰度、颜色或纹理方面的非负相似度。而对图像的一个分割s就是对图的一个剪切，被分割的每个区域C∈S对应着图中的一个子图。而分割的最优原则就是使划分后的子图在内部保持相似度最大，而子图之间的相似度保持最小。基于图论的分割方法的本质就是移除特定的边，将图划分为若干子图从而实现分割。目前所了解到的基于图论的方法有GraphCut，GrabCut和Random Walk等。  
&emsp;&emsp;图像映射为图之后，根据采取的准则方法，图像中的前景和背景都转化为了图中的顶点，并且每个顶点之间都被赋予了一定的权值。若将这个图割开来一分为二，当割在前景顶点和背景顶点之间时能得到最小的一个割。Graph Cut采用图论中的最小割（Minimum Cut）来实现分割，并用最大流（Max-flow）来得到这个最小割。Grub Cut是另一种基于图论的图像分割方法。其从图像到图的基本映射原理与Graph Cut是一样的，区别在于Grub Cut的交互方式更简单，只需要用户画一个矩形框，将待分割的目标完整地框起来，算法将自动迭代完成图像的分割。同时与Graph Cut的目标和背景的模型是灰度直方图不同，Grab Cut取代为RGB三通道的混合高斯模型GMM。  

- **Graph Cut（Goldberg-Tarjan、Ford-Fulkerson）**

&emsp;&emsp;Graph Cuts中有两种顶点、两种边。第一种普通顶点对应于图像中的每个像素。每两个邻域顶点（对应于图像中每两个邻域像素）的连接就是一条边。这种边也叫n-links。除图像像素外，还有另外两个终端顶点，叫S（source：源点，取源头之意）和T（sink：汇点，取汇聚之意）。每个普通顶点和这2个终端顶点之间都有连接，组成第二种边。这种边也叫t-links。

![图像识别的主要过程]({{'/styles/images/image segmentation/图4.png' | prepend: site.baseurl }})
 
&emsp;&emsp;图 4  分割图像对应的s-t图

&emsp;&emsp;上图就是一个图像对应的s-t图，每个像素对应图中的一个相应顶点，另外还有s和t两个顶点。上图有两种边，实线的边表示每两个邻域普通顶点连接的边n-links，虚线的边表示每个普通顶点与s和t连接的边t-links。在前后景分割中，s一般表示前景目标，t一般表示背景。图中每条边都有一个非负的权值we，也可以理解为cost（代价或者费用）。一个cut（割）就是图中边集合E的一个子集C，那这个割的cost（表示为C）就是边子集C的所有边的权值的总和。

## 5. 基于能量泛函的分割方法

&emsp;&emsp;该类方法主要指的是活动轮廓模型（active contour model）以及在其基础上发展出来的算法，其基本思想是使用连续曲线来表达目标边缘，并定义一个能量泛函使得其自变量包括边缘曲线，因此分割过程就转变为求解能量泛函的最小值的过程，一般可通过求解函数对应的欧拉(Euler．Lagrange)方程来实现，能量达到最小时的曲线位置就是目标的轮廓所在。   
&emsp;&emsp;主动轮廓线模型是一个自顶向下定位图像特征的机制，用户或其他自动处理过程通过事先在感兴趣目标附近放置一个初始轮廓线，在内部能量（内力）和外部能量（外力）的作用下变形外部能量吸引活动轮廓朝物体边缘运动，而内部能量保持活动轮廓的光滑性和拓扑性，当能量达到最小时，活动轮廓收敛到所要检测的物体边缘。按照模型中曲线表达形式的不同，活动轮廓模型可以分为两大类：参数活动轮廓模型（parametric active contour model）和几何活动轮廓模型（geometric active contour model）。  
&emsp;&emsp;活动轮廓模型（Active Contour Model，ACM）是偏微分方程在图像分割中的应用，由Kass、Witkin和Terzopoulos等1987年提出来的。这是一种基于能量的图像分割方法，其能量函数为基于曲线的内部能量和基于图像数据外部能量的加权和。通过极小化能量泛函，使得待分割目标周围的一条曲线在固有内力和图像外力的共同作用下不断演化，最终收敛到目标的边界轮廓。活动轮廓模型依据其表达和实现方法不同可分为两种，参数化的Snake及其改进模型和几何形变模型——水平集（Level Set）。

### 5.1	参数活动轮廓模型

&emsp;&emsp;参数活动轮廓模型是基于Lagrange框架，直接以曲线的参数化形式来表达曲线，最具代表性的是由Kasset a1(1987)所提出的Snake模型。Snakes模型的基本思想很简单，它以构成一定形状的一些控制点为模板（轮廓线），通过模板自身的弹性形变，与图像局部特征相匹配达到调和，即某种能量函数极小化，完成对图像的分割。再通过对模板的进一步分析而实现图像的理解和识别。简单的来讲，SNAKE模型就是一条可变形的参数曲线及相应的能量函数，以最小化能量目标函数为目标，控制参数曲线变形，具有最小能量的闭合曲线就是目标轮廓。  
&emsp;&emsp;构造Snakes模型的目的是为了调和上层知识和底层图像特征这一对矛盾。无论是亮度、梯度、角点、纹理还是光流，所有的图像特征都是局部的。所谓局部性就是指图像上某一点的特征只取决于这一点所在的邻域，而与物体的形状无关。但是人们对物体的认识主要是来自于其外形轮廓。Snakes模型的轮廓线承载了上层知识，而轮廓线与图像的匹配又融合了底层特征。这两项分别表示为Snakes模型中能量函数的内部力和图像力。模型的形变受到同时作用在模型上的许多不同的力所控制，每一种力产生一部分能量，这部分能量表示为活动轮廓模型的能量函数的一个独立的能量项。   
&emsp;&emsp;Snake模型首先需要在感兴趣区域的附近给出一条初始曲线，接下来最小化能量泛函，让曲线在图像中发生变形并不断逼近目标轮廓。Kass等提出的原始Snakes模型由一组控制点：v(s)=[x(s), y(s)]   s∈[0, 1] 组成，这些点首尾以直线相连构成轮廓线。其中x(s)和y(s)分别表示每个控制点在图像中的坐标位置。 s 是以傅立叶变换形式描述边界的自变量。在Snakes的控制点上定义能量函数（反映能量与轮廓之间的关系）：

&emsp;&emsp;<a href="https://www.codecogs.com/eqnedit.php?latex=E_{total}=\int_s(\alpha|\frac{\partial}{\partial&space;s&space;\vec{v}|^2&plus;\beta|\frac{\partial^2}{\partial&space;s^2}\vec{v}|^2)&plus;E_{ext}(\vec{v}}(s))ds" target="_blank"><img src="https://latex.codecogs.com/gif.latex?E_{total}=\int_s(\alpha|\frac{\partial}{\partial&space;s&space;\vec{v}|^2&plus;\beta|\frac{\partial^2}{\partial&space;s^2}\vec{v}|^2)&plus;E_{ext}(\vec{v}}(s))ds" title="E_{total}=\int_s(\alpha|\frac{\partial}{\partial s}\vec{v}|^2+\beta|\frac{\partial^2}{\partial s^2}\vec{v}|^2+E_{ext}(\vec{v}(s)))ds" /></a>  
其中第1项称为弹性能量，是v的一阶导数的模，第2项称为弯曲能量，是v的二阶导数的模，第3项是外部能量（外部力），在基本Snakes模型中一般只取控制点或连线所在位置的图像局部特征例如梯度：

&emsp;&emsp;<a href="https://www.codecogs.com/eqnedit.php?latex=E_{ext}(\vec{v}(s))=P(\vec{v}(s))=-|\bigtriangledown&space;I(v)|^2" target="_blank"><img src="https://latex.codecogs.com/gif.latex?E_{ext}(\vec{v}(s))=P(\vec{v}(s))=-|\bigtriangledown&space;I(v)|^2" title="E_{ext}(\vec{v}(s))=P(\vec{v}(s))=-|\bigtriangledown I(v)|^2" /></a>

也称图像力。（当轮廓C靠近目标图像边缘，那么C的灰度的梯度将会增大，那么上式的能量最小，由曲线演变公式知道该点的速度将变为0，也就是停止运动了。这样，C就停在图像的边缘位置了，也就完成了分割。那么这个的前提就是目标在图像中的边缘比较明显了，否则很容易就越过边缘了。）  
&emsp;&emsp;弹性能量和弯曲能量合称内部能量（内部力），用于控制轮廓线的弹性形变，起到保持轮廓连续性和平滑性的作用。而第三项代表外部能量，也被称为图像能量，表示变形曲线与图像局部特征吻合的情况。内部能量仅仅跟snake的形状有关，而跟图像数据无关。而外部能量仅仅跟图像数据有关。在某一点的α和β的值决定曲线可以在这一点伸展和弯曲的程度。  
&emsp;&emsp;最终对图像的分割转化为求解能量函数Etotal(v)极小化（最小化轮廓的能量）。在能量函数极小化过程中，弹性能量迅速把轮廓线压缩成一个光滑的圆，弯曲能量驱使轮廓线成为光滑曲线或直线，而图像力则使轮廓线向图像的高梯度位置靠拢。基本Snakes模型就是在这3个力的联合作用下工作的。  
&emsp;&emsp;因为图像上的点都是离散的，所以我们用来优化能量函数的算法都必须在离散域里定义。所以求解能量函数Etotal(v)极小化是一个典型的变分问题（微分运算中，自变量一般是坐标等变量，因变量是函数；变分运算中，自变量是函数，因变量是函数的函数，即数学上所谓的泛函。对泛函求极值的问题，数学上称之为变分法）。  
&emsp;&emsp;在离散化条件（数字图像）下，由欧拉方程可知最终问题的答案等价于求解一组差分方程：（欧拉方程是泛函极值条件的微分表达式，求解泛函的欧拉方程，即可得到使泛函取极值的驻函数，将变分问题转化为微分问题。）

&emsp;&emsp;<a href="https://www.codecogs.com/eqnedit.php?latex=-\alpha^{'}\vec{v}^{'}-(\alpha-\beta)\vec{v}^{''}&plus;2\beta^{'}\vec{v}^{'''}&plus;\beta\vec{v}^{''''}=-\bigtriangledown&space;P(\vec{v})" target="_blank"><img src="https://latex.codecogs.com/gif.latex?-\alpha^{'}\vec{v}^{'}-(\alpha-\beta)\vec{v}^{''}&plus;2\beta^{'}\vec{v}^{'''}&plus;\beta\vec{v}^{''''}=-\bigtriangledown&space;P(\vec{v})" title="-\alpha^{'}\vec{v}^{'}-(\alpha-\beta)\vec{v}^{''}+2\beta^{'}\vec{v}^{'''}+\beta\vec{v}^{''''}=-\bigtriangledown P(\vec{v})" /></a>

记外部力 F = −∇ P， Kass等将上式离散化后，对x(s)和y(s)分别构造两个五对角阵的线性方程组，通过迭代计算进行求解。在实际应用中一般先在物体周围手动点出控制点作为Snakes模型的起始位置，然后对能量函数迭代求解。  
&emsp;&emsp;通过能量最小化逼近所提取的特征有很大优越性，但是，由于能量函数的非凸性，存在多个局部极小值，因而模型的初始位置需要靠近目标的轮廓边界；因内力的抵抗作用，模型不能处理尖角、深凹、狭长分叉等形状。

- **Snake模型综述** 

&emsp;&emsp;Snake模型主要研究的方面：1.表示内部能量的曲线演化；2.外力；3.能量最小化。  
&emsp;&emsp;现有的初始轮廓确定的方法有以下几种：1.人工勾勒图像的边缘；2.序列图像差分边界；3.基于序列图像的前一帧图像边界的预测；4.基于传统图像分割结果进行边界选取。  
&emsp;&emsp;Snake模型的改进算法：1.Cohen提出的气球（balloon）理论模型；2.Xu提出梯度矢量流（GVF）概念。  
&emsp;&emsp;局部优化算法：1.Amini提出基于动态规划的snake算法；2.变分法；3.贪婪算法；4.有限差分法；5.有限元法。    
&emsp;&emsp;全局优化算法：1.模拟退火；2.遗传算法；3.神经网络。  
&emsp;&emsp;Snake的进化模型：1.McInerney 提出一种拓扑自适应snake模型（Topology Adaptive  Snake,T-Snake）；2.双T-Snake模型（Dual-T-Snakes）；3.Loop  Snake 模型；4.连续snake模型；5.B-Snake模型。

- **GVF Snake Model**

&emsp;&emsp;*slne.jpg：*

![图像识别的主要过程]({{'/styles/images/image segmentation/图5.PNG' | prepend: site.baseurl }})	 

&emsp;&emsp;*rect.jpg:*
 	 
![图像识别的主要过程]({{'/styles/images/image segmentation/图6.PNG' | prepend: site.baseurl }})

&emsp;&emsp;*test.jpg:*
 	 
![图像识别的主要过程]({{'/styles/images/image segmentation/图7.PNG' | prepend: site.baseurl }})

&emsp;&emsp;*apple.jpg:*

![图像识别的主要过程]({{'/styles/images/image segmentation/图8.PNG' | prepend: site.baseurl }})	 

&emsp;&emsp;该算法对凹凸形状的边缘都能很好适应。

### 5.2	几何活动轮廓模型

&emsp;&emsp;几何活动轮廓模型的曲线运动过程是基于曲线的几何度量参数而非曲线的表达参数，因此可以较好地处理拓扑结构的变化，并可以解决参数活动轮廓模型难以解决的问题。而水平集（Level Set）方法（Osher，1988）的引入，则极大地推动了几何活动轮廓模型的发展，因此几何活动轮廓模型一般也可被称为水平集方法。  

- **曲线演化理论**

&emsp;&emsp;曲线可以简单的分为几种：

![图像识别的主要过程]({{'/styles/images/image segmentation/图9.PNG' | prepend: site.baseurl }})

&emsp;&emsp;曲线存在曲率，曲率有正有负，于是在法向曲率力的推动下，曲线的运动方向之间有所不同：有些部分朝外扩展，而有些部分则朝内运动。这种情形如下图所示。图中蓝色箭头处的曲率为负，而绿色箭头处的曲率为正。  
&emsp;&emsp;简单曲线在曲率力（也就是曲线的二次导数）的驱动下演化所具有的一种非常特殊的数学性质是：一切简单曲线，无论被扭曲得多么严重，只要还是一种简单曲线，那么在曲率力的推动下最终将退化成一个圆，然后消逝（可以想象下，圆的所有点的曲率力都向着圆心，所以它将慢慢缩小，以致最后消逝）。  
&emsp;&emsp;描述曲线几何特征的两个重要参数是单位法矢和曲率，单位法矢描述曲线的方向，曲率则表述曲线弯曲的程度。曲线演化理论就是仅利用曲线的单位法矢和曲率等几何参数来研究曲线随时间的变形。曲线的演变过程可以认为是表示曲线在作用力 F 的驱动下，朝法线方向 N 以速度 v 演化。而速度是有正负之分的，所以就有如果速度 v 的符号为负，表示活动轮廓演化过程是朝外部方向的，如为正，则表示朝内部方向演化，活动曲线是单方向演化的，不可能同时往两个方向演化。  
&emsp;&emsp;所以曲线的演变过程，就是不同力在曲线上的作用过程，力也可以表达为能量。世界万物都趋向于能量最小而存在。因为此时它是最平衡的，消耗最小的。那么在图像分割里面，我们目标是把目标的轮廓找到，那么在目标的轮廓这个地方，整个轮廓的能量是最小的，那么曲线在图像任何一个地方，都可以因为力朝着这个能量最小的轮廓演变，当演变到目标的轮廓的时候，因为能量最小，力平衡了，速度为0了，也就不动了，这时候目标就被我们分割出来了。

- **水平集方法**

&emsp;&emsp;水平集方法是将曲线嵌入到一个3维的曲面中的0水平集中，通过演化3维空间中的曲面，再从演化后的曲面中获取0水平集，这个0水平集就是演化后的曲线。假设二维平面上的曲线由  表示，该关系也可用隐函数 = 0来描述，此时若设：

&emsp;&emsp;<a href="https://www.codecogs.com/eqnedit.php?latex=\psi(x,y)=y-f(x)" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\psi(x,y)=y-f(x)" title="\psi(x,y)=y-f(x)" /></a>

则<a href="https://www.codecogs.com/eqnedit.php?latex=\psi(x,y)=0" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\psi(x,y)=0" title="\psi(x,y)=0" /></a>就是曲线的隐式表达式。如图：

![图像识别的主要过程]({{'/styles/images/image segmentation/图10.PNG' | prepend: site.baseurl }})

对于<a href="https://www.codecogs.com/eqnedit.php?latex=\psi(x,y)" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\psi(x,y)" title="\psi(x,y)" /></a>的构建，一般采用符号距离函数

- **Chan-Vese模型**

![图像识别的主要过程]({{'/styles/images/image segmentation/图11.PNG' | prepend: site.baseurl }})

## 6. 基于聚类的图像分割
## 7. 基于模糊集理论的图像分割
## 8. 基于基因编码的图像分割
## 9. 基于神经网络ANN
## 10. 启发式算法的图像分割
