入門
所以你想要一個香草安裝？
那有什麼意思？
vanilla設置意味著操作系統本身保持相對不變 - 並且大部分與Hackintosh相關的kexts，補丁等都包含在EFI分區中。出於所有意圖和目的，vanilla安裝的主分區與官方Apple計算機的主分區相同。

快速術語詞彙表

您將在本指南中看到許多術語 - 我將在此概述其中的一些術語及其定義：

Clover - 這是我們將使用的引導程序。真正的mac有一個自定義固件，允許他們啟動macOS。PC硬件需要一些幫助才能使其正常工作; Clover幫助我們實現這一目標。它還處理kext注入，ACPI重命名，kext補丁和許多其他功能。
Kexts - “kext”這個詞實際上就是K ernel Ext ension 的組合; 你可以把kexts簡單地想像為macOS的驅動程序。
Config.plist - 這是告訴Clover要做什麼的文件。它是一個XML格式的屬性列表（看起來非常類似於HTML），是設置Hackintosh最重要的部分之一。
OOB - Out Of the Box的首字母縮寫詞，意味著工作支持而不需要調整。
當我處理本指南（可能）時，會添加更多內容
先決條件
本指南僅關注桌面。筆記本電腦還有其他指南（參見TMac上的RehabMan指南） - 但它們通常比本指南更具體。

我們需要一些東西才能讓我們開始：

一個8 + GB的USB閃存盤
安裝OS X / macOS.app（最好直接從應用程序商店下載）
__ Clover的安裝包 __（由Dids提供）
Clover Configurator（勇敢者可以使用任何文本編輯器進行編輯 - 但CC通常更快）
確保您獲得全球版
__ VirtualSMC.kext - 這取代了FakeSMC.kext作為我們的SMC模擬器，VirtualSMC或FakeSMC對於啟動我們的Hackintosh至關重要。沒有其中一個，我們永遠不會開機。
我們的主板/等的任何其他kexts
我們將在下一節中討論這個問題！
一些耐心，堅持和谷歌
