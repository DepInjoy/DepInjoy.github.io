---
layout: post
title: QGroundControl介绍
date: 2019-1-20 20:43
category: 地面站
tags: [无人机]
description:
---

​	QGroundControl是一款用跨平台编程语言Qt撰写的一款主要用于PX4飞控进行通信的一款地面站产品，其界面通过ui和qml完成，底层通过C++完成逻辑通信。



### 主要源代码解析

首先，摘录main函数部分主要的函数接口，对主要的代码进行解析。

```C++
    //处理日志记录方式
    AppMessages::installHandler();

    //地图插件
    Q_IMPORT_PLUGIN(QGeoServiceProviderFactoryQGC)

    /*
      QGCApplication为QGC的主要类
      新建的该类是一个全局变量，在该类中new所需要的类
      QGC中其他模块需要访问是可以通过qgcApp()来访问相关的变量或者接口
      QGC通过创建一个全局变量的形式来确保qml和C++操作的类是唯一的
    */
    QGCApplication* app = new QGCApplication(argc, argv, runUnitTests);
    Q_CHECK_PTR(app);

    /*
      设置APP的图标，不同的操作系统设置图标的方式是不同的
      如windows下只需要在pro文件中添加RC_ICONS变量添加ico文件即可
    */
#ifdef Q_OS_LINUX
    QApplication::setWindowIcon(QIcon(":/res/resources/icons/qgroundcontrol.ico"));
#endif /* Q_OS_LINUX */

    /*
        将C++中的一些类注册到qt的元对象系统(meta-object system)，其实现的方式有三种：
        qmlRegisterType:注册的类型可以在qml中创建，相当于C++中实例化一个类
        qmlRegisterUncreatableType：注册的类型在qml中不可实例化，但是此类的属性和方法可以在qml文件中访问。
        qmlRegisterSingletonType：注册单例，其中QGroundControlQmlGlobal是qml和C++类之间进行访问的主要接口。
    */
    app->_initCommon();

     /*
      确定缓存图片的存放位置和地图缓存系统初始化
      地图缓存系统通过QGCTileCacheWorker类来创建一个线程来完成对地图缓存的线程控制
      windows : C:/Users/<USER>/AppData/Local/cache/QGCMapCache**
      下面我会针对该部分进行个人了解的一个简单阐述，目前了解很浅如果后续有机会会对该部分进行不断的完善补充。
    */
    getQGCMapEngine()->init();

    int exitCode = 0;

    /*
      启动用户操作界面并加载用户的一些设置信息
      其中MainWindow::_create()创建界面
    */
    if (!app->_initForNormalAppBoot()) {
        return -1;
    }

    //维持主进程
    exitCode = app->exec();

    //销毁MainWindow实例
    app->_shutdown();

    //销毁QGCApplication实例
    delete app;

    //关闭缓存系统，即销毁QGCMapEngine
    destroyMapEngine();

    return exitCode;
```

然后，介绍QGC中主要的QGCApplication类,该部分主要通过创建全局的_app实例，其他的模块可以同qgcAPP()来实现对其的访问，下面看一下QGCApplication的构造函数的主要代码来明确该部分的主要功能。

```C++
//APP的信息设置，如APP的名称、域名、版本等等
setApplicationName(QGC_APPLICATION_NAME);
setOrganizationName(QGC_ORG_NAME);
setOrganizationDomain(QGC_ORG_DOMAIN);
this->setApplicationVersion(QString(GIT_VERSION));

/*
  设置Setting，主要用于保存用户的一下设置信息，如窗口的大小、HOME点的位置等等
  可以通过读取相关的配置信息，来获取上次打开时的配置信息
*/
QSettings::setDefaultFormat(QSettings::IniFormat);
QSettings settings;

//setting中DeleteAllSettingsNextBoot则在启动后删除所有的设置的参数
fClearSettingsOptions |= settings.contains(_deleteAllSettingsKey);
if (fClearSettingsOptions) {
    settings.clear();
    //删除缓存的参数信息
    QDir paramDir(ParameterManager::parameterCacheDir());
    paramDir.removeRecursively();
    paramDir.mkpath(paramDir.absolutePath());
} else {
    /*
      获取QGC中的参数版本信息，如获取失败，则清除Setting信息
      版本不匹配时，清除Setting信息
    */
    if (settings.contains(_settingsVersionKey)) {
        if (settings.value(_settingsVersionKey).toInt() != QGC_SETTINGS_VERSION) {
            settings.clear();
            _settingsUpgraded = true;
        }
    } else if (settings.allKeys().count()) {
        settings.clear();
        _settingsUpgraded = true;
    }
}
//设置QGC的版本信息
settings.setValue(_settingsVersionKey, QGC_SETTINGS_VERSION);

//Set up our logging filters
QGCLoggingCategoryRegister::instance()->setFilterRulesFromSettings(loggingOptions);

//初始化蓝牙
#ifdef QGC_ENABLE_BLUETOOTH
QBluetoothLocalDevice localDevice;
if (localDevice.isValid())
{
    _bluetoothAvailable = true;
}
#endif

// Initialize Video Streaming
initializeVideoStreaming(argc, argv);

/*
  QGC将需要调用的主要的类在toolbox中创建
  QGCToolbox是在该类中构造的类的父类
  定义protected变量QGCApplication* _app和QGCToolbox* _toolbox来使得其子类可以访问和改变同一个变量
*/
_toolbox = new QGCToolbox(this);
_toolbox->setChildToolboxes();
```

