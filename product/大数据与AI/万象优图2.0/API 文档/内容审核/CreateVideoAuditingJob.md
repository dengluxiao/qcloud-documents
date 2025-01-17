## 功能描述

CreateVideoAuditingJob 接口用于提交一个视频审核任务。您可以通过主动设置回调地址接收审核信息，也可以通过 Jobid 进行查询。

## 请求

#### 请求示例

```plaintext
POST /video/auditing HTTP/1.1
Host: <BucketName-APPID>.ci.<Region>.myqcloud.com
Date: <GMT Date>
Authorization: <Auth String>
Content-Length: <length>
Content-Type: application/xml



<body>
```

> ?Authorization: Auth String（详情请参见 [请求签名](https://cloud.tencent.com/document/product/436/7778) 文档）。

#### 请求头

此接口仅使用公共请求头部，详情请参见 [公共请求头部](https://cloud.tencent.com/document/product/460/42865) 文档。

#### 请求体

该请求操作的实现需要有如下请求体。

```plaintext
<Request>
  <Input>
    <Object></Object>
  </Input>
  <Conf>
    <DetectType>Porn,Terrorism,Politics,Ads</DetectType>
    <Snapshot>
        <Mode>Interval</Mode>
        <TimeInterval></TimeInterval>
        <Count></Count>
    </Snapshot>
    <Callback></Callback>
    <BizType></BizType>
    <DetectContent></DetectContent>
  </Conf>
</Request>
```

具体的数据描述如下：

| 节点名称（关键字） | 父节点 | 描述           | 类型      | 是否必选 |
| ------------------ | ------ | -------------- | --------- | -------- |
| Request            | 无     | 保存请求的容器 | Container | 是       |

Container 类型 Request 的具体数据描述如下：

| 节点名称（关键字） | 父节点  | 描述             | 类型      | 是否必选 |
| ------------------ | ------- | ---------------- | --------- | -------- |
| Input              | Request | 待操作的媒体信息 | Container | 是       |
| Conf               | Request | 操作规则         | Container | 是       |

Container 类型 Input 的具体数据描述如下：

| 节点名称（关键字） | 父节点        | 描述           | 类型   | 是否必选 |
| ------------------ | ------------- | -------------- | ------ | -------- |
| Object             | Request.Input | 媒体文件的名称 | String | 是       |

Container 类型 Conf 的具体数据描述如下：

| 节点名称（关键字） | 父节点       | 描述                                                         | 类型      | 是否必选 |
| ------------------ | ------------ | ------------------------------------------------------------ | --------- | -------- |
| DetectType         | Request.Conf | 审核类型：涉黄 Porn、涉暴恐 Terrorism、政治敏感 Politics、广告 Ads，可以传入多种类型 | String    | 是   |
| Snapshot           | Request.Conf | 视频截帧的配置                                            | Container | 是   |
| Callback           | Request.Conf | 回调地址，以`http://`或者`https://`开头的地址                  | String    | 否       |
| BizType            | Request.Conf | 审核策略，不带审核策略时使用默认策略                         | String    | 否   |
| DetectContent      | Request.Conf | 审核内容开关。0: 只审截图，1: 审核截图和音频。默认为0        | String    | 否   |

Container 类型 Snapshot 的具体数据描述如下：

| 节点名称（关键字） | 父节点                | 描述                                                         | 类型      | 是否必选 |
| ------------------ | :-------------------- | ------------------------------------------------------------ | --------- | -------- |
| Mode               | Request.Conf.Snapshot | 截帧模式。Interval 表示间隔模式；Average 表示平均模式；Fps 表示固定帧率模式。</br><li> Interval 模式：TimeInterval，Count 参数生效。当设置 Count，未设置 TimeInterval 时，表示截取所有帧，共 Count 张图片</br><li> Average 模式：Count 参数生效。表示整个视频，按平均间隔截取共 Count 张图片</br><li> Fps 模式：TimeInterval 表示每秒截取多少帧，Count 表示共截取多少帧 | String | 否       |
| Count              | Request.Conf.Snapshot | 视频截帧数量，范围为(0, 10000]                                    | String    | 否       |
| TimeInterval       | Request.Conf.Snapshot | 视频截帧频率，范围为(0, 60]，单位为秒，支持 float 格式，执行精度精确到毫秒 | String    | 否       |

## 响应

#### 响应头

此接口仅返回公共响应头部，详情请参见 [公共响应头部](https://cloud.tencent.com/document/product/460/42866) 文档。 

#### 响应体

该响应体返回为 **application/xml** 数据，包含完整节点数据的内容展示如下：

```plaintext
<Response>
    <JobsDetail>
      <JobId></JobId>
      <State></State>
      <CreationTime></CreationTime>
    </JobsDetail>
</Response>
```

具体的数据内容如下：

| 节点名称（关键字） | 父节点 | 描述           | 类型      |
| :----------------- | :----- | :------------- | :-------- |
| Response           | 无     | 保存结果的容器 | Container |

Container 节点 Response 的内容：

| 节点名称（关键字） | 父节点   | 描述           | 类型      |
| :----------------- | :------- | :------------- | :-------- |
| JobsDetail         | Response | 任务的详细信息 | Container |

Container 节点 JobsDetail 的内容：

| 节点名称（关键字） | 父节点              | 描述                                                         | 类型   |
| :----------------- | :------------------ | :----------------------------------------------------------- | :----- |
| JobId              | Response.JobsDetail | 新创建视频审核任务的 ID                                       | String |
| State              | Response.JobsDetail | 任务的状态，为 Submitted（已提交审核）、Snapshoting（视频截帧中）、Success（审核成功）、Failed（审核失败）、Auditing（审核中）其中一个 | String |
| CreationTime       | Response.JobsDetail | 任务的创建时间                                               | String |

#### 错误码

该请求操作无特殊错误信息，常见的错误信息请参见 [错误码](https://cloud.tencent.com/document/product/460/42867) 文档。

## 实际案例

#### 请求

```plaintext
POST /video/auditing HTTP/1.1
Authorization: q-sign-algorithm=sha1&q-ak=AKIDZfbOAo7cllgPvF9cXFrJD0a1ICvR****&q-sign-time=1497530202;1497610202&q-key-time=1497530202;1497610202&q-header-list=&q-url-param-list=&q-signature=28e9a4986df11bed0255e97ff90500557e0e****
Host: examplebucket-1250000000.ci.ap-beijing.myqcloud.com
Content-Length: 166
Content-Type: application/xml



<Request>
    <Input>
       <Object>a.mp4</Object>
    </Input>
    <Conf>
        <DetectType>Porn,Terrorism,Politics,Ads</DetectType>
        <Snapshot>
            <Mode>Interval</Mode>
            <Start>0.5</Start>
            <TimeInterval>50</TimeInterval>
            <Count>100</Count>
        </Snapshot>
        <Callback>http://callback.com/</Callback>
    </Conf>
</Request>
```

#### 响应

```plaintext
HTTP/1.1 200 OK
Content-Type: application/xml
Content-Length: 230
Connection: keep-alive
Date: Thu, 15 Jun 2017 12:37:29 GMT
Server: tencent-ci
x-ci-request-id: NTk0MjdmODlfMjQ4OGY3XzYzYzhf****



<Response>
    <JobsDetail>
       <JobId>vab1ca9fc8a3ed11ea834c525400863904</JobId>
       <State>Submitted</State>
       <CreationTime>2019-07-07T12:12:12+0800</CreationTime>
    </JobsDetail>
</Response>
```
	
	
	
