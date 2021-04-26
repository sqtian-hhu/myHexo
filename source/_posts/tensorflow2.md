---
title: tensorflow2
author: 世庆
top: false
cover: false
toc: true
mathjax: false
date: 2021-04-23 12:28:36
img:
coverImg:
password:
summary:
tags: Tensorflow
categories: Python
---

##创建一个Tensor
	创建全为0的张量: tf.zeros(维度)
		维度: 一维 直接写个数
			  二维 [行,列]
			  多维 [n,m,j,k...]
	创建全为1的张量: tf.ones(维度)
	创建全为指定值的张量: tf.fill(维度,指定值)
	
	生成正态分布的随机数: 默认均值为0, 标准差为1
		tf.random.normal(维度,mean=均值,stddev=标准差)
	生成截断式正态分布的随机数
		tf.random.truncated_normal(维度,mean=均值,stddev=标准差)
	生成均匀分布随机数
		tf.random.uniform(维度,minval=最小值,maxval=最大值)
	
##tf2常用函数
	强制tensor转换为该数据类型
		tf.cast(张量名,dtype=数据类型)
	计算张量维度上的元素最小值
		tf.reduce_min(张量名)
	计算张量维度上的元素最大值
		tf.reduce_max(张量名) 
	计算张量沿着指定维度的平均值
		tf.reduce_mean(张量名,axis=操作轴)
	计算张量沿着指定维度的和
		tf.reduce_sum(张量名,axis=操作轴)
		不指定axis则对所有元素操作
	将变量标记为"可训练",被标记的变量会在反向传播中记录梯度信息
		tf.Variable(初始值)
		w = tf.Variable(tf.random.normal([2,2],mean=0,stddev=1))
	四则运算
		add  subtract  multiply  divide
		只有两个维度相同的张量才能进行四则运算
	矩阵乘
		tf.matmul(矩阵1,矩阵2)
	切分传入张量的第一维度,生成输入特征/标签对,构建数据集(即打标签)
		data = tf.data.Dataset.from_tensor_slices((输入特征,标签))
	with 结构记录计算过程,gradient求出张量的梯度
		with tf.GradientTape() as tape:
			若干个计算过程
		grad = tape.gradient(函数,对谁求导)
	enumerate是python的内建函数, 他可遍历每个元素 组合为: 索引 元素
		seq = ['one','two','three']
		for i, element in enumerate(seq):
			print(i,element)
	独热编码: 在分类问题中,常用独热码做标签
		tf.one_hot(待转换数据,depth=几分类)
	softmax
		tf.nn.softmax(x)
	赋值操作,更新参数的值并返回. 调用assign_sub前,先用tf.Variable定义变量w为可训练
		w.assign_sub(w要自减的内容)
	返回张量沿指定维度最大值的索引
		tf.argmax(张量名,axis=操作轴)
	
##预备知识
tf.where(条件语句,真返回A,假返回B)
	c=tf.where(tf.greater(a,b),a,b)
np.random.RandomState.rand(维度)
	返回一个[0,1]之间的随机数
np.stack(数组1,数组2)
	将两个数组按垂直方向叠加
np.mgrid[起始值:结束值:步长,起始值:结束值:步长]
x.ravel()
	将x变为一维数组, 即把x拉直
np.c_[数组1,数组2...]
	是返回的间隔数值点配对

用Tensorflow API: tf.keras搭建网络
	六步法
	1. import
	2. train, test
	3. model = tf.keras.models.Sequential
	4. model.compile
	5. model.fit
	6. model.summary
			
#描述各层网络
model = tf.keras.models.Sequential([网络结构])
#卷积层
tf.keras.layers.Conv2D(filters=巻积核个数,kernel_size=卷积核尺寸,strides=卷积步长,padding="alid"or"same")
#拉直层
tf.keras.layers.Flatten()
#全连接层
tf.keras.layers.Dense(神经元个数,activation="激活函数",kenel_regularizer=正则化方式)

#LSTM层
tf.keras.LSTM()

model.compile(optimizer=优化器,loss=损失函数,metrics=["准确率"])
优化器可选: 'sgd', 'adagrad', 'adadelta', 'adam'
loss可选: 'mse' / tf.keras.losses.Meanquaredrror(), 'sparse_categorical_crossentropy' / tf.keras.losses.SparseategoricalCrossentropy()
Metrics可选:
	'accuracy': y_和y都是数值, 如y_=[1], y=[1]
	'categorical_accuracy': y_和y都是独热码, 如y_=[0,1,0] y=[0.256,0.695,0.048]
	'sparse_categorical_accuracy': y_是数值, y是独热码
model.fit(训练集的输入特征,训练集的标签,batch_size= ,epochs= ,
		validation_data=(测试集的输入特征,测试集的标签),
		validation_split=从训练集划分多少比例给测试集,
		validation_freq=多少次epoch测试一次)
 
