# 陪驾小程序
## 名称：
- DriveBuddy
- 陪驾达人


## 业务逻辑
### Phase 1
- 师傅可以定义空闲时间段
- 用户可以预约空闲时间段
- 两类用户/师傅
	- 有车
	- 无车
### Phase 2
- 对师傅进行认证 （线下）
	- 已认证/未认证师傅
- 根据用户地址进行附近匹配
- 向用户推荐师傅（避免 无车 + 无车 的组合）
### Phase 3
- 用户可以发出需求，师傅抢单
### Phase 4
- 在线支付
	- 定金
	- 尾款
- 评价

## 模块划分及各模块流程
### 登录管理

### 个人信息管理
#### 陪练 (training partner)
- 更新信息
	- 姓名
	- 手机
	- 地址
	- 驾照
    	- 编号
    	- 照片
	- 车辆信息
    	- 品牌
    	- 型号
    	- 手动/自动
    	- 轿车/suv
- 提交审核
##### 状态定义
1. 未完成审核 (draft)
1. 更新后待审核 (pending)
1. 审核通过 (approved)

##### 主页管理
- 可预约时间
- 自我介绍相关
- 特长相关

#### 学员 (customer)
- 更新信息
  - 姓名
  - 手机
  - 地址

### 训练
- 获取陪练信息列表
- 学员预约
- 陪练确认预约
- 完成训练
- 评价
#### 训练管理
##### 状态定义
1. 学员发起待陪练确认 (initial)
	1. 学员撤回 (canceled)
1. 陪练确认待训练 (scheduled)
	1. 陪练拒绝 (rejected)
1. 到达预定时间时，训练中 (training)
1. 预定时间之后，训练完成待确认 (pending)
	1. 存在异议 (disputed)
	1. 未完成的训练 (incompleted)
1. 双方确认后完成训练 (completed)
1. 用户完成评论 (reviewed)



## 实体定义
### 用户 (user)
- id (微信 id)
- nick_name (微信昵称)
- register_ts (注册时间)
- initial_login_ts (首次登录时间)
- last_login_ts (上次登录时间)
- partner_id (陪练id，fk)

#### 陪练 (trainingPartner)
- id
- register_ts (注册时间)
- name (姓名)
- identity_number (身份证号码)
- phone_number
- address
- license_id
- license_type
- license_register_date
- license_expire_date
- validation_state (身份验证状态)
- updated_ts (信息更新时间)
- validated_ts (验证通过时间)

##### 车辆信息 (vehicle)
- id
- brand (品牌)
- model (型号)
- type (车辆类型，轿车/suv/mpv)
- is_auto (是否为自动挡)
- number_plate (车牌号)
- user_id (用户id)

##### 可预约时间 (partinerSchedule)
- id
- date (日期)
- start_hour (开始小时)
- end_hour (结束小时)
- partner_id (陪练id，fk)
- is_scheduled (是否已被预约)

### 训练 (trainingOrder)
- id
- partner_id (陪练id，fk)
- customer_id (用户id，fk)
- vehicle_id (车辆id，fk)
- schedule_id (预约时间id，fk)
- state (状态)
- review (评论)


## 接口定义
