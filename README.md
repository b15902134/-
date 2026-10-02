# 耶穌基督：神聖救世主的永恆聖光——以 SwiftUI Shape、Canvas 與時間驅動動畫實作的紀念插畫

> 以 SwiftUI 原生繪圖技術完成的動態人物插畫，透過溫暖的配色、神聖光影與平穩動畫，表達慈愛、包容、盼望與永恆平安。

## 專案資訊

| 項目 | 說明 |
|---|---|
| 開發平台 | Xcode |
| 程式語言 | Swift |
| UI 框架 | SwiftUI |
| 主要技術 | Shape、ZStack、Canvas、Path、CGAffineTransform、TimelineView |
| 外部套件 | 無 |
| 外部圖片 | 無 |

## 一、專案發想

本專案以「耶穌張開雙手迎接人們」為設計主題，透過溫暖、柔和的視覺風格，呈現慈愛、包容與神聖的形象，並紀念至高無上的外公回歸屬靈，回到主耶穌與天父的懷抱中，享受永恆的福樂與平安。就如聖經所應許的，在神那裡不再有悲傷、痛苦與眼淚。

人物設計保留耶穌常見的視覺特徵，包括長髮、鬍鬚、米色長袍、紅色披巾及草鞋。背景採用天空藍與金黃色漸層，搭配光環、放射光束和光點，營造平靜且莊重的氛圍。

整體作品未使用外部圖片素材，而是將人物拆分為頭髮、臉部、五官、鬍鬚、手掌、衣袖、長袍、披巾、腳部及草鞋，再透過 SwiftUI 的基本形狀與自訂路徑逐層組合。

## 二、系統架構

| 元件 | 責任 |
|---|---|
| `ContentView` | 負責整體版面配置與裝置尺寸適配 |
| `SacredBackground` | 負責漸層背景、放射光束、光點及中央聖光 |
| `AssignmentShapeLayer` | 集中展示 SwiftUI 內建形狀與 modifier |
| `JesusIllustration` | 使用 Canvas 與 Path 繪製人物各部位 |
| `AnimatedJesusIllustration` | 負責人物漂浮與呼吸動畫 |

透過元件化設計，將背景、人物及動畫分開管理，使程式結構清楚，並方便後續維護。

## 三、使用技術與功能

### SwiftUI 內建形狀

- `Rectangle`：底部金色光影。
- `Circle`：人物頭部後方的圓形聖光。
- `Ellipse`：左右兩側的柔和雲層。
- `Capsule`：斜向細長光線。
- `RoundedRectangle`：背景柔光裝飾。
- `UnevenRoundedRectangle`：底部不對稱光影。

各種形狀搭配尺寸、位置、旋轉角度與透明度，組合成完整背景。

### ZStack 圖層管理

```swift
ZStack {
    SacredBackground()
    AnimatedJesusIllustration()
}
```

背景位於底層，人物位於上層。背景內部依序放置漸層、幾何裝飾、放射光與中央光環。

### Modifier

| Modifier | 用途 |
|---|---|
| `.frame()` | 設定形狀尺寸 |
| `.offset()` | 調整顯示位置 |
| `.fill()` | 設定填滿顏色 |
| `.opacity()` | 控制透明度 |
| `.rotationEffect()` | 製作旋轉效果 |
| `.scaleEffect()` | 製作縮放動畫 |
| `.ignoresSafeArea()` | 讓背景延伸至整個螢幕 |

### Assets RGB 色彩管理

專案在 `Assets.xcassets` 中建立兩組自訂色票：

- `SacredSky`
- `SacredGold`

```swift
Color("SacredSky")
Color("SacredGold")
```

將主要顏色集中管理，可避免 RGB 數值分散在不同程式區塊，也便於後續統一調整配色。

### Canvas 與 Path

人物本體完全使用 `Canvas` 與 `Path` 繪製，不依賴外部圖片。

```swift
path.move(to:)
path.addLine(to:)
path.addCurve(to:control1:control2:)
path.addQuadCurve(to:control:)
path.closeSubpath()
```

- `move(to:)`：指定路徑起點。
- `addLine(to:)`：建立直線。
- `addCurve`：建立三次貝茲曲線。
- `addQuadCurve`：建立二次貝茲曲線。
- `closeSubpath()`：封閉路徑，使形狀能夠填色。

人物各部位以不同 Path 分開管理，包括長袍、衣袖、頭髮、鬍鬚、手掌、紅色披巾、腳部與草鞋。

### 左右鏡像處理

左右對稱的部位使用 `CGAffineTransform` 水平翻轉，避免重複維護兩組座標：

```swift
let transform = CGAffineTransform(translationX: 430, y: 0)
    .scaledBy(x: -1, y: 1)
```

