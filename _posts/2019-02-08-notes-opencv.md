---
layout: post
#标题配置
title:  Opencv
#时间配置
date:   2019-02-08 01:08:00 +0800
#大类配置
categories: notes
#小类配置
tag: Opencv
---

* content
{:toc}



## 1. test_snake model

		typedef struct CvMemStorage {
		struct CvMemBlock* bottom; 
		struct CvMemBlock* top;  
		struct CvMemStorage* parent;  
		int block_size;  
		int free_space; 
		} CvMemStorage;
&emsp;&emsp;CvMemStorage：内存存储器，可用来存储诸如序列、轮廓、图形、子划分等动态增长数据结构的底层结构，由一系列以同等大小的内存块构成，呈列表型。  
&emsp;&emsp;bottom域指的是列首，top域指的是当前指向的块但未必是列尾。在bottom和top之间所有的块(包括bottom, 不包括top）被完全占据了空间；在 top和列尾之间所有的块（包括块尾，不包括top)则是空的；而top块本身则被占据了部分空间。  
&emsp;&emsp;创建内存块：   

		CvMemStorage* m_storage=cvCreateMemStorage(int block_size=0); 
  
&emsp;&emsp;block_size：存储块的大小以字节表示。如果大小是 0 byte, 则将该块设置成默认值 当前默认大小为64k。  
&emsp;&emsp;函数cvCreateMemStorage 创建一内存块并返回指向块首的指针。起初，存储块是空的。
函数cvClearMemStorage 将存储块的 top 置到存储块的头部（注：清空存储块中的存储内容）。该函数并不释放内存（仅清空内存）。  

		void cvClearMemStorage( CvMemStorage* m_storage );

&emsp;&emsp;函数cvMemStorageAlloc在存储块中分配以内存缓冲区, 该缓冲区的大小不能超过内存块的大小，否则就会导致运行时错误。缓冲区的地址被调整为CV_STRUCT_ALIGN字节（当前为 sizeof(double))。

		void* cvMemStorageAlloc( CvMemStorage* m_storage, size_t size);

&emsp;&emsp;函数cvSaveMemStoragePos 将存储块的当前位置保存到参数pos 中。 函数 cvRestoreMemStoragePos可进一步获取该位置（地址）。  

		void cvSaveMemStoragePos( const CvMemStorage* storage,CvMemStoragePos* pos );

&emsp;&emsp;函数cvRestoreMemStoragePos 通过参数 pos恢复内存块的位置。该函数和函数 cvClearMemStorage是释放被占用内存块的唯一方法。注意：没有什么方法可去释放存储块中被占用的部分内存。  

		void cvRestoreMemStoragePos( CvMemStorage* storage,CvMemStoragePos* pos );

		typedef struct CvSeq
		{
		    CV_SEQUENCE_FIELDS()
		};
		#define CV_SEQUENCE_FIELDS() 
		int flags; /* micsellaneous flags */ 
		int header_size; /* 序列头的大小 */ 
		struct CvSeq* h_prev; /* 前一个序列 */ 
		struct CvSeq* h_next; /* 后一个序列 */ 
		struct CvSeq* v_prev; /* 第二级前一个序列 */ 
		struct CvSeq* v_next; /* 第二级后一个序列 */ 
		int total; /* 元素的总个数 */ 
		int elem_size;/* 元素的尺寸 */ 
		char* block_max;/* 上一块的最大块 */ 
		char* ptr; /* 当前写指针 */ 
		int delta_elems; /*序列中快的大小 (序列粒度) */ 
		CvMemStorage* storage; /*序列的存储位置 */ 
		CvSeqBlock* free_blocks; /* 未分配的块序列 */ 
		CvSeqBlock* first; /* 指向第一个快序列 */

&emsp;&emsp;用户可以自定义一个结构，然后通过宏定义CV_SEQUENCE_FIELDS()将自己定义的结构放在CvSeq参数后面组成一个新的结构。有两种类型的序列：密集型、稀疏型。密集型序列的基本类型就是CvSeq，这样的序列用来表示可增长的一维数组：向量、栈，队列和双向队列。他们中间没有间隔，如果从中间插入或者删除一个元素，那么从它到离它比较近的一个终点的元素都要移动。稀疏型序列的基本类型是CvSet，他们是一个个节点的序列，每个节点，每个可以通过节点标识来申请或者释放，这种用于无序的数据结构，例如：集合元素，图标，哈希表等等。   
&emsp;&emsp;函数 cvFindContours从二值图像中提取轮廓，并且返回提取轮廓的数目。指针 first_contour 的内容由函数填写。它包含第一个最外层轮廓的指针，如果指针为 NULL，则没有检测到轮廓（比如图像是全黑的）。其它轮廓可以从 first\_contour 利用 h\_next 和 v\_next 链接访问到。  

		int cvFindContours(CvArr* image, CvMemStorage* storage, CvSeq** first_contour,int header_size=sizeof(CvContour), int mode=CV_RETR_LIST, int method=CV_CHAIN_APPROX_SIMPLE, CvPoint offset=cvPoint(0,0));  

&emsp;&emsp;image：输入的 8-比特、单通道图像. 非零元素被当成 1，0 象素值保留为 0 - 从而图像被看成二值的。为了从灰度图像中得到这样的二值图像，可以使用 cvThreshold, cvAdaptiveThreshold 或 cvCanny. 本函数改变输入图像内容。   
&emsp;&emsp;storage：得到的轮廓的存储容器。   
&emsp;&emsp;first\_contour：输出参数：包含第一个输出轮廓的指针。   
&emsp;&emsp;header_size：如果 method=CV\_CHAIN\_CODE,则序列头的大小>=sizeof(CvChain)，否则 >=sizeof(CvContour)。   
&emsp;&emsp;mode：提取模式。  
&emsp;&emsp;CV\_RETR\_EXTERNAL --- 只提取最外层的轮廓。   
&emsp;&emsp;CV\_RETR\_LIST --- 提取所有轮廓，并且放置在list中。   
&emsp;&emsp;CV\_RETR\_CCOMP --- 提取所有轮廓，并且将其组织为两层的 hierarchy: 顶层为连通域的外围边界，次层为洞的内层边界。   
&emsp;&emsp;CV\_RETR\_TREE --- 提取所有轮廓，并且重构嵌套轮廓的全部 hierarchy。    
&emsp;&emsp;method：逼近方法（对所有节点, 不包括使用内部逼近的 CV\_RETR\_RUNS）。   
&emsp;&emsp;CV\_CHAIN\_CODE --- Freeman 链码的输出轮廓. 其它方法输出多边形（定点序列）。     
&emsp;&emsp;CV\_CHAIN\_APPROX\_NONE --- 将所有点由链码形式翻译（转化）为点序列形式。   
&emsp;&emsp;CV\_CHAIN\_APPROX\_SIMPLE --- 压缩水平、垂直和对角分割，即函数只保留末端的象素点。   
&emsp;&emsp;CV\_CHAIN\_APPROX\_TC89\_L1。  
&emsp;&emsp;CV\_CHAIN\_APPROX\_TC89\_KCOS --- 应用Teh-Chin链逼近算法。   
&emsp;&emsp;CV\_LINK\_RUNS --- 通过连接为1的水平碎片使用完全不同的轮廓提取算法。  
&emsp;&emsp;CV\_RETR\_LIST --- 提取模式可以在本方法中应用。  
&emsp;&emsp;offset：每一个轮廓点的偏移量. 当轮廓是从图像 ROI 中提取出来的时候，使用偏移量有用，因为可以从整个图像上下文来对轮廓做分析。  

		void cvDrawContours( CvArr *img, CvSeq* contour,CvScalar external_color, CvScalar hole_color,int max_level, int thickness=1,int line_type=8, CvPoint offset=cvPoint(0,0) );  

