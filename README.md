# PYNQ-Z2_AXI-Lite-adder

## 1.開一個新專案，板子選擇PYNQ-Z2

![image](https://github.com/Anderson991288/PYNQ-Z2-note/assets/68816726/95824b87-74f8-48ab-b70e-122d393c5d89)


## 2. 寫一個基本的加法器

* 接收兩個輸入 a 和 b，並產生一個輸出 c，c 是 a 和 b 的和
* 加法器可以處理的數字的最大位元數由 WIDTH 參數決定

* **axi_adder.v**
```
module axi_adder #(parameter WIDTH = 4)(
    input [WIDTH-1:0] a,
    input [WIDTH-1:0] b,
    output [WIDTH:0] c
    );
assign c=a+b;   
endmodule
```


## 3. 建立 AXI4 Peripheral

### AXI4 Peripheral:

![未命名](https://github.com/Anderson991288/PYNQ-Z2-note/assets/68816726/5b9c4293-63fa-4f0d-b4f2-2954a0897b52)

![未命名1](https://github.com/Anderson991288/PYNQ-Z2-note/assets/68816726/411e4f4f-0956-40f6-92b2-f89d48fda3bc)

![未命名2](https://github.com/Anderson991288/PYNQ-Z2-note/assets/68816726/200525b2-6614-4093-88dc-414c95c1dc9a)

![未命名3](https://github.com/Anderson991288/PYNQ-Z2-note/assets/68816726/977c7dee-d680-4f30-aa1d-9611f8db1591)

## 4.修改內建AXI程式，

* **axi_adder_v1_0_S00_AXI :**

* 透過 AXI Bus接收輸入（a 和 b），並將計算結果（c）傳送回去
    
    將 slv_reg0 暫存器的內容分配給 a_wire。 a_wire 將持有來自 AXI Bus接口的數據，這些數據是加法器的第一個輸入 a
    ```
    assign a_wire = slv_reg0;
    ```
    
    將 slv_reg1 暫存器的內容分配給 b_wire。b_wire 用於存加法器的第二個輸入 b
    ```
    assign b_wire = slv_reg1;
    ```
    
    將加法器的輸出 c 分配給 c_wire 。這裡 c_wire 的大小可能比 c 大，因為它使用的是 AXI 數據寬度 C_S_AXI_DATA_WIDTH，所以只有低位元（WIDTH:0）被賦值為 c 的值
    ```
    assign c_wire[WIDTH:0] = c;
    ```
    
    將 a_wire 的低 WIDTH 位元分配給加法器的輸入 a。這確保了只有相應的位元數被用於加法運算
    ```
    assign a = a_wire[WIDTH-1:0];
    ```
    
    同上，將 b_wire 的低 WIDTH 位元分配給加法器的輸入 b
    ```
    assign b = b_wire[WIDTH-1:0];
    ```
    
    
    將 c_wire 中高於 WIDTH 位元的部分清零。由於 c 只占 WIDTH+1 位元，所以更高的位元應該被設置為 0
    ```
    assign c_wire[C_S_AXI_DATA_WIDTH-1:WIDTH+1] = 0;
    ```





**axi_adder_v1_0 ：**

* 這是頂層模塊，定義 bus interface 的接口和參數
  
```
// Add user logic here
axi_adder #(.WIDTH(WIDTH)
     ) axi_adder_inst (
            .a(a_i),
            .b(b_i),
            .c(c_i)
     );
```
 * 它包含了 WIDTH 參數，用於設定加法器可以處理的數字的最大位元數
```
parameter WIDTH = 4,
```
* 此模塊內嵌了一個子模塊 axi_adder_v1_0_S00_AXI 和 axi_adder，分別負責處理 AXI 加法運算
    
    



## 4. Run Synthesis 和 Run Implementation，完成後再 Re-package IP

![image](https://github.com/Anderson991288/PYNQ-Z2_AXI-Lite-adder/assets/68816726/b03d7768-9a65-4ab9-851f-301cb152ea33)


## 5. Create Block design ，將剛剛包好的 IP 加入，按照以下連接

![image](https://github.com/Anderson991288/PYNQ-Z2_AXI-Lite-adder/assets/68816726/4fbf51ef-9f8f-4210-b89e-9f889a254ccf)

* **Processing System 有些接口預設是沒有的，可以點兩下到選單裡面找**
![image](https://github.com/Anderson991288/PYNQ-Z2_AXI-Lite-adder/assets/68816726/7a4751a4-8f11-47ad-93e2-0fe35fb6cbf2)


## 6. "Create HDL Wrapper" ，包裝 Block Design 檔案。

![11](https://github.com/Anderson991288/PYNQ-Z2-note/assets/68816726/18d45c14-0d65-46fa-8c56-25111e22804b)



## 7. Run Synthesis"， "Run Implementation"，Generate Bitstream 後再輸出 Block design

![12](https://github.com/Anderson991288/PYNQ-Z2-note/assets/68816726/c473aa9d-6000-4576-9e42-7cd073cb0c23)

## 8.  輸出.tcl file
* 在Tcl console 中輸入 "write_bd_tcl "你的專案路徑/design_1.tcl" "



## 9. 將 design_1.tcl ，design_1_wrapper.bit，design_1.hwh 複製到板子上

* **檔名要改成一樣的，原來預設檔名是design_1.tcl ，design_1_wrapper.bit，design_1.hwh**

![image](https://github.com/Anderson991288/PYNQ-Z2_AXI-Lite-adder/assets/68816726/051b9465-6a13-45eb-b737-5f17386ee5a8)


## 10. 在Jupyter Notenook 中編寫程式，導入自訂的Overlay

```
from pynq import Overlay
ol = Overlay('adder.bit')

ol.axi_adder_0.write(0x0,5)

ol.axi_adder_0.write(0x4,10)

ol.axi_adder_0.read(0x8)
```
### Result
![result](https://github.com/Anderson991288/PYNQ-Z2_AXI-Lite-adder/assets/68816726/27f87ee8-8aa9-4c67-81eb-283e4026d01b)
