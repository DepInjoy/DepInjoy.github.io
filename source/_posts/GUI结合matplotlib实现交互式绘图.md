---
layout: post
title: GUI结合matplotlib实现交互式绘图
date: 2018-7-18 18:04
category: Python
tags: [Matplotlib, PyQt5, 可视化]
description: 本文描述了如何通过PyQt5将matplotlib的绘图在GUI界面展示，且该示例添加了鼠标滚轮检测支持滚轮缩放展示并可以自动适应窗口大小。
---

Matplotlib作为Python绘图的核心基础数据库，常常作为我们可视化数据分析展示的首选，下面我将介绍一下通过PyQt5绘制GUI实现数据交互。[PyQt5的UI绘制可以结合Qt Designer实现](https://www.jianshu.com/p/7d0add425d75)，我们在Qt的ui文件完成整个界面的布局，之后通过Pyuic工具将ui文件转化为Python脚本，这样的开发过程既可以通过ui提前观察效果，减少代码调试，极大开发时间。需要注意的是：如果你想使得界面可以适应窗口大小，那么便需要添加layout，添加Layout时一定要选中部件，右键选择布局，再选择垂直、水平等其他布局，并设置相应控件的sizePolicy属性为Expanding，可以参考[Qt Designer 设计PyQt5界面时自适应设置](https://blog.csdn.net/loovelj/article/details/79529625)。UI界面设计完成之后，便可以通过pyuic5来生成Python脚本，有如下两种方式：

- 1 通过PyCharm，在Tools->External Tools->Pyuic转换
- 2 直接执行命令
```
pyuic5 -o ui.py ui.ui
```

通过上面的步骤，我们实现了UI控件布局，接下来实现将matplotlib绘图添加到此UI中，matplotlib绘图区域由NavigationToolbar(导航栏)和MatplotCanvas(画布)两大部分组成,下面示例中的MatplotCanvas类实现matplotlib绘图区域，将该类赋值给UI设计中相对应的widget控件，即可实现Matplotlib的绘图结果展示。

经过上面的描述我们完成了通过PyQt5的ui显示Matplotlib的功能，但是Matplotlib可以通过选中区域左键实现放大，右键缩小，这样操作起来很不方便，下面我介绍一下Matplotlib添加鼠标滚轮缩放的实现机制，可以参考博客[Matplotlib事件处理及拾取](https://wizardforcel.gitbooks.io/matplotlib-user-guide/7.3.html)。鼠标滚轮对应于'scroll_event'事件，我们需要写一个回调函数(下面示例的__zoom函数),将事件和函数建立连接
```
self.ax.figure.canvas.mpl_connect('scroll_event', self.__zoom)
```
从而将事件和相关处理的绑定。

最后，给出一套完整示例代码。

```python
import sys
import numpy

from PyQt5 import QtCore, QtGui, QtWidgets
from PyQt5.QtWidgets import QSizePolicy,QWidget,QMainWindow,QApplication
from matplotlib.figure import Figure
from matplotlib.backends.backend_qt5agg import (
 FigureCanvasQTAgg as FigureCanvas,
 NavigationToolbar2QT as NavigationToolbar)
from PyQt5.QtWidgets import QVBoxLayout, QGroupBox

class MatplotCanvas(FigureCanvas):
    def __init__(self, basescale = 1.25):
        self.basescale = basescale
        #setup firgure and axis
        self.__figure = Figure()
        self.__figure.subplots_adjust(top=0.98, bottom=0.05, right=0.99, left=0.05, hspace=10, wspace=10)
        self.ax = self.__figure.add_subplot(111)
        # initialize canvas
        FigureCanvas.__init__(self, self.__figure)
        # set widget as expandable
        FigureCanvas.setSizePolicy(self,
            QSizePolicy.Expanding,QSizePolicy.Expanding)
        FigureCanvas.updateGeometry(self)

        self.ax.figure.canvas.mpl_connect('scroll_event', self.__zoom)
        self.inited = False

    # 鼠标滚轮缩放处理
    def __zoom(self, event):
        if event.inaxes != self.ax.axes:
            return

        if event.button == 'up':
            self.scale = 1 / self.basescale
        elif event.button == 'down':
            self.scale = self.basescale
        else:
            self.scale = 1

        self.curXlim = self.ax.get_xlim()
        self.curYlim = self.ax.get_ylim()
        self.curXRange = (self.curXlim[1] - self.curXlim[0]) * self.scale
        self.curYRange = (self.curYlim[1] - self.curYlim[0]) * self.scale

        relx = (self.curXlim[1] - event.xdata) / (self.curXlim[1] - self.curXlim[0])
        rely = (self.curYlim[1] - event.ydata) / (self.curYlim[1] - self.curYlim[0])

        self.ax.set_xlim([event.xdata - self.curXRange * (1 - relx),
                          event.xdata + self.curXRange * relx])
        self.ax.set_ylim([event.ydata - self.curYRange * (1 - rely),
                          event.ydata + self.curYRange * rely])
        self.draw()

class MatplotWidget(QWidget):
    def __init__(self, parent = None):
        # initialize a Qt mainwindow widget
        QWidget.__init__(self, parent)
        self.canvas = MatplotCanvas()
        self.canvas.ax.grid()
        self.grided = True
        self.canvas.ax.relim()
        self.canvas.ax.autoscale(True)
        self.navigater = NavigationToolbar(self.canvas, self)
        self.__vlayout = QVBoxLayout()
        # add the navigation toolbar and canvas to vertical box
        self.__vlayout.addWidget(self.navigater)
        self.__vlayout.addWidget(self.canvas)
        self.setLayout(self.__vlayout)

class Ui_MainWindow(object):
    # 界面绘制
    def setupUi(self, MainWindow):
        MainWindow.setObjectName("MainWindow")
        MainWindow.setWindowModality(QtCore.Qt.WindowModal)
        MainWindow.resize(865, 608)
        sizePolicy = QtWidgets.QSizePolicy(QtWidgets.QSizePolicy.Expanding, QtWidgets.QSizePolicy.Expanding)
        sizePolicy.setHorizontalStretch(0)
        sizePolicy.setVerticalStretch(0)
        sizePolicy.setHeightForWidth(MainWindow.sizePolicy().hasHeightForWidth())
        MainWindow.setSizePolicy(sizePolicy)
        MainWindow.setMinimumSize(QtCore.QSize(865, 608))
        self.centralwidget = QtWidgets.QWidget(MainWindow)
        sizePolicy = QtWidgets.QSizePolicy(QtWidgets.QSizePolicy.Expanding, QtWidgets.QSizePolicy.Expanding)
        sizePolicy.setHorizontalStretch(0)
        sizePolicy.setVerticalStretch(0)
        sizePolicy.setHeightForWidth(self.centralwidget.sizePolicy().hasHeightForWidth())
        self.centralwidget.setSizePolicy(sizePolicy)
        self.centralwidget.setMinimumSize(QtCore.QSize(846, 557))
        self.centralwidget.setObjectName("centralwidget")
        self.gridLayout_2 = QtWidgets.QGridLayout(self.centralwidget)
        self.gridLayout_2.setObjectName("gridLayout_2")
        self.gridLayout = QtWidgets.QGridLayout()
        self.gridLayout.setObjectName("gridLayout")
        self.data_plot = MatplotWidget()
        sizePolicy = QtWidgets.QSizePolicy(QtWidgets.QSizePolicy.Expanding, QtWidgets.QSizePolicy.Expanding)
        sizePolicy.setHorizontalStretch(0)
        sizePolicy.setVerticalStretch(0)
        sizePolicy.setHeightForWidth(self.data_plot.sizePolicy().hasHeightForWidth())
        self.data_plot.setSizePolicy(sizePolicy)
        self.data_plot.setMinimumSize(QtCore.QSize(711, 541))
        self.data_plot.setObjectName("data_plot")
        self.gridLayout.addWidget(self.data_plot, 0, 0, 3, 1)
        self.draw_but = QtWidgets.QPushButton(self.centralwidget)
        sizePolicy = QtWidgets.QSizePolicy(QtWidgets.QSizePolicy.Fixed, QtWidgets.QSizePolicy.Fixed)
        sizePolicy.setHorizontalStretch(0)
        sizePolicy.setVerticalStretch(0)
        sizePolicy.setHeightForWidth(self.draw_but.sizePolicy().hasHeightForWidth())
        self.draw_but.setSizePolicy(sizePolicy)
        self.draw_but.setMinimumSize(QtCore.QSize(93, 28))
        self.draw_but.setObjectName("draw_but")
        self.draw_but.clicked.connect(self.drawData)
        self.gridLayout.addWidget(self.draw_but, 0, 2, 1, 1)
        self.clear_but = QtWidgets.QPushButton(self.centralwidget)
        sizePolicy = QtWidgets.QSizePolicy(QtWidgets.QSizePolicy.Fixed, QtWidgets.QSizePolicy.Fixed)
        sizePolicy.setHorizontalStretch(0)
        sizePolicy.setVerticalStretch(0)
        sizePolicy.setHeightForWidth(self.clear_but.sizePolicy().hasHeightForWidth())
        self.clear_but.setSizePolicy(sizePolicy)
        self.clear_but.setMinimumSize(QtCore.QSize(93, 28))
        self.clear_but.setObjectName("clear_but")
        self.clear_but.clicked.connect(self.clearData)
        self.gridLayout.addWidget(self.clear_but, 1, 2, 1, 1)
        spacerItem = QtWidgets.QSpacerItem(15, 20, QtWidgets.QSizePolicy.Fixed, QtWidgets.QSizePolicy.Minimum)
        self.gridLayout.addItem(spacerItem, 2, 1, 1, 1)
        spacerItem1 = QtWidgets.QSpacerItem(20, 465, QtWidgets.QSizePolicy.Minimum, QtWidgets.QSizePolicy.Expanding)
        self.gridLayout.addItem(spacerItem1, 2, 2, 1, 1)
        self.gridLayout_2.addLayout(self.gridLayout, 0, 0, 1, 1)
        MainWindow.setCentralWidget(self.centralwidget)
        self.menubar = QtWidgets.QMenuBar(MainWindow)
        self.menubar.setGeometry(QtCore.QRect(0, 0, 865, 26))
        self.menubar.setObjectName("menubar")
        MainWindow.setMenuBar(self.menubar)
        self.statusbar = QtWidgets.QStatusBar(MainWindow)
        self.statusbar.setObjectName("statusbar")
        MainWindow.setStatusBar(self.statusbar)

        self.retranslateUi(MainWindow)
        QtCore.QMetaObject.connectSlotsByName(MainWindow)

    def retranslateUi(self, MainWindow):
        _translate = QtCore.QCoreApplication.translate
        MainWindow.setWindowTitle(_translate("MainWindow", "MainWindow"))
        self.draw_but.setText(_translate("MainWindow", "Draw"))
        self.clear_but.setText(_translate("MainWindow", "Clear"))

    def drawData(self):
        x = numpy.arange(0, 100, 0.1)
        y = numpy.sin(x)
        if not self.data_plot.grided:
            self.data_plot.canvas.ax.grid()
            self.data_plot.grided = True
        self.data_plot.canvas.ax.plot(x, y)
        self.data_plot.canvas.ax.legend(["y=sin(x)"], loc="upper right")
        self.data_plot.canvas.draw()

    def clearData(self):
        self.data_plot.canvas.ax.clear()
        self.data_plot.grided = False
        if not self.data_plot.grided:
            self.data_plot.canvas.ax.grid()
            self.data_plot.grided = True
        self.data_plot.canvas.draw()

class DesktopGui(QMainWindow, Ui_MainWindow):
    def __init__(self, parent = None):
        super(DesktopGui, self).__init__(parent)
        self.setupUi(self)

if __name__ == "__main__":
    app = QApplication(sys.argv)
    gui = DesktopGui()
    gui.showMaximized()
    gui.show()
    sys.exit(app.exec_())
```