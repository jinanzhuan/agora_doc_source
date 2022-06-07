## 整体架构

下图展示了灵动课堂的整体产品架构：

![](https://web-cdn.agora.io/docs-files/1651722021414)

## 基础功能

| <span style="white-space:nowrap;">功能&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;</span> | 说明                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| :-------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 实时音视频互动                                                                          | <li>1 对 1 互动教学场景中，学生和老师默认都可以发送和接收音视频流。</li><li>在线互动小班课和互动直播大班课中，学生默认不发送音视频流。上课过程中，学生可“上讲台”发送音视频流。</li>                                                                                                                                                                                                                                                                                                                                                    |
| 实时消息互动                                                                            | 老师和学生可以在课堂中发送文字、表情、图片进行实时消息互动。                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| 互动白板                                                                                | 老师和学生可在白板上进行绘画涂鸦，支持画笔、文本框、图形、橡皮擦、分页、激光笔等功能。                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| 课件管理                                                                                | 老师可以在课堂中上传本地课件供教学使用。支持的文件格式包含：PPT、PPTX、DOC、DOCX、PDF、MP3、MP4、PNG、JPG、GIF。                                                                                                                                                                                                                                                                                                                                                                                                                       |
| 云端录制                                                                                | 老师可在课堂中开启录制。灵动课堂会通过[页面录制](https://docs.agora.io/cn/cloud-recording/cloud_recording_webpage_mode?platform=RESTful)将指定 URL 的页面内容和音频混合录制为一个 MP4 音视频文件，实现音视频内容、白板内容、课件内容同步录制。                                                                                                                                                                                                                                                                                         |
| 屏幕共享                                                                                | 老师可在课堂中将自己的屏幕、窗口或浏览器标签页分享给学生观看。                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| 互动教学道具                                                                            | <li>课堂奖励：老师可在上课过程中授予学生星星、奖杯等形式的奖励。</li><li>答题器：适用于课堂中老师提问、全班学生一起答题的场景。老师端可在答题器中添加或减少选项数量并设置正确选项，然后发起答题。答题结束后，可统计并展示提交人数和正确率。</li><li>投票器：适用于课堂中老师向全班学生征集意见的场景。老师端可在投票器中设置投票主题、选项以及投票的起始和截止时间，然后发起投票。投票结束后，可统计并展示投票结果。</li><li>倒计时：老师可在工具箱中打开倒计时工具并设置倒计时数值。老师点击开始倒计时后学生端会出现倒计时面板。</li> |

## 进阶功能

<table>
<thead>
  <tr>
    <th>模块</th>
    <th>功能</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td rowspan="9">房间</td>
    <td>配置房间自定义属性，用于实现房间层级的个性化业务需求，例如可在课程中添加课间休息状态。</td>
  </tr>
  <tr>
    <td>配置课堂开始时间，适用于提前排课、课堂到点自动开始的场景。</td>
  </tr>
  <tr>
    <td>配置课堂持续时间，适用于提前排课、课堂到点自动结束的场景。</td>
  </tr>
  <tr>
    <td>配置拖堂时间。拖堂时长后，房间关闭，房间里的用户会立即被踢出房间，其它用户无法也再加入该房间。</td>
  </tr>
  <tr>
    <td>配置上讲台学生人数上限。默认情况下，同时上讲台人数最多 6 人。</td>
  </tr>
  <tr>
    <td>配置学生举手人数上限。默认情况下，同时举手人数默认最多 10 人。</td>
  </tr>
  <tr>
    <td>配置学生进入教室后是否默认直接上讲台。</td>
  </tr>
  <tr>
    <td>配置区域，用于确保教室所在区域和用于存储课件及录制文件的 OSS 所在区域保持一致。</td>
  </tr>
  <tr>
    <td>支持课中事件监听，实时同步课堂内发生的事件。</td>
  </tr>
  <tr>
    <td rowspan="5">用户</td>
    <td>配置用户自定义属性，用于设置头像、年龄等属性。</td>
  </tr>
  <tr>
    <td>用户列表。展示课堂中所有用户的上下台状态、摄像头状态、麦克风状态、奖励个数等信息。</td>
  </tr>
  <tr>
    <td>自定义奖励。</td>
  </tr>
  <tr>
    <td>指定学生上讲台发言。</td>
  </tr>
  <tr>
    <td>踢人。</td>
  </tr>
  <tr>
    <td rowspan="3">流</td>
    <td>配置视频编码属性（码率、分辨率和镜像模式）。</td>
  </tr>
  <tr>
    <td>加密音视频流。</td>
  </tr>
  <tr>
    <td>配置发流权限。</td>
  </tr>
  <tr>
    <td rowspan="3">设备和媒体</td>
    <td>开关和检测音视频设备（耳机、麦克风）。</td>
  </tr>
  <tr>
    <td>控制视频渲染。</td>
  </tr>
  <tr>
    <td>控制音频播放。</td>
  </tr>
  <tr>
    <td rowspan="3">UIKit/UIStore</td>
    <td>配置多语言。</td>
  </tr>
  <tr>
    <td>调整教室布局。</td>
  </tr>
  <tr>
    <td>修改课堂颜色。</td>
  </tr>
  <tr>
    <td>Widget</td>
    <td>用于实现可插拔的自定义插件，如互动白板、答题器、倒计时。</td>
  </tr>
  <tr>
    <td rowspan="3"></td>
    <td>配置录制视频分辨率。</td>
  </tr>
  <tr>
    <td>配置录制文件存储地址。</td>
  </tr>
  <tr>
    <td>配置录制启动时间和结束时间。</td>
  </tr>
</tbody>
</table>