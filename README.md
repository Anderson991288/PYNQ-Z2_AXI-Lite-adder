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




### Result
![result](https://github.com/Anderson991288/PYNQ-Z2_AXI-Lite-adder/assets/68816726/27f87ee8-8aa9-4c67-81eb-283e4026d01b)