模板:
import tensorflow as tf
from sklearn import datasets
import numpy as np

x_train = datasets.load_iris().data
y_train = datasets.load_iris().target

np.random.seed(116)
np.random.shuffle(x_train)
np.random.seed(116)
np.random.shuffle(y_train)
tf.random.set_seed(116)

model = tf.keras.models.Sequential([
	#编写具体模型
    tf.keras.layers.Dense(3, activation='softmax', kernel_regularizer=tf.keras.regularizers.l2())
])

model.compile(optimizer=tf.keras.optimizers.SGD(lr=0.1),
              loss=tf.keras.losses.SparseCategoricalCrossentropy(from_logits=False),
              metrics=['sparse_categorical_accuracy'])

model.fit(x_train, y_train, batch_size=32, epochs=500, validation_split=0.2, validation_freq=20)

model.summary()

	
##模块化	六步法
	1. import
	2. train, test   (自制数据集, 数据增强)
	3. class Myodel(Model) model = MyModel
	4. model.compile
	5. model.fit   (断点续训)
	6. model.summary  (参数提取, acc/loss可视化)
	

#继承了Model类的Irisodel	
class IrisModel(Model):
    def __init__(self):
    	#构造函数
        super(IrisModel, self).__init__()
        #定义网络结构块
        #d1: 自定义的层名字
        self.d1 = Dense(3, activation='softmax', kernel_regularizer=tf.keras.regularizers.l2())

    def call(self, x):
    	#实现前向传播
        y = self.d1(x)
        return y

model = IrisModel()


#保存模型
tf.keras.callbacks.Modelheckpoint(
filepath=路径文件名,
save_weights_only=True/False,
save_best_only=True/False)

#读取模型
load_weight(路径文件名)

#断点续训
checkpoint_save_path = "./checkpoint/mnist.ckpt"
if os.path.exists(checkpoint_save_path + '.index'):
    print('-------------load the model-----------------')
    model.load_weights(checkpoint_save_path)

cp_callback = tf.keras.callbacks.ModelCheckpoint(filepath=checkpoint_save_path,
                                                 save_weights_only=True,
                                                 save_best_only=True)

history = model.fit(x_train, y_train, batch_size=32, epochs=5, validation_data=(x_test, y_test), validation_freq=1,
                    callbacks=[cp_callback])


#预测结果
result = model.predict(x_predict)

#感受野
卷积神经网络各输出特征图中的每个像素点，在原始输入图片上映射区域的大小。

#卷积层
tf.keras.layers.Conv2D(filters=卷积核个数,kernel_size=卷积核尺寸#正方形写核长整数或(核高h,核宽w),strides=滑动步长,padding="same"or"valid",
	activation="relu"or"sigmoid"or"tanh"or"softmax",#如有BN此处不写
	input_shape=(高,宽,通道数) #输入特征图维度,可省略)

#标准化: 使数据符合0均值,1为标准差的分布
#批标准化: 对一小批数据(batch), 做标准化处理
BN层位于卷积层之后, 激活层之前

池化用于减少特征数据量. 最大值池化可提取图片纹理,均值池化可保留背景特征


卷积就是特征提取器 CBAPD
 
 #模板
 class LeNet5(Model):
    def __init__(self):
        super(LeNet5, self).__init__()
        self.c1 = Conv2D(filters=6, kernel_size=(5, 5),
                         activation='sigmoid')
        self.p1 = MaxPool2D(pool_size=(2, 2), strides=2)

        self.c2 = Conv2D(filters=16, kernel_size=(5, 5),
                         activation='sigmoid')
        self.p2 = MaxPool2D(pool_size=(2, 2), strides=2)

        self.flatten = Flatten()
        self.f1 = Dense(120, activation='sigmoid')
        self.f2 = Dense(84, activation='sigmoid')
        self.f3 = Dense(10, activation='softmax')

    def call(self, x):
        x = self.c1(x)
        x = self.p1(x)

        x = self.c2(x)
        x = self.p2(x)

        x = self.flatten(x)
        x = self.f1(x)
        x = self.f2(x)
        y = self.f3(x)
        return y


model = LeNet5()


#循环网络
连续序列的预测

循环核：参数时间共享，循环层提取时间信息。
前向传播时：记忆体内存储的状态信息ht，在每个时刻都被刷新，
			三个参数矩阵wxh whh why自始至终都是固定不变的
反向传播时：三个参数矩阵wxh whh why被梯度下降法更新

yt=softmax（ht*why+by）
ht=tanh（xt*wxh+h（t-1）*whh+bh）

tf.keras.layers.SimpleRNN(记忆体个数,activation='激活函数',return_sequences=是否每个时刻输出ht到下一层)