QGCToolbox的子类解析：

```C++
//settings界面设置和参数获取管理
_settingsManager =          new SettingsManager(app, this);

//Scan and load plugins
_scanAndLoadPlugins(app);
//语音播放，通过微软的TTS技术将字符串转化为语音
_audioOutput =              new GAudioOutput            (app, this);
//fact系统，实现不同类型的参数的管理，主要用于参数管理
_factSystem =               new FactSystem              (app, this);
//固件管理，QGC支持PX4和APM两种固件，通过该类来实现管理。实时通信中，通过接收到的心跳中的固件类型和飞机类型来调用不同的类实现不同的操作。
_firmwarePluginManager =    new FirmwarePluginManager   (app, this);
#ifndef __mobile__
//GPS管理器，猜测是获取手机的GPS信息
_gpsManager =               new GPSManager              (app, this);
#endif
_imageProvider =            new QGCImageProvider        (app, this);
_joystickManager =          new JoystickManager         (app, this);
//连接管理，目前QGC支持串口，TCP、UDP、日志回访、MOCK(测试使用)等等
_linkManager =              new LinkManager             (app, this);
//处理MAVlink通信协议
_mavlinkProtocol =          new MAVLinkProtocol         (app, this);
//mission管理
_missionCommandTree =       new MissionCommandTree      (app, this);
//Vehicle管理，管理连接到QGC的多个飞机
_multiVehicleManager =      new MultiVehicleManager     (app, this);
//地图管理，包括缩放等级、地图缓存等等
_mapEngineManager =         new QGCMapEngineManager     (app, this);
//UAS消息处理，该模块像是软仿、硬仿、QGC中模拟遥控摇杆的处理
_uasMessageHandler =        new UASMessageHandler       (app, this);
_qgcPositionManager =       new QGCPositionManager      (app, this);
_followMe =                 new FollowMe                (app, this);
_videoManager =             new VideoManager            (app, this);
//mavlink日志处理
_mavlinkLogManager =        new MAVLinkLogManager       (app, this);
```



#### 地图缓存

​	地图缓存模块维护了一个队列。

```C++
void QGCCacheWorker::run()
{
    if(!_valid && !_failed) {
        _init();
    }
    if(_valid) {
        _db = new QSqlDatabase(QSqlDatabase::addDatabase("QSQLITE", kSession));
        _db->setDatabaseName(_databasePath);
        _db->setConnectOptions("QSQLITE_ENABLE_SHARED_CACHE");
        _valid = _db->open();
    }
    while(true) {
        QGCMapTask* task;
        if(_taskQueue.count()) {
            _mutex.lock();
            task = _taskQueue.dequeue();
            _mutex.unlock();
            switch(task->type()) {
                case QGCMapTask::taskInit:
                    break;
                case QGCMapTask::taskCacheTile:
                    _saveTile(task);
                    break;
                case QGCMapTask::taskFetchTile:
                    _getTile(task);
                    break;
                case QGCMapTask::taskFetchTileSets:
                    _getTileSets(task);
                    break;
                case QGCMapTask::taskCreateTileSet:
                    _createTileSet(task);
                    break;
                case QGCMapTask::taskGetTileDownloadList:
                    _getTileDownloadList(task);
                    break;
                case QGCMapTask::taskUpdateTileDownloadState:
                    _updateTileDownloadState(task);
                    break;
                case QGCMapTask::taskDeleteTileSet:
                    _deleteTileSet(task);
                    break;
                case QGCMapTask::taskPruneCache:
                    _pruneCache(task);
                    break;
                case QGCMapTask::taskReset:
                    _resetCacheDatabase(task);
                    break;
                case QGCMapTask::taskExport:
                    _exportSets(task);
                    break;
                case QGCMapTask::taskImport:
                    _importSets(task);
                    break;
                case QGCMapTask::taskTestInternet:
                    _testInternet();
                    break;
            }
            task->deleteLater();
            //-- Check for update timeout
            size_t count = _taskQueue.count();
            if(count > 100) {
                _updateTimeout = LONG_TIMEOUT;
            } else if(count < 25) {
                _updateTimeout = SHORT_TIMEOUT;
            }
            if(!count || (time(0) - _lastUpdate > _updateTimeout)) {
                if(_valid) {
                    _updateTotals();
                }
            }
        } else {
            //-- Wait a bit before shutting things down
            _waitmutex.lock();
            int timeout = 5000;
            if(!_waitc.wait(&_waitmutex, timeout))
            {
                _waitmutex.unlock();
                _mutex.lock();
                //-- If nothing to do, close db and leave thread
                if(!_taskQueue.count()) {
                    _mutex.unlock();
                    break;
                }
                _mutex.unlock();
            }
            _waitmutex.unlock();
        }
    }
    if(_db) {
        delete _db;
        _db = NULL;
        QSqlDatabase::removeDatabase(kSession);
    }
}
```

