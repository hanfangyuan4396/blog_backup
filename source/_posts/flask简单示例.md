---
title: flask简单示例
date: 2022-11-19 18:34:04
reward: true
tags: flask
---

### 安装依赖
```
pip install flask -i https://pypi.douban.com/simple
pip install flask_cors -i https://pypi.douban.com/simple
```

### 简单示例
<!-- more -->
```python
# app.py
from flask import Flask, request, send_from_directory, jsonify
from flask_cors import CORS

app = Flask(__name__) # 创建flask应用
CORS(app) # 支持跨域访问

# 默认为get请求
@app.route('/hello')
def hello():
    return 'hello world' # 返回text/html类型响应，是一个html文件，<body>hello world</body>

# 上传文件
@app.route('/upload', methods=['POST'])
def upload():
    f = request.files['filename']
    f.save('xxx.txt')
    # jsonify()返回application/json类型的响应 request.json解析post的json数据
    return jsonify({'message': 'upload success'}) 

# 文件服务器
@app.route('/download/<path:name>')
def download(name):
    return send_from_directory(r'E:\xxx\xxx', name, as_attachment=True)

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=8080, debug=True)
```

### 启动
```
python app.py
```