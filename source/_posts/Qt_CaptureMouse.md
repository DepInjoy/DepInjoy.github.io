---
layout: post
title: Qt捕获键盘事件
date: 2017-4-1 18:04
category: Qt
tags: [Qt，QML]
description: 本文展示一个捕获键盘输入的示例，同时通过将示例中的Rectangle获取焦点以及设置两个键盘区域的实验增加对键盘区域的理解。
---

[QML键盘处理](https://blog.csdn.net/u012419303/article/details/45477393)  [QMLBook键盘输入](https://www.devbean.net/2014/03/qt-study-road-2-qml-input-elements/)  [Keyboard Focus in Qt Quick](http://doc.qt.io/qt-5/qtquick-input-focus.html)

下面的这个示例，可以捕获鼠标点击以及键盘的ESC和数字的输入，并在keyname的区域进行显示。

```Qt
Window {
    width: 200;
    height: 200;
    visible: true
    Rectangle{
        anchors.fill: parent
        MouseArea{
            anchors.fill:   parent
            onClicked:  {keyname.text = "Mouse's clicked!"}
        }
        Text {//用于显示按键情况
            anchors.centerIn: parent
            id: keyname;
            text: qsTr("键盘记录器");
        }
        FocusScope{
        	id：firstFocus
            anchors.centerIn: parent;
            anchors.fill: parent
            focus: true//不获取焦点是不能处理键盘事件的，所以要设置为true
            Keys.enabled: true;
            Keys.onEscapePressed: Qt.quit();
            Keys.onPressed: {
                switch(event.key)
                {
                case Qt.Key_0:
                case Qt.Key_1:
                case Qt.Key_2:
                case Qt.Key_3:
                case Qt.Key_4:
                case Qt.Key_5:
                case Qt.Key_6:
                case Qt.Key_7:
                case Qt.Key_8:
                case Qt.Key_9:
                    event.accepted=true;
                    keyname.text=event.key-Qt.Key_0;
                    break;
                }
            }
        }
    }
}
```

下面我们进行几组实验来增强对键盘事件捕获的理解：

- 1 设置Rectangle捕获键盘焦点，其后续的键盘区域失效

如果我们此时在Rectangle中添加focus:true属性，则Rectangle将捕获键盘焦点，FocusScope对键盘的处理将会失效。

- 2 添加两个FocusScope区域，到底谁会捕获键盘事件以及如何选择获取键盘事件的区域。
  - secondFocus获取到键盘事件，在运行区域中可以看到相应的日志输出
  - 如果我们希望firstFocus捕获到键盘事件，需要将secondFocus的focus：false使其不捕获键盘事件。
  - 将secondFocus的层级增大（设置为z:999），firstFocus将获取到键盘事件。(原因未知？？？)

首先，在上面的示例中添加如下的代码，实现添加两个键盘获取区域，进行上面的实验就可以看到相应的效果。

```Qt
            FocusScope{
            	id：secondFocus
                anchors.fill: parent
                focus: true//不获取焦点是不能处理键盘事件的，所以要设置为true
                anchors.centerIn: parent;
                Keys.enabled: true;
                Keys.onEscapePressed:console.log("**************************")
                Keys.onPressed: {
                    switch(event.key)
                    {
                    case Qt.Key_0:
                    case Qt.Key_1:
                    case Qt.Key_2:
                    case Qt.Key_3:
                    case Qt.Key_4:
                    case Qt.Key_5:
                    case Qt.Key_6:
                    case Qt.Key_7:
                    case Qt.Key_8:
                    case Qt.Key_9:
                        event.accepted=true;
                        console.log("===========================",event.key-Qt.Key_0)
                        break;
                    }
                }
            }
```



