# qrcodematching2
实验二维码5
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>双二维码比对系统</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/vue/2.1.10/vue.min.js"></script>
    <script src="https://cdn.rawgit.com/schmich/instascan-builds/master/instascan.min.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Arial', sans-serif;
            background: #f0f2f5;
        }

        .container {
            width: 100%;
            min-height: 100vh;
            padding: 20px;
            position: relative;
        }

        .step {
            display: none;
            flex-direction: column;
            align-items: center;
            gap: 20px;
        }

        .active {
            display: flex;
        }

        #preview {
            width: 100%;
            max-width: 500px;
            height: 300px;
            border-radius: 10px;
            overflow: hidden;
            background: #000;
        }

        .result-box {
            width: 100%;
            max-width: 500px;
            padding: 20px;
            background: #fff;
            border-radius: 10px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
        }

        .confirm-btn {
            width: 100%;
            max-width: 500px;
            padding: 15px;
            background: #007bff;
            color: white;
            border: none;
            border-radius: 25px;
            font-size: 16px;
            cursor: pointer;
            position: fixed;
            bottom: 30px;
            left: 50%;
            transform: translateX(-50%);
            transition: background 0.3s;
        }

        .confirm-btn:active {
            background: #0056b3;
        }

        .status {
            font-size: 18px;
            color: #666;
            text-align: center;
        }
    </style>
</head>
<body>
    <div class="container">
        <!-- 步骤1：扫描第一个二维码 -->
        <div class="step active" id="step1">
            <h2 class="status">请扫描第一个二维码</h2>
            <div id="preview"></div>
        </div>

        <!-- 步骤2：显示第一个二维码信息 -->
        <div class="step" id="step2">
            <h2 class="status">第一个二维码信息</h2>
            <div class="result-box" id="result1"></div>
            <button class="confirm-btn" onclick="startSecondScan()">确认并扫描第二个二维码</button>
        </div>

        <!-- 步骤3：扫描第二个二维码 -->
        <div class="step" id="step3">
            <h2 class="status">请扫描第二个二维码</h2>
            <div id="preview2"></div>
        </div>

        <!-- 步骤4：显示比对结果 -->
        <div class="step" id="step4">
            <h2 class="status">比对结果</h2>
            <div class="result-box" id="finalResult"></div>
        </div>
    </div>

<script>
    let scanner = null;
    let firstResult = '';
    let secondResult = '';

    // 初始化第一个扫描
    function initFirstScan() {
        Instascan.Camera.getCameras().then(function(cameras) {
            if (cameras.length > 0) {
                scanner = new Instascan.Scanner({
                    video: document.getElementById('preview'),
                    mirror: false,
                    captureImage: false
                });

                scanner.addListener('scan', function(content) {
                    firstResult = content;
                    showStep(2);
                    document.getElementById('result1').innerHTML = content;
                    scanner.stop();
                });

                scanner.start(cameras[0]);
            } else {
                alert('未找到摄像头设备');
            }
        });
    }

    // 开始第二个扫描
    function startSecondScan() {
        showStep(3);
        Instascan.Camera.getCameras().then(function(cameras) {
            if (cameras.length > 0) {
                scanner = new Instascan.Scanner({
                    video: document.getElementById('preview2'),
                    mirror: false,
                    captureImage: false
                });

                scanner.addListener('scan', function(content) {
                    secondResult = content;
                    showStep(4);
                    compareResults();
                    scanner.stop();
                });

                scanner.start(cameras[0]);
            }
        });
    }

    // 比对结果
    function compareResults() {
        const resultDiv = document.getElementById('finalResult');
        if (firstResult === secondResult) {
            resultDiv.innerHTML = '✅ 两个二维码内容一致';
            resultDiv.style.color = 'green';
        } else {
            resultDiv.innerHTML = '❌ 内容不一致<br><br>第一个二维码：' + 
                                firstResult + '<br><br>第二个二维码：' + secondResult;
            resultDiv.style.color = 'red';
        }
    }

    // 步骤切换
    function showStep(stepNumber) {
        document.querySelectorAll('.step').forEach(div => {
            div.classList.remove('active');
        });
        document.getElementById('step' + stepNumber).classList.add('active');
    }

    // 初始化
    window.onload = initFirstScan;
</script>
</body>
</html>
