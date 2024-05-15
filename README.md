# luta-esp32c3-eink
ESPhome code for Luta-esp32c3 module with e-ink display

## Hardware 硬體架構

此為根據 [LiuChengloong](https://www.cnblogs.com/manastudent) 設計的硬體搭配使用的ESPhome code

- 正面硬體外觀:



- 背面硬體外觀:



| I/O定義 | Pin name  |	ESP32C3 |
|:----:|:----:|:----:|
| Button |	IO8 |	GPIO8 |
|	| IO5 | GPIO5 |
| LED |	D5 |	GPIO13 |
|	| D4 | GPIO12 |
| Eink |	CS |	GPIO7|
| |	DC |	GPIO10 |
| |	CLK |	GPIO4 |
| |	MOSI |	GPIO6 |
| |	RESET |	GPIO3 |
| |	BUSY |	GPIO2 |


## Installation 安裝方式

1. 將`luta-esp32c3-eink.yaml`的內容修改成自己想要的內容 **解說在下方**
2. 按右上角`INSTALL`選`Manual Download`編譯完成選`Modern format`下載編譯好的檔案
3. 按住模組上的BOOT後再插上電腦
4. 用Chrome或 Edge瀏覽器開啟 <https://web.esphome.io/>
5. 按Connect，選擇標有JTAG的COM port
6. 按INSTALL選擇剛做好的bin檔燒錄
7. 畫面顯示完成後按下背面的RST或是重新上電即完成!

   
