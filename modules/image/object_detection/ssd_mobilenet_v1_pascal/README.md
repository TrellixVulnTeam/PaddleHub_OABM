# ssd_mobilenet_v1_pascal

|模型名称|ssd_mobilenet_v1_pascal|
| :--- | :---: |
|类别|图像 - 目标检测|
|网络|SSD|
|数据集|PASCAL VOC|
|是否支持Fine-tuning|否|
|模型大小|24MB|
|最新更新日期|2021-02-26|
|数据指标|-|


## 一、模型基本信息

- ### 应用效果展示
  - 样例结果示例：
     <p align="center">
     <img src="https://user-images.githubusercontent.com/22424850/131504887-d024c7e5-fc09-4d6b-92b8-4d0c965949d0.jpg"   width='50%' hspace='10'/>
     <br />
     </p>

- ### 模型介绍

  - Single Shot MultiBox Detector (SSD) 是一种单阶段的目标检测器。与两阶段的检测方法不同，单阶段目标检测并不进行区域推荐，而是直接从特征图回归出目标的边界框和分类概率。SSD 运用了这种单阶段检测的思想，并且对其进行改进：在不同尺度的特征图上检测对应尺度的目标。该PaddleHub Module的基网络为MobileNet-v1模型，在Pascal数据集上预训练得到，目前仅支持预测。


## 二、安装

- ### 1、环境依赖  

  - paddlepaddle >= 1.6.2  

  - paddlehub >= 1.6.0  | [如何安装paddlehub](../../../../docs/docs_ch/get_start/installation.rst)

- ### 2、安装

  - ```shell
    $ hub install ssd_mobilenet_v1_pascal
    ```
  - 如您安装时遇到问题，可参考：[零基础windows安装](../../../../docs/docs_ch/get_start/windows_quickstart.md)
 | [零基础Linux安装](../../../../docs/docs_ch/get_start/linux_quickstart.md) | [零基础MacOS安装](../../../../docs/docs_ch/get_start/mac_quickstart.md)

## 三、模型API预测

- ### 1、命令行预测

  - ```shell
    $ hub run ssd_mobilenet_v1_pascal --input_path "/PATH/TO/IMAGE"
    ```
  - 通过命令行方式实现目标检测模型的调用，更多请见 [PaddleHub命令行指令](../../../../docs/docs_ch/tutorial/cmd_usage.rst)
- ### 2、预测代码示例

  - ```python
    import paddlehub as hub
    import cv2

    object_detector = hub.Module(name="ssd_mobilenet_v1_pascal")
    result = object_detector.object_detection(images=[cv2.imread('/PATH/TO/IMAGE')])
    # or
    # result = object_detector.object_detection((paths=['/PATH/TO/IMAGE'])
    ```

- ### 3、API

  - ```python
    def object_detection(paths=None,
                         images=None,
                         batch_size=1,
                         use_gpu=False,
                         output_dir='detection_result',
                         score_thresh=0.5,
                         visualization=True,
                         )
    ```

    - 预测API，检测输入图片中的所有目标的位置。

    - **参数**

      - paths (list\[str\]): 图片的路径； <br/>
      - images (list\[numpy.ndarray\]): 图片数据，ndarray.shape 为 \[H, W, C\]，BGR格式； <br/>
      - batch\_size (int): batch 的大小；<br/>
      - use\_gpu (bool): 是否使用 GPU；<br/>
      - output\_dir (str): 图片的保存路径，默认设为 detection\_result; <br/>
      - score\_thresh (float): 识别置信度的阈值；<br/>
      - visualization (bool): 是否将识别结果保存为图片文件。

      **NOTE:** paths和images两个参数选择其一进行提供数据


    - **返回**

      - res (list\[dict\]): 识别结果的列表，列表中每一个元素为 dict，各字段为:
        - data (list): 检测结果，list的每一个元素为 dict，各字段为:
          - confidence (float): 识别的置信度
          - label (str): 标签
          - left (int): 边界框的左上角x坐标
          - top (int): 边界框的左上角y坐标
          - right (int): 边界框的右下角x坐标
          - bottom (int): 边界框的右下角y坐标
        - save\_path (str, optional): 识别结果的保存路径 (仅当visualization=True时存在)

  - ```python
    def save_inference_model(dirname)
    ```
    - 将模型保存到指定路径。

    - **参数**

      - dirname: 模型保存路径 <br/>


## 四、服务部署

- PaddleHub Serving可以部署一个目标检测的在线服务。

- ### 第一步：启动PaddleHub Serving

  - 运行启动命令：
  - ```shell
    $ hub serving start -m ssd_mobilenet_v1_pascal
    ```

  - 这样就完成了一个目标检测的服务化API的部署，默认端口号为8866。

  - **NOTE:** 如使用GPU预测，则需要在启动服务之前，请设置CUDA\_VISIBLE\_DEVICES环境变量，否则不用设置。

- ### 第二步：发送预测请求

  - 配置好服务端，以下数行代码即可实现发送预测请求，获取预测结果

  - ```python
    import requests
    import json
    import cv2
    import base64


    def cv2_to_base64(image):
      data = cv2.imencode('.jpg', image)[1]
      return base64.b64encode(data.tostring()).decode('utf8')

    # 发送HTTP请求
    data = {'images':[cv2_to_base64(cv2.imread("/PATH/TO/IMAGE"))]}
    headers = {"Content-type": "application/json"}
    url = "http://127.0.0.1:8866/predict/ssd_mobilenet_v1_pascal"
    r = requests.post(url=url, headers=headers, data=json.dumps(data))

    # 打印预测结果
    print(r.json()["results"])
    ```


## 五、更新历史

* 1.0.0

  初始发布

* 1.1.2

  修复numpy数据读取问题

* 1.1.3

  移除 fluid api

* 1.2.0

  修复推理模型无法导出的问题

  - ```shell
    $ hub install ssd_mobilenet_v1_pascal==1.2.0
    ```
