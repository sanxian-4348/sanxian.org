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
> 電腦硬體裝修乙級檢定不只是單純的組裝電腦，更是對「穩定性」與「除錯能力」的嚴格考驗。相較於一般家用機，乙級檢定更側重於環境規劃、故障排除，以及對伺服器等級硬體的理解。

基本上需要了解電腦零件故障排除、兩台電腦重灌、網路線跳線、Client/Server 端連線設置、電路焊接及程式設計。

---

### Server & Client 配置
在乙級術科中，兩部電腦的連線設定是核心重點：
1. **Server 端**：負責建立網域主控站（AD DS）、DNS 伺服器以及 DHCP 配發。
2. **Client 端**：需正確設定 IP 網段並成功「加入網域」。
3. **實體連線**：使用自製跳線進行通訊測試。

---

### 介面卡程式控制

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