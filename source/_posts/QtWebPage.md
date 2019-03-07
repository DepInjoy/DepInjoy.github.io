---
layout: post
title: Qt直接加载web页面
date: 2019-3-7 11:26
category: Qt
tags: [Qt]
description: 
---



### 页面加载网页

加载百度页面示例：

```Qt
import QtQuick.Window 2.2
import QtQuick.Controls 1.4
import QtQuick 2.0
import QtWebEngine 1.0

Window {
        width: Screen.width
        height: Screen.height
        visible:true

        WebEngineView {
            anchors.fill: parent
            url: "https://www.baidu.com/"
        }
}
```

