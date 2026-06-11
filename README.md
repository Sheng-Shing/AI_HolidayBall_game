# AI Holiday Ball Game

勝行科技智慧團康 AI 的網頁接球遊戲。玩家控制擋板接住落下的彩色球，可使用鍵盤左右鍵操作，也可透過「Holiday Ball」BLE 控制器操作。

## 功能

- 彩色接球遊戲，分數、關卡、生命即時顯示
- 支援鍵盤左右鍵控制擋板
- 支援 Web Bluetooth 連接 Holiday Ball 控制器
- 左側壓力資料控制左移
- 右側壓力資料控制右移
- 隨關卡提升增加球速、掉落頻率與同時球數

## 使用方式

直接用支援 Web Bluetooth 的瀏覽器開啟 `index.html`。

建議使用：

- Chrome
- Edge

注意事項：

- Web Bluetooth 通常需要 HTTPS 或 localhost 環境。
- iOS 裝置的 Web Bluetooth 支援有限，請依實際瀏覽器能力測試。
- 連接 Holiday Ball 時，畫面提示「請勿握壓軟球」期間請勿握壓左右軟球，方便校正初始壓力值。

## 操作

鍵盤操作：

- 左移：`ArrowLeft`
- 右移：`ArrowRight`

Holiday Ball 操作：

1. 點擊「連接 Holiday Ball」
2. 連線成功後，狀態顯示為「已連接」
3. 握壓左側軟球左移
4. 握壓右側軟球右移

## BLE 設定

目前使用的 BLE UUID：

```text
Service UUID:        0000fff0-0000-1000-8000-00805f9b34fb
Characteristic UUID: 0000fff2-0000-1000-8000-00805f9b34fb
```

初始化流程：

1. 連接 BLE 裝置
2. 啟用 characteristic notifications
3. 等待 3 秒，確保 notifications 已準備好
4. 傳送 `BFWLX`
5. 收到 `255` 後傳送 `ST`

## 感測資料格式

一般資料封包中：

- `data[2..4]`：左側壓力值，24-bit big-endian
- `data[5..7]`：右側壓力值，24-bit big-endian

壓力值解析：

```js
left = data[2] * 256 * 256 + data[3] * 256 + data[4];
right = data[5] * 256 * 256 + data[6] * 256 + data[7];
```

壓力值最大值為 `4194303`。連線校正時會記錄左右兩側的初始壓力值，之後的感測值會先扣除校正值，再正規化為 `0` 到 `1` 的控制值。

## 專案檔案

```text
index.html   遊戲畫面與控制元件
script.js    遊戲邏輯、BLE 連線與感測資料解析
style.css    版面與視覺樣式
todo.md      開發備忘
```

## 開發

此專案不需要建置流程。若要用 localhost 測試，可在專案目錄啟動任一靜態伺服器，例如：

```sh
python3 -m http.server 8000
```

然後開啟：

```text
http://localhost:8000
```
