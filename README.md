# F044 vue3+flask房屋推荐可视化系统|vue+flask+pinia+mysql+echarts

> 完整项目收费，可联系QQ: 81040295 微信: mmdsj186011 注明从github来的，谢谢！
也可以关注我的B站： 麦麦大数据 https://space.bilibili.com/1583208775
> 

关注B站，有好处！
编号:  F044
## 视频

https://www.bilibili.com/video/BV1drk2BFEzK/

## 1 系统简介
系统简介：本系统是一个基于Vue3+Flask构建的房屋推荐可视化系统，其核心功能围绕房地产数据的展示、推荐和用户管理展开。主要包括：首页，用于展示系统概览和轮播图；房产卡片，提供房地产数据的概览，并支持查看位置（例如通过百度地图）及用户点赞“喜欢”的功能；可视化模块，通过ECharts图表展示房产数据，包括价格走势、区域分布等信息，并生成词云分析房产特征；推荐算法模块，利用机器学习模型进行智能房屋推荐，为用户提供个性化服务；以及用户管理模块，包含登录、注册功能，以及个人设置（允许用户修改个人信息、头像及密码），确保系统的安全性和个性化体验。。
## 2 功能设计
该系统采用典型的B/S（浏览器/服务器）架构模式。用户通过浏览器访问Vue前端界面，该前端由HTML、CSS、JavaScript以及Vue.js生态系统中的Vuex（用于状态管理）、Vue Router（用于路由导航）和Echarts（用于数据可视化）等组件构建。前端通过API请求与Flask后端进行数据交互，Flask后端则负责业务逻辑处理，并利用SQLAlchemy（或类似ORM工具）与MySQL数据库进行持久化数据存储。此外，系统还包含一个独立的爬虫模块，负责从外部来源抓取数据并将其导入MySQL数据库，为整个系统提供数据支撑。
### 2.1系统架构图
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/f7df91de8e1b41cbaa86903a9048e767.jpeg)
### 2.2 功能模块图
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/d46fe5b8d34a41bd924421740a813b72.jpeg)
## 3 功能展示
### 3.1 登录 & 注册
登录注册做的是一个可以切换的登录注册界面，点击去登录后者去注册可以切换，背景是一个视频，循环播放。
登录需要验证用户名和密码是否正确，如果**不正确会有错误提示**。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/b8d543b62f0042df943aa5c001a1d218.png)
注册需要**验证用户名是否存在**，如果错误会有提示。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/3c586d6aa55240c6a801f3b7bb927131.png)
### 3.2 主页
主页的布局采用了上侧是菜单，下侧是操作面板的布局方法，右侧的上方还有用户的头像和退出按钮，如果是新注册用户，没有头像，这边则不显示，需要在个人设置中上传了头像之后就会显示。
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/b2381318c0324f05b53ba11ed8a403f1.png)
### 3.3 二手房推荐
推荐加载：
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/a1823cd39da8440eb368589e09ae8983.png)
推荐二手房卡片：
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/c8ebabf4aa3a4f33a94cf00a35097c6f.png)
### 3.4 二手房数据分析
利用echarts进行二手房分析：
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/d81b5fd461e04c8b8e24d0b0690b8d54.png)
### 3.5 二手房词云分析
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/e53ad63083614d49a10f38299e202ab6.png)
### 3.6 数据查询
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/289e826f00734717b3c221216757ac6c.png)
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/bbb3973faabe4007a00d536f9ce5dbd4.png)
### 3.7 个人设置
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/6318771b52694f72ad20314770d19a55.png)
## 4程序代码
### 4.1 代码说明
代码介绍：基于协同过滤和内容推荐混合的二手房屋推荐系统的Python实现。代码主要包括数据加载、特征提取、推荐算法和结果生成四个主要部分。系统会根据用户的历史行为和偏好（如价格范围、区域、房间数等）以及房屋的特征（如面积、楼层、装修情况等）进行推荐。
### 4.2 流程图
![在这里插入图片描述](https://i-blog.csdnimg.cn/direct/71d438e7cb4745698a8acbeb82f6de44.png)
### 4.3 代码实例
```python
# 二手房屋推荐系统

import pandas as pd
from sklearn.neighbors import NearestNeighbors
from collections import defaultdict

class HouseRecommender:
    def __init__(self):
        # 加载用户数据和房屋数据
        self.user_data = pd.read_csv("user_preferences.csv")
        self.house_data = pd.read_csv("house_info.csv")
        
        # 提取用户特征（如价格范围、区域偏好、房间数）
        self.user_features = self._extract_user_features()
        
        # 提取房屋特征（如价格、面积、楼层、装修情况等）
        self.house_features = self._extract_house_features()
        
    def _extract_user_features(self):
        # 从用户数据中提取特征
        features = []
        for _, user in self.user_data.iterrows():
            features.append([
                user['budget'],  # 预算
                user['preferred_area'],  # 偏好区域
                user['bedroom_num']  # 房间数
            ])
        return features
    
    def _extract_house_features(self):
        # 从房屋数据中提取特征
        features = []
        for _, house in self.house_data.iterrows():
            features.append([
                house['price'],  # 价格
                house['area'],  # 面积
                house['floor'],  # 楼层
                house['renovation']  # 装修情况
            ])
        return features
    
    def recommend(self, user_id, num_recommendations=5):
        # 使用协同过滤和内容推荐混合算法进行推荐
        # 1. 找到相似的用户
        similar_users = self._find_similar_users(user_id)
        
        # 2. 根据相似用户的偏好推荐房屋
        recommended_houses = self._get_recommended_houses(similar_users)
        
        # 3. 根据内容推荐进一步过滤
        filtered_houses = self._filter_by_content(recommended_houses, user_id)
        
        return filtered_houses[:num_recommendations]
    
    def _find_similar_users(self, user_id):
        # 使用KNN算法找到相似的用户
        knn = NearestNeighbors(n_neighbors=5)
        knn.fit(self.user_features)
        user_feature = self.user_features[user_id]
        distances, indices = knn.kneighbors([user_feature])
        return indices[0]

    def _get_recommended_houses(self, user_ids):
        # 根据相似用户的历史行为推荐房屋
        recommended = defaultdict(int)
        for user_id in user_ids:
            for house_id in self.user_data.iloc[user_id]['history']:
                recommended[house_id] += 1
        return sorted(recommended.items(), key=lambda x: x[1], reverse=True)
    
    def _filter_by_content(self, recommended_houses, user_id):
        # 根据用户的内容偏好进行过滤
        user_budget = self.user_data.iloc[user_id]['budget']
        user_area = self.user_data.iloc[user_id]['preferred_area']
        filtered = []
        for house_id, _ in recommended_houses:
            house = self.house_data.iloc[house_id]
            if house['price'] <= user_budget and house['area'] == user_area:
                filtered.append(house_id)
        return filtered

# 使用示例
recommender = HouseRecommender()
recommended_houses = recommender.recommend(0)  # 推荐用户0
print("推荐的房屋ID:", recommended_houses)

```
