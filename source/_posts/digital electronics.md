---
title: 數位電子
date: 2026-01-28 13:00:00
tags:
  - 乙級
  - 數位電子
  - digital electronics
cover: /images/b.jpg
urlname: digital electronics
---

## 數位電子

### 前言
數位電子乙級檢定涉及 CPLD/FPGA 燒錄與子母板電路焊接。相較於硬體裝修，本考科更強調 Verilog 硬體描述語言的運用、除頻電路設計以及矩陣掃描邏輯。

---

### 題目介紹
1. **第一題**：四位數七段顯示器動態掃描控制。
2. **第二題**：4x4 矩陣式鍵盤掃描與解碼顯示。

---

### 電路設計

### 焊接方式

### 燒錄程式碼

#### 第一題
```verilog
module no1(a,b,c,d,e,f,g,dp,cc,ck);       
    input ck;           
    output [1:4] cc;  
    output a,b,c,d,e,f,g,dp;       

    reg [13:0] div_f;
    reg [1:4] cc;
    reg a,b,c,d,e,f,g,dp;

    always @(posedge ck) 
        div_f <= div_f + 1;

    always @(posedge div_f[13])
        case(cc)
            4'b0001: begin
                cc <= 4'b1000;
                {a,b,c,d,e,f,g,dp} <= 8'b11111100;
            end
            4'b1000: begin
                cc <= 4'b0100;
                {a,b,c,d,e,f,g,dp} <= 8'b11111111;
            end
            4'b0100: begin
                cc <= 4'b0010;
                {a,b,c,d,e,f,g,dp} <= 8'b01100000;
            end
            default: begin
                cc <= 4'b0001;
                {a,b,c,d,e,f,g,dp} <= 8'b01100110;
            end
        endcase
endmodule
```

#### 第二題
```verilog
module no2(ck,a,b,c,d,e,f,g,col,row);
input ck;
input [2:0] col; //行
output a,b,c,d,e,f,g;
output[3:0] row; //列
reg a,b,c,d,e,f,g;
reg [3:0] row;
reg [13:0] div_f;
always @(posedge ck) //除頻電路
	div_f<=div_f+1;
always @(posedge div_f[13])
	case(row)
		4'b1110: //掃描第一列
			begin
				row<=4'b1101;
				if(col==3'b110)
					{a,b,c,d,e,f,g}<=7'b1001111; //1
				if(col==3'b101)
					{a,b,c,d,e,f,g}<=7'b0010010; //2
				if(col==3'b011)
					{a,b,c,d,e,f,g}<=7'b0000110; //3
			end
		4'b1101: //掃描第二行
			begin
				row<=4'b1011;
				if(col==3'b110)
					{a,b,c,d,e,f,g}<=7'b1001100; // 4
				if(col==3'b101)
					{a,b,c,d,e,f,g}<=7'b0100100; // 5
				if(col==3'b011)
					{a,b,c,d,e,f,g}<=7'b0100000; // 6
			end
		4'b1011: //掃描第三行
			begin
				row<=4'b0111;
				if(col==3'b110)
					{a,b,c,d,e,f,g}<=7'b0001111; // 7
				if(col==3'b101)
					{a,b,c,d,e,f,g}<=7'b0000000; // 8
				if(col==3'b011)
					{a,b,c,d,e,f,g}<=7'b0001100; // 9
			end
		default:
			begin
				row<=4'b1110;
				if(col==3'b110)
					{a,b,c,d,e,f,g}<=7'b1110010; //* 修改此處
				if(col==3'b101)
					{a,b,c,d,e,f,g}<=7'b0000001; //0
				if(col==3'b011)
					{a,b,c,d,e,f,g}<=7'b1100110; //# 修改此處
			end
	endcase
endmodule
 
```

### 感想
