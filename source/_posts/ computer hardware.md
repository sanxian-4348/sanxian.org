---
title: 電腦硬體裝修乙級檢定筆記
date: 2025-11-28 13:00:00
tags:
  - 電腦硬體裝修
  - 乙級
  - Server
  - 介面卡
cover: /images/computer hardware.webp
urlname: Gomoku
---

# 電腦硬體裝修乙級檢定筆記

## 前言

> 電腦硬體裝修乙級檢定不僅是單純的電腦組裝，更是對系統**穩定性**與**除錯能力**的嚴格考驗。相較於一般家用機，乙級檢定更側重於**環境規劃**、**故障排除**，以及對**伺服器等級硬體**的理解。

> 考取這張證照非常依賴考場設備的狀況。以我朋友的經驗為例，第一站為**介面卡焊接**。完成焊接後，需要使用 **Visual Basic IDE** 進行藍牙連線。Visual Basic 這個 IDE 在使用上存在一些缺點，例如在呼叫函式或操作圖形介面時，可能會遇到卡頓或指令未被正確接收的問題，導致重複操作。更令人沮喪的是，有時程式碼都已完成，準備進行藍牙連接時，電腦卻突然死機（藍屏或黑屏），這完全取決於考場設備的運氣。我朋友就曾多次遇到重打程式後又死機的情況，令人非常難過。

> 此外，藍牙連線的環境也可能因考場而異。有些考場可能提供 **Windows 10**，有些則是 **Windows 11**。我個人在 **Windows 11** 環境下操作感覺較為順暢和友好，而學校練習時使用的 **Windows 10** 版本則讓我感到有些不便，其實是拉完了。當焊接與程式設計都完成後，還需要啟動 **Arduino 1.8.18** 版本（此版本也可能因考場而異），對 **HC-05 藍牙模組**進行名稱與密碼的更改。更改指令如下所示：

## Server & Client 配置

在乙級術科中，兩部電腦的連線設定是核心重點，主要分為以下三個部分：

1.  **Server 端**：負責建立**網域主控站 (AD DS)**、**DNS 伺服器**以及 **DHCP 配發**。
2.  **Client 端**：需正確設定 **IP 網段**並成功「**加入網域**」。
3.  **實體連線**：使用**自製跳線**進行通訊測試。

## 題目介紹

乙級檢定基本上需要了解以下技能：

*   電腦零件故障排除
*   兩台電腦重灌
*   網路線跳線
*   Client/Server 端連線設置
*   電路焊接
*   程式設計

## 介面卡程式控制

需使用 **Arduino IDE** 進行 HC-05 藍牙模組的 AT Command 設定。以下為常用指令：

```md
AT             // 進入 AT 模式
AT+NAME=BTxx   // 更改藍牙模組名稱，將 BTxx 替換為欲設定的名稱
AT+PSWD=1234   // 更改藍牙模組密碼，將 1234 替換為欲設定的密碼
AT+UART=9600,0,0 // 更改傳輸速率 bps (鮑率)，此處設定為 9600

AT+NAME        // 查詢藍牙模組名稱
AT+PSWD        // 查詢藍牙模組密碼
AT+UART        // 查詢傳輸速率 bps (鮑率)
```
---
#### 在 Arduino IDE 中，請依序點選「工具」選單 → 「序列埠監控視窗」，然後選擇藍牙模組所對應的 COM Port，即可輸入上述指令進行設定。

---

介面卡控制程式碼 (Visual Basic)

以下為介面卡控制的 Visual Basic 程式碼範例：
```vb
Dim a As Integer, b(99) As Integer, c As Integer

Private Sub Command1_Click(Index As Integer)
    a = Index
    c = 0
End Sub

Private Sub display(no As Integer)
    Dim i As Integer
    For i = 0 To 7
        If (no Mod 2 = 1) And (a = 1) Then
            G(i).FillColor = RGB(0, 255, 0)
        End If
        If (no Mod 2 = 1) And (a = 2) Then
            R(i).FillColor = RGB(255, 0, 0)
        End If
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
    
    Dim i As Integer
    For i = 0 To 7
        G(i).FillColor = vbWhite
        R(i).FillColor = vbWhite
    Next i
    
    If MSComm1.PortOpen Then
        For i = 0 To 7
            G(i).FillColor = RGB(0, 128, 0)
            R(i).FillColor = RGB(128, 0, 0)
        Next i
        
        If a = 1 Then
            MSComm1.Output = "G" & b(c)
            display(b(c))
        End If
        If a = 2 And c <= 8 Then
            MSComm1.Output = "R" & 2 ^ c
            display(2 ^ c)
        End If
    End If
    
    If a = 3 Then
        MSComm1.Output = "R0"
        MSComm1.Output = "G0"
        End
    End If
    
    If c > 15 Then
        c = 15
    Else
        c = c + 1
    End If
End Sub
```

