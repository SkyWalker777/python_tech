机器学习 5 电影推荐系统设计
#######################################################################

主要内容

::

	项目框架
	数据源解析
	统计推荐模块
	离线推荐模块
	实时推荐模块
	基于内容的推荐模块

项目框架
***********************************************************************

::

	大数据处理流程
	系统模块设计
	项目系统架构
	项目数据流图

|image0|

数据生命周期
========================================================================

|image1|

大数据处理流程
========================================================================

|image2|

|image3|

我们的目标
========================================================================

|image4|

系统模块设计
========================================================================

|image5|

项目系统架构
========================================================================

|image6|

|image7|

系统数据流图
========================================================================

|image8|

数据源解析
*************************************************************************

| 电影信息
| 用户评分信息
| 电影标签信息

movies.csv, ratings.csv, tags.csv

电影信息
========================================================================

|image9|

用户评分信息
========================================================================

|image10|

电影标签信息
========================================================================

|image11|

主要数据模型
========================================================================

|image12|

统计推荐模块
***********************************************************************

| 历史热门电影统计
| 近期热门
| 电影平均评分统计
| 各类别 Top 10 优质电影统计

|image13|

|image14|

历史热门电影统计
========================================================================

|image15|

近期热门电影统计 
========================================================================

|image16|

电影平均评分统计
========================================================================

|image17|

各类别 Top10 评分电影统计
========================================================================

|image18|

离线推荐模块
***********************************************************************

| 用 ALS 算法训练隐语义模型
| 计算用户推荐矩阵
| 计算电影相似度矩阵

离线推荐模块
========================================================================

|image19|

ALS 推荐模型训练
========================================================================

|image20|

计算用户推荐矩阵
========================================================================

|image21|

计算电影相似度矩阵
========================================================================

|image22|

存储电影相似度矩阵
========================================================================

|image23|

实时推荐模块
***********************************************************************

| 实时推荐架构
| 实时推荐优先级计算

基于模型的实时推荐模块
========================================================================

|image24|

|image25|

推荐优先级计算
========================================================================

|image26|

|image27|

基于内容的推荐
========================================================================

|image28|

|image29|

混合推荐 —— 分区混合
***********************************************************************

|image30|

.. |image0| image:: /_static/machine_learn_intro/WX20200817-095457@2x.webp
.. |image1| image:: /_static/machine_learn_intro/WX20200817-095822@2x.webp
.. |image2| image:: /_static/machine_learn_intro/WX20200817-100805@2x.webp
.. |image3| image:: /_static/machine_learn_intro/WX20200817-100836@2x.webp
.. |image4| image:: /_static/machine_learn_intro/WX20200817-101421@2x.webp
.. |image5| image:: /_static/machine_learn_intro/WX20200817-101613@2x.webp
.. |image6| image:: /_static/machine_learn_intro/WX20200817-101859@2x.webp
.. |image7| image:: /_static/machine_learn_intro/WX20200817-102047@2x.webp
.. |image8| image:: /_static/machine_learn_intro/WX20200817-102249@2x.webp
.. |image9| image:: /_static/machine_learn_intro/WX20200817-102918@2x.webp
.. |image10| image:: /_static/machine_learn_intro/WX20200817-103058@2x.webp
.. |image11| image:: /_static/machine_learn_intro/WX20200817-124048@2x.webp
.. |image12| image:: /_static/machine_learn_intro/WX20200817-124313@2x.webp
.. |image13| image:: /_static/machine_learn_intro/WX20200817-193748@2x.webp
.. |image14| image:: /_static/machine_learn_intro/WX20200817-194009@2x.webp
.. |image15| image:: /_static/machine_learn_intro/v2-0b1b9a101c88098f9870130e3c52b3f2_1440w.jpg
.. |image16| image:: /_static/machine_learn_intro/v2-0581d38980bcb780b7af417abda2b4fa_r.jpg
.. |image17| image:: /_static/machine_learn_intro/v2-384dbf5f643e6df51f25e41eeeb88b4e_r.jpg
.. |image18| image:: /_static/machine_learn_intro/v2-eade484789b29fb851eeb7a9493ee6ed_r.jpg
.. |image19| image:: /_static/machine_learn_intro/v2-3a01ce2c9e6947fc9727704b29e65331_r.jpg
.. |image20| image:: /_static/machine_learn_intro/v2-94c3767674af68ac4488e4f9202d727f_r.jpg
.. |image21| image:: /_static/machine_learn_intro/v2-8f5bf20c1301a2338c695be43500d632_r.jpg
.. |image22| image:: /_static/machine_learn_intro/v2-b68e803227eee9655e5bb15b6eea01dc_r.jpg
.. |image23| image:: /_static/machine_learn_intro/v2-5761480a237d4b5080be15cd08873d9f_1440w.jpg
.. |image24| image:: /_static/machine_learn_intro/v2-ed89d490e3cc5ad3d1dacaeeb3a0d3a8_r.jpg
.. |image25| image:: /_static/machine_learn_intro/v2-79fde2463ed01ba10ac2cbafd39c60c1_r.jpg
.. |image26| image:: /_static/machine_learn_intro/v2-4af89c5ca4398e72005d3a1683715c85_r.jpg
.. |image27| image:: /_static/machine_learn_intro/v2-2e2b310607382faefc56d32537a9813b_r.jpg
.. |image28| image:: /_static/machine_learn_intro/v2-ff3a4ede29322e75ec0d445b21f00a65_r.jpg
.. |image29| image:: /_static/machine_learn_intro/v2-6e246d8b84110d70c49b5b6ce2310434_r.jpg
.. |image30| image:: /_static/machine_learn_intro/v2-958c57c4ad84b079640a261378e667e0_r.jpg






























































































