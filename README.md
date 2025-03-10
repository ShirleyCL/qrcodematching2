# qrcodematching2
实验二维码3
<script src="https://unpkg.com/html5-qrcode/minified/html5-qrcode.min.js"></script>
<!DOCTYPE html>
<html lang="zh">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>双二维码扫描</title>
    <style>
        body {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            height: 100vh;
            margin: 0;
            background-color: #f7f7f7;
        }
        #camera {
            width: 100%;
            max-width: 600px;
            margin-bottom: 20px;
        }
        #result {
            margin-top: 20px;
            font-size: 20px;
        }
        .qr-box {
            width: 200px;
            height: 200px;
            border: 2px dashed #000;
            position: relative;
        }
        .qr-box[data-qr-index="0"] {
            margin-right: 20px;
        }
        .qr-box div {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(255, 255, 255, 0.5);
        }
    </style>
</head>
<body>
    <div id="camera" class="qr-box" data-qr-index="0"></div>
    <div id="camera2" class="qr-box" data-qr-index="1"></div>
    <div id="result"></div>

    <script>
        let scannedQRCode1 = null;
        let scannedQRCode2 = null;

        const html5QrCode1 = new Html5Qrcode("camera");
        const html5QrCode2 = new Html5Qrcode("camera2");

        function onScanSuccess(qrCodeMessage, qrIndex) {
            if (qrIndex === 0 && !scannedQRCode1) {
                scannedQRCode1 = qrCodeMessage;
                document.getElementById("result").innerText = `第一个二维码: ${scannedQRCode1}`;
            } else if (qrIndex === 1 && !scannedQRCode2) {
                scannedQRCode2 = qrCodeMessage;
                document.getElementById("result").innerText += `\n第二个二维码: ${scannedQRCode2}`;
            }

            if (scannedQRCode1 && scannedQRCode2) {
                // 比较两个二维码
                if (scannedQRCode1 === scannedQRCode2) {
                    document.getElementById("result").innerText += "\n两个二维码一致!";
                } else {
                    document.getElementById("result").innerText += "\n二维码不一致!";
                }
                // 停止扫描
                html5QrCode1.stop();
                html5QrCode2.stop();
            }
        }

        const startScanning = () => {
            html5QrCode1.start(
                { facingMode: "environment" }, // 使用环境摄像头
                {
                    fps: 10,
                    qrbox: { width: 200, height: 200 } // 定义二维码框的大小
                },
                (qrCodeMessage) => onScanSuccess(qrCodeMessage, 0) // 第一个二维码
            ).catch(err => {
                console.error("无法启动扫码器1.", err);
            });

            html5QrCode2.start(
                { facingMode: "environment" },
                {
                    fps: 10,
                    qrbox: { width: 200, height: 200 }
                },
                (qrCodeMessage) => onScanSuccess(qrCodeMessage, 1) // 第二个二维码
            ).catch(err => {
                console.error("无法启动扫码器2.", err);
            });
        };

        startScanning(); // 开始扫描二维码
    </script>
</body>
</html>
