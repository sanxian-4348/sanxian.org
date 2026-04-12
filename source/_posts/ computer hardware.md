---
title: 電腦硬體裝修
date: 2025-11-28 13:00:00
tags:
  - 電腦硬體裝修
  - 乙級
  - server
  - 介面卡
cover: /images/computer hardware.webp
urlname: Gomoku
---

## 電腦硬體裝修

### 前言
電腦硬體裝修乙級檢定不只是單純的組裝電腦，更是對「穩定性」與「除錯能力」的嚴格考驗。相較於一般家用機，乙級檢定更側重於環境規劃、故障排除，以及對伺服器等級硬體的理解。
考這張證照非常吃考場設備，我拿我朋友來舉例子第一站為介面卡焊接製作完成焊接後要使用Visual basic IDE 進行藍芽連線，Visual basic這個IDE使用上有個缺點會讓你卡卡的呼叫函式或圖形你要慢慢的操作不然就是呼叫的函式有打上去但電腦沒接收到有這個函式等於說~~白打~~，圖形則是他會像老阿公一樣緩緩的被你調整各種姿勢，再來說道我朋友看到一個的悲劇當你程式都打完了準備進行藍牙連接時你的電腦給你死機藍屏黑屏這就是考場設備運氣了，他是每次重打就死機給他看，非常得令人難過，再來就是藍牙上的連接第一站有些學校的場地可能是提供**windows 10**或者**windows 11**版本上我自己考成功是**windows 11**介面操作上會比較的友好，我是這樣覺得，學校練習的版本是**windows 10**我是覺得已經**拉完了**，再來如果你焊接好了程式也打完了，就要去啟動**arduno 1.8.18**這個版本也是各個考場都不相同，要進行**HC-05**藍芽模組的名稱更改、密碼更改，下方是更改指令⬇️

---

### Server & Client 配置
在乙級術科中，兩部電腦的連線設定是核心重點：
1. **Server 端**：負責建立網域主控站（AD DS）、DNS 伺服器以及 DHCP 配發。
2. **Client 端**：需正確設定 IP 網段並成功「加入網域」。
3. **實體連線**：使用自製跳線進行通訊測試。
---

### 題目介紹

基本上需要了解電腦零件故障排除、兩台電腦重灌、網路線跳線、Client/Server 端連線設置、電路焊接及程式設計。

---

### 介面卡程式控制
需使用Ardumo IDE
```cpp
AT //進入AT模式
AT+NMAE=BTxx//更改名稱
AT+PSWD=1234//更改密碼
AT+UART=9600,0,0//更改傳輸速率bps

AT+NAME//查詢名稱
AT+PSWD//查詢密碼
AT+UART//查詢傳輸速率bps
```

---

在arduno IDE 找工具選單➡️序列阜監控視窗➡️選擇藍牙的com port，在進行上面的更改指令。
#### 介面卡程式
```vb
Dim a, b(99), c As Integer

Private Sub Command1_Click(Index As Integer)
    a = Index
    c = 0
End Sub

Private Sub display(no)
    For i = 0 To 7
        If no Mod 2 = 1 And a = 1 Then G(i).FillColor = RGB(0, 255, 0)
        If no Mod 2 = 1 And a = 2 Then R(i).FillColor = RGB(255, 0, 0)
        no = no \ 2
    Next i 
End Sub

Private Sub Command2_Click()
    If MSComm1.PortOpen Then
        MSComm1.Output = "R0"
        MSComm1.Output = "G0"
        MSComm1.PortOpen = False
        Command2.Caption = "Connect Bluetooth"
    Else
        MSComm1.PortOpen = True
        Command2.Caption = "Disconnect Bluetooth"
        MSComm1.Output = "R0"
        MSComm1.Output = "G0"
    End If
End Sub

Private Sub Timer1_Timer()
    b(0) = &H18
    b(1) = &H24
    b(2) = &H42
    b(3) = &H81
    Label1.Caption = "Current Time:" & Time$
    For i = 0 To 7
        G(i).FillColor = vbWhite
        R(i).FillColor = vbWhite
    Next i
    If MSComm1.PortOpen Then
        For i = 0 To 7
            G(i).FillColor = RGB(0, 128, 0)
            R(i).FillColor = RGB(128, 0, 0)
        Next i 
        If a = 1 Then MSComm1.Output = "G" & b(c): display(b(c))
        If a = 2 And c <= 8 Then MSComm1.Output = "R" & 2 ^ c: display(2 ^ c)
    End If
    If a = 3 Then MSComm1.Output = "R0": MSComm1.Output = "G0": End 
    If c > 15 Then c = 15 Else c = c + 1    
End Sub
```