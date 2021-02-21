## GBDT
GBDT通过多轮迭代,每轮迭代产生一个弱分类器，每个分类器在上一轮分类器的残差基础上进行训练。对弱分类器的要求一般是足够简单，并且是**低方差和高偏差**的。
![示例图](https://upload-images.jianshu.io/upload_images/7155422-5f9eeadbc73d93c6.png?imageMogr2/auto-orient/strip|imageView2/2/w/1120/format/webp)

GBDT的加法模型为：
$$
f_m(x)=\sum_{m=1}^MT(x;\Theta_m)
$$

其中，$T(x;\Theta_m)$代表了弱分类器：决策树(CART)，$M$代表树的个数

前向算法：首先确定初始提升树$f_0(x)=0$，第m棵树是：
$$
f_m(x)=f_{m-1}(x)+T(x;\Theta_m)
$$

通过经验风险最小确定下一棵树的参数(残差最小，这也是模型方差小、偏差大的原因)：

$$
\hat{\Theta}_m = argmin\sum_{i=1}^NL(y_i, f_{m-1}(x_i)+T(x;\Theta_m))
$$

其中$L$就是损失函数，回归算法选择的损失函数一般是均方差或者绝对值误差，而在分类算法中一般的损失函数，都是对数损失。
均方差：
$$
L(Y, P(Y|X))=(Y-f(X))^2
$$
绝对值损失：
$$
L(Y, P(Y|X))=|Y-f(X)|
$$
对数损失：
$$
L(Y, P(Y|X))=-\log P(Y|X)
$$