此方式能確保左右部位的尺寸與比例一致，同時降低重複程式碼。

### 響應式縮放

人物以 `430 × 760` 作為原始設計尺寸，再透過 `GeometryReader` 計算縮放比例：

```swift
let scale = min(
    geometry.size.width / 430,
    geometry.size.height / 780
)
```

取寬度與高度比例中的較小值，可以讓人物保持等比例並完整顯示，避免在不同裝置尺寸上遭到裁切。

### 時間驅動動畫

專案使用 `TimelineView` 取得持續變化的時間，再透過正弦函數建立平滑循環：

```swift
let wave = sin(seconds * 2 * Double.pi / 4.0)

JesusIllustration()
    .scaleEffect(CGFloat(1 + wave * 0.01))
    .offset(y: CGFloat(wave * 6))
```

動畫功能包含：

- 人物上下漂浮。
- 人物輕微呼吸縮放。
- 中央光環縮放與明暗變化。
- 背景光點透明度變化。
- 放射光束持續旋轉。

人物動畫幅度刻意保持較小，以維持畫面莊重感。專案亦讀取系統的「減少動態效果」設定；啟用後動畫會自動停止，符合基本無障礙設計原則。

## 四、主要實作指令

### 全螢幕漸層背景

```swift
LinearGradient(
    colors: [
        Color("SacredSky"),
        Color(red: 0.82, green: 0.90, blue: 0.96),
        Color(red: 0.98, green: 0.92, blue: 0.72)
    ],
    startPoint: .top,
    endPoint: .bottom
)
.ignoresSafeArea()
```

### 基本形狀設定

```swift
Circle()
    .fill(Color("SacredGold").opacity(0.16))
    .frame(width: 235, height: 235)
    .offset(y: -220)
```

### 自訂路徑

```swift
var path = Path()
path.move(to: CGPoint(x: 166, y: 215))
path.addLine(to: CGPoint(x: 135, y: 365))
path.closeSubpath()
```

### 背景光束旋轉

```swift
.rotationEffect(
    .degrees(rotationDegrees),
    anchor: UnitPoint(x: 0.5, y: 0.21)
)
```

## 五、專案操作方式

1. 使用 Xcode 開啟 `68.xcodeproj`。
2. 選擇可用的 iPhone Simulator。
3. 按下 `Command + R` 建置並執行專案。
4. 若使用 SwiftUI Preview，可開啟 `ContentView.swift` 後按下 Resume。
5. 若動畫沒有播放，請確認系統的「減少動態效果」未開啟。

## 六、開發過程與問題處理

開發過程中較具挑戰性的部分是人物各部位的比例、接合位置與圖層順序。

手掌必須先繪製，再由衣袖覆蓋手腕接合處，否則手掌和袖口之間會出現空隙。衣袖外框也不能直接封閉所有邊線，否則袖子與長袍交界處會出現不自然的黑線。

紅色披巾由胸前主布料與垂落部分組成，兩個路徑必須使用相同顏色，並在肩膀附近正確銜接，才能呈現一整條布料的效果。

腳部採用多層結構繪製，依序包含鞋底、腳部、草鞋外殼與編織紋路。透過正確的繪製順序，草鞋可以自然覆蓋腳掌，同時保留鞋底輪廓。

動畫採用時間驅動方式，而非一次性狀態切換，確保動畫能在 Preview 與模擬器中持續更新。

## 七、開發心得

本專案讓我更深入理解 SwiftUI 的圖層結構、座標系統與形狀組合方式。原本對 SwiftUI 的理解主要集中在文字、按鈕與版面配置，但實際製作後發現，SwiftUI 也能透過 Shape、Canvas 和 Path 完成較複雜的插畫。

製作過程中，我體會到路徑座標與圖層順序對畫面的影響很大。即使只調整少量控制點，也可能讓頭髮、手掌、衣袖或腳部產生明顯變化。因此每次修改後都必須重新檢查整體比例，而不是只觀察單一部位。

另外，將重複使用的顏色、路徑和轉換邏輯集中管理，可以降低程式重複度並提升維護效率。透過本次實作，我熟悉了 `ZStack`、modifier、Assets 色票、`Canvas`、`Path`、`CGAffineTransform`、`GeometryReader` 與時間驅動動畫，也提升了將視覺設計轉換成程式結構的能力。

## 八、程式碼

主要程式位於專案資料夾中的 `68/ContentView.swift`。

在 GitHub 儲存庫首頁依序開啟 `68` 資料夾，再選擇 `ContentView.swift`，即可查看完整 SwiftUI 程式碼。若上傳時已將內層 `68` 資料夾的內容直接放到儲存庫根目錄，則直接開啟根目錄中的 `ContentView.swift`。

## 授權與用途

本專案為 SwiftUI 課程學習與紀念用途。
