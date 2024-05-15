# luta-esp32c3-eink
ESPhome code for Luta-esp32c3 module with e-ink display

## Hardware 硬體架構

此為根據 [LiuChengloong](https://www.cnblogs.com/manastudent) 設計的硬體搭配使用的ESPhome code

- 正面硬體外觀:

<img src="https://i.imgur.com/iA85DBX.jpg" width="50%" />

- 背面硬體外觀:

<img src="https://i.imgur.com/2pRsxEy.jpg" width="50%" />

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

   
## ESPHome yaml 說明

### 實體按鈕

GPIO5, GPIO8分別代表兩個按鈕可用來前後換頁，或不寫功能寫上name，在HA就會變成一個無線按鈕

可當自動化的觸發，進而控制HA內的其他裝置

```YAML
binary_sensor:
  - platform: gpio
    id: button_io5
    name: "${device_name} IO5"
    pin:
      number: GPIO05
      mode: INPUT_PULLUP
      inverted: True
    on_press: #control change page
      then:
        - display.page.show_next: my_display
        - component.update: my_display

  - platform: gpio
    id: button_io8
    name: "${device_name} IO8"
    pin:
      number: GPIO08
      mode: INPUT_PULLUP
      inverted: True

```

### 在HA控制換頁

有2個按鈕，按下去分別會去顯示p1(Time Page)與p2(Message Page)，如果有要再新增更多頁可以再仿照程式碼再新增

```YAML
button:
  - platform: template
    name: "Show Time Page"
    icon: 'mdi:clock'
    on_press:
      then:
        - display.page.show: p1
        - component.update: my_display
    
  - platform: template
    name: "Show Message Page"
    icon: 'mdi:update'
    on_press:
      then:
        - display.page.show: p2
        - component.update: my_display
```

### LED 控制

有2顆LED，可以用來當狀態的LED燈或是手動控制開關都可以

```YAML
switch:
  - platform: gpio
    name: "${device_name} D5 LED"
    pin: GPIO13
    id: relay
```

```YAML
status_led:
  pin:
    number: GPIO12
```

### 根據Wi-Fi強度顯示圖示

說明: 
- 大於等於-60顯示三格
- -60~-70顯示兩格
- -70~-75顯示一格
- -75~-85顯示零格
- 小於-85顯示中斷

可自由變更強度範圍要顯示的格數

```YAML
          //wifi signal
          if (id(wifisignal).state >= -60) {
              //Excellent
              it.print(0, 0, id(wifi_font), "\U000F08BE");
          } else if (id(wifisignal).state  >= -70) {
              //Good
              it.print(0, 0, id(wifi_font), "\U000F08BD");
          } else if (id(wifisignal).state  >= -75) {
              //Fair
              it.print(0, 0, id(wifi_font),"\U000F08BC");
          } else if (id(wifisignal).state  >= -85) {
              //Weak
              it.print(0, 0, id(wifi_font),"\U000F08BF");
          } else {
              //Unlikely working signal
              it.print(0, 0, id(wifi_font),"\U000F0783");
          }
```

### 修改自訂訊息內容

作法:

1. 在字型宣告處的`msg_font`將要顯示的中文字**先全部寫出來**這樣才能正常顯示唷!!
```YAML
font:
  - file: "fonts/NotoSansTC-Medium.ttf"
    id: msg_font
    size: 40
    glyphs: 衣服已經洗拿去烘好囉!趕快收起來ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz,."%-~_:°
```

2. 在`display`的p2，置換想要顯示的文字，依目前設定的大小，一行就是顯示`7個中文字`，超過將無法顯示唷!
```YAML
display:
  ...
      - id: p2
        lambda: |- 
          it.printf(150,15, id(msg_font), TextAlign::TOP_CENTER, "衣服已經洗好囉!");
          it.printf(150,70, id(msg_font), TextAlign::TOP_CENTER, "趕快拿去烘~");
```

### 增加更多頁面

作法: 仿照按鈕及顯示的程式碼，可再新增多組，例如下面是第三頁，按下去顯示衣服烘好了

有不在`msg_font`內的中文字要記得新增，這樣才能正常顯示唷!

```YAML

button: #複製在button程式碼的最下面，不可重複寫button唷!
  ...
  - platform: template
    name: "Show Dryer Done Page"
    icon: 'mdi:update'
    on_press:
      then:
        - display.page.show: p3
        - component.update: my_display


display: #複製在display程式碼的最下面，不可重複寫display唷!
  ...
      - id: p3
        lambda: |- 
          it.printf(150,15, id(msg_font), TextAlign::TOP_CENTER, "衣服已經烘好囉!");
          it.printf(150,70, id(msg_font), TextAlign::TOP_CENTER, "趕快收起來!!");
```
