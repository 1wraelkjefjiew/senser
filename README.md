# senser　on github
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>QRコード課題</title>
    <script src="https://unpkg.com/html5-qrcode"></script>
</head>
<body>

    <h2>バーコードスキャンテスト</h2>
    
    <div id="reader" style="width:300px; display: none;"></div>
    
    <button id="startButton">スキャン開始</button>
    <button id="resultButton">スキャン結果を表示</button>
    
    <p id="result">スキャン結果: <span id="lastResult">(まだありません)</span></p>

    <script>
        // 課題1: サイズ3の配列を準備し、文字列を入れておく 
        const items = ["りんご", "みかん", "バナナ"];

        // 資料のコードを参考にした変数定義 [cite: 352, 353, 354, 355, 356, 357]
        let lastScannedCode = null;
        const breader = new Html5Qrcode("reader");
        const startButton = document.getElementById("startButton");
        const resultButton = document.getElementById("resultButton");
        const resultDisplay = document.getElementById("lastResult");
        let isScanning = false;

        // 「スキャン開始」ボタンの処理 [cite: 358]
        startButton.addEventListener("click", () => {
            if (isScanning) {
                // スキャンを停止する処理 [cite: 361, 404]
                breader.stop().then(() => {
                    document.getElementById("reader").style.display = "none"; // リーダー画面を非表示 [cite: 362, 405]
                    startButton.textContent = "スキャン開始"; // ボタンの文字を戻す [cite: 363, 406]
                    isScanning = false;
                }).catch(err => {
                    console.error("スキャナの停止に失敗しました。", err);
                });
            } else {
                // スキャンを開始する処理 [cite: 366]
                document.getElementById("reader").style.display = "block"; // リーダー画面を表示 [cite: 373]
                breader.start(
                    { facingMode: "environment" }, // 背面カメラを使用 [cite: 375]
                    { fps: 10, qrbox: 250 },       // スキャン設定 [cite: 376]
                    (decodedText, decodedResult) => {
                        // 読み取り成功時、結果をlastScannedCode変数に保存する 
                        lastScannedCode = decodedText;
                        alert(`「${decodedText}」を読み取りました。「スキャン停止」後、「結果を表示」ボタンを押してください。`);
                        
                        // 読み取り後に自動で停止させたい場合は、下のstop()処理を有効化します
                        // breader.stop();
                        // isScanning = false;
                        // startButton.textContent = "スキャン開始";
                        // document.getElementById("reader").style.display = "none";
                    },
                    (errorMessage) => {
                        // 読み取り失敗時の処理 (何もしない) [cite: 382]
                    }
                ).then(() => {
                    startButton.textContent = "スキャン停止"; // ボタンの文字を更新 [cite: 384]
                    isScanning = true;
                }).catch(err => {
                    alert("カメラの起動に失敗しました。");
                });
            }
        });

        // 「スキャン結果を表示」ボタンの処理 [cite: 388]
        resultButton.addEventListener("click", () => {
            if (lastScannedCode) {
                // 課題3: 読み取った番号に対応する配列の中身を表示 
                const qrNumber = parseInt(lastScannedCode);

                // 配列のインデックスは0から始まるため、読み取った番号から1を引く
                if (qrNumber >= 1 && qrNumber <= items.length) {
                    const fruit = items[qrNumber - 1];
                    resultDisplay.textContent = fruit; // 例: "1"なら「りんご」を表示
                } else {
                    resultDisplay.textContent = `無効な番号です: ${lastScannedCode}`;
                }
            } else {
                // まだ何もスキャンされていない場合の表示 
                resultDisplay.textContent = " (まだスキャンされていません)";
            }
        });
    </script>

</body>
</html>