&emsp;&emsp;img：用以绘制轮廓的图像。和其他绘图函数一样，边界图像被感兴趣区域（ROI）所剪切。   
&emsp;&emsp;contour：指针指向第一个轮廓。     
&emsp;&emsp;external\_color：外层轮廓的颜色。   
&emsp;&emsp;hole\_color：内层轮廓的颜色。   
&emsp;&emsp;max\_level：绘制轮廓的最大等级。如果等级为0，绘制单独的轮廓。如果为1，绘制轮廓及在其后的相同的级别下轮廓。如果值为2，所有的轮廓。如果等级为2，绘制所有同级轮廓及所有低一级轮廓，诸此种种。如果值为负数，函数不绘制同级轮廓，但会升序绘制直到级别为abs(max_level)-1的子轮廓。   
&emsp;&emsp;thickness：绘制轮廓时所使用的线条的粗细度。如果值为负(e.g. =CV_FILLED)，绘制内层轮廓。当thickness>=0,函数cvDrawContours在图像中绘制轮廓,或者当thickness<0时，填充轮廓所限制的区域。   
&emsp;&emsp;line\_type：线条的类型。参考cvLine。   
&emsp;&emsp;offset：照给出的偏移量移动每一个轮廓点坐标。当轮廓是从某些感兴趣区域(ROI)中提取的然后需要在运算中考虑ROI偏移量时，将会用到这个参数。   
&emsp;&emsp;CvTermCriteria---迭代算法终止条件结构体的。

		class CV_EXPORTS TermCriteria  
		{  
		public:  
		    enum  
		    {  
		        COUNT=1, //the maximum number of iterations or elements to compute  
		        MAX_ITER=COUNT, //ditto  
		        EPS=2 //the desired accuracy or change in parameters at which the iterative algorithm stops  
		    };   
		    //[1]默认构造函数  
		    TermCriteria();  
		    //[2]完全构造函数  
		    TermCriteria(int type, int maxCount, double epsilon);  
		    //[3]CvTermCriteria结构体和TermCriteria类的转换构造函数  
		    TermCriteria(const CvTermCriteria& criteria);  
		    //! conversion to CvTermCriteria  
		    operator CvTermCriteria() const;  
		    //[4]TermCriteria类的三个数据成员----终止条件的类型,最大迭代次数,所期望的精度  
		    int type;
		    int maxCount; 
		    double epsilon; 
		}; 

		int cvCreateTrackbar(
		const char* trackbar_name, //滑动条的名称
		const char* window_name, //窗口的名称，滑动条不会遮挡图像
		int* value, //当滑动条被拖到时，OpenCV会自动将当前位置所代表的值传给指针指向的整数
		int count, //滑动条所能达到的最大值
		CvTrackbarCallback on_change //可选的回调函数
		);
		//读取value值
		int cvGetTrackbarPos(const char* trackbar_name, cosnt char* window_name);
		//设置value值
		void cvSetTrackbarPos(const char* trackbar_name, const char* window_name, int pos);

&emsp;&emsp;函数cvLine 在图像中的点1和点2之间画一条线段。线段被图像或感兴趣的矩形(ROI rectangle)所裁剪。对于具有整数坐标的non-antialiasing 线条，使用8-连接或者4-连接Bresenham 算法。画粗线条时结尾是圆形的。画 antialiased 线条使用高斯滤波。要指定线段颜色，用户可以使用使用宏CV_RGB( r, g, b )。  

		void cvLine( CvArr* img, CvPoint pt1, CvPoint pt2, CvScalar color,
             int thickness=1, int line_type=8, int shift=0 );

&emsp;&emsp;img: 图像。    
&emsp;&emsp;pt1: 线段的第一个端点。  
&emsp;&emsp;pt2: 线段的第二个端点。  
&emsp;&emsp;color: 线段的颜色。  
&emsp;&emsp;thickness: 线段的粗细程度。  
&emsp;&emsp;line_type: 线段的类型。  
&emsp;&emsp;8 (or 0) --- 8-connected line（8邻接)连接线。  
&emsp;&emsp;4 --- 4-connected line(4邻接)连接线。  
&emsp;&emsp;CV\_AA --- antialiased 线条。  
&emsp;&emsp;shift: 坐标点的小数点位数。  

## 2. test_Chan Vese model

&emsp;&emsp;函数cvtColor()用于将图像从一个颜色空间转换到另一个颜色空间的转换（目前常见的颜色空间均支持），并且在转换的过程中能够保证数据的类型不变，即转换后的图像的数据类型和位深与源图像一致。

		void cv::cvtColor(  
		    cv::InputArray src, // 输入序列  
		    cv::OutputArray dst, // 输出序列  
		    int code, // 颜色映射码  
		    int dstCn = 0 // 输出的通道数 (0='automatic')  
		); 

&emsp;&emsp;其中，最后一个参数dstCn用于指定目标图像的通道数，如果指定的值是默认值0，那么通道数将由输入图像和颜色转换码决定。

		dilate(
		  InputArray src,
		  OutputArray dst,
		  InputArray kernel,
		  Point anchor=Point(-1,-1),
		  int iterations=1,
		  int borderType=BORDER_CONSTANT,
		  const Scalar& borderValue=morphologyDefaultBorderValue()
		);

&emsp;&emsp;src：源图像。   
&emsp;&emsp;dst：目标图像。  
&emsp;&emsp;kernel：膨胀操作的核。若为NULL时，表示的是使用参考点位于中心3x3的核。   
&emsp;&emsp;我们一般使用函数 getStructuringElement配合这个参数的使用。  &emsp;&emsp;getStructuringElement函数会返回指定形状和尺寸的结构元素（内核矩阵）。  
&emsp;&emsp;anchor：锚的位置，其有默认值（-1，-1），表示锚位于中心。   
&emsp;&emsp;iterations：迭代使用erode()函数的次数，默认值为1。   
&emsp;&emsp;borderType：用于推断图像外部像素的某种边界模式。   
&emsp;&emsp;borderValue：当边界为常数时的边界值。  
