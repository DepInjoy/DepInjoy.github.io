---
layout: post
title: Qt属性注册、信号与槽函数
date: 2018-1-8 18:04
category: Qt
tags: [Qt]
description: 
---

​	

​	Qt主要由C++和其独有的QML组成，其中C++负责完成后端的逻辑处理，而QML则可以完成前端界面的绘制，这也就意味着Qt必然需要提供一种方式来实现，前端和后端数据（属性）、函数（方法）的访问。

#### C++暴露属性、方法给QML

- 1 使用Q_PROPERTY宏为QObject类或QObject类父类指定属性，将数据暴露给QML。
  如下例所示，我们可以在qml文件中访问MessageHandler的name、message和messageCount属性，当属性值发生改变时发射messageChanged、messageCountChanged信号来通知qml。当我们在前端可以通过message="bulabula",此时会触发messageChanged信号，进而调用setMessage函数来实现C++后端相关参数的修改。
- 2 使用Q_INVOKABLE宏将函数注册为槽函数，将接口暴露给QML。
- 3 将函数声明通过public slots将函数声明为公共槽函数，将接口暴露给QML

```
#ifndef MESSAGEHANDLER_H
#define MESSAGEHANDLER_H
#include <QObject>
class MessageHandler : public QObject
{
    Q_OBJECT
public:
    explicit MessageHandler(QObject *parent = 0);
    Q_PROPERTY(QString name             READ name)
    Q_PROPERTY(QString message          READ message    WRITE setMessage    NOTIFY messageChanged)
    Q_PROPERTY(int messageCount         READ messageCount                   NOTIFY messageCountChanged)

    QString name(void)              {return _name;}
    QString message(void)           {return _msg;}
    void setMessage(QString msg)    {_msg = msg;    emit messageChanged(msg);}
    quint16 messageCount(void)      {return _msgCount;}
    Q_INVOKABLE clearAll(void);
signals:
    void messageChanged(QString msg);
    void messageCountChanged(void);
public slots:
    void reset(void);
private:
    QString _name;
    QString _msg;
    int     _msgCount;
};
#endif // MESSAGEHANDLER_H
```

```
#include <QtQml>
#include "MessageHandler.h"
MessageHandler::MessageHandler(QObject *parent)
    : QObject(parent)
    , _name("Test")
    , _msgCount(0)
    , _msg("unknown")
{
    qmlRegisterType<MessageHandler> ("Demo", 1,0,"MessageHandler");
}
```

#### QML中自定义信号

- 1 在自定义的QML控件中通过signal定义信号，构造QML时通过信号处理器来实现信号处理

```
//Message.qml
signal     clear()
```

```
Message{
    onClear:    console.log("Clear is selected.")
}
```

#### C++暴露信号给QML

QML引擎会为QObject类型的信号创建信号处理器,信号处理器的名字是```on<Singal>```，信号处理器通过信号参数名字处理传递过来的参数。

```
class MessageHandler: public QObject
{
    Q_OBJECT
public:
    //...
signals:
    void newMessageArrived(Qstring message);
    void clearAll(void);
}
```

我们可以通过以下方式将该C++中的信号和QML中的处理绑定：

- 1 把MessageHandler注册为QML的类型后，声明MessageHandler对象，利用信号处理器来对信号处理。

```C++
MessageHandler{
    onNewMessageArrived:    console.log("new message arrived: ",message)
    onClearAll:             console.log("Clear all messages")
}
```

- 2 利用Connections对象响应C++信号。

```
Connections{
    target:     MessageHandler
    onNewMessageArrived:{
        console.log("new message arrived: ",message)
    }
}
```

- 信号与方法连接

```qml
    Rectangle {
        id: rect
        anchors.fill:           parent
        signal messageReceived(string message, int count)

        Component.onCompleted: {
            rect.messageReceived.connect(handleMessage)  // 连接信号和方法
        }

        function handleMessage(message, count) {
            console.log(message + " with " + count)
        }
        
        MouseArea{
            anchors.fill:   parent
            onClicked: {
                rect.messageReceived("Test", 1)
            }
        }
    }
```

