# MFC 文档与视图结构

第二节介绍了文档、视图的关系，两者是一个一对多的映射。一个文档可以对应多个视图，而一个视图只能对应一个文档。举个例子，一个*.html 文件，可以用记事本打开，也可以用浏览器打开。这里*.html 文件就是文档，记事本和浏览器就是这个文档的两个视图。

文档和视图结构的优势就在于数据的管理与显示分离，数据总是要以一定的结构来存储，而视图负责数据的呈现。数据往往有多种呈现方式，例如一份报表，可以用表格的形式呈现，也可以用图表的形式呈现。根据不同的需求以不同的形式呈现，这就是视图的特性。

在使用文档/视图体系开发应用程序过程中，涉及的不仅仅是文档与视图。总体来说，涉及 4 个部分：文档模板、文档、视图和框架窗口。

下面以 VC2010 创建多文档应用程序为例来介绍文档/视图结构，项目名称为 MDITest。首先来看看应用程序的初始化过程：

```
// CMDITestApp 初始化
BOOL CMDITestApp::InitInstance()
{
    // 如果一个运行在 Windows XP 上的应用程序清单指定要使用
    //ComCtl32.dll 版本 6 或更高版本来启用可视化方式，
    //则需要 InitCommonControlsEx()。否则，将无法创建窗口。
    INITCOMMONCONTROLSEX InitCtrls;
    InitCtrls.dwSize = sizeof(InitCtrls);
    // 将它设置为包括所有要在应用程序中使用的公共控件类
    InitCtrls.dwICC = ICC_WIN95_CLASSES;
    InitCommonControlsEx(&InitCtrls);
    CWinAppEx::InitInstance();
    // 初始化 OLE 库
    if (!AfxOleInit())
    {
        AfxMessageBox(IDP_OLE_INIT_FAILED);
        return FALSE;
    }

    AfxEnableControlContainer();

    EnableTaskbarInteraction();

    // 使用 RichEdit 控件需要  AfxInitRichEdit2()   
    // AfxInitRichEdit2();
    // 标准初始化
    // 如果未使用这些功能并希望减小
    // 最终可执行文件的大小，则应移除下列
    // 不需要的特定初始化例程
    // 更改用于存储设置的注册表项
    // TODO: 应适当修改该字符串，
    // 例如修改为公司或组织名
    SetRegistryKey(_T("应用程序向导生成的本地应用程序"));
    LoadStdProfileSettings(4);  // 加载标准 INI 文件选项(包括 MRU)
    InitContextMenuManager();
    InitKeyboardManager();
    InitTooltipManager();
    CMFCToolTipInfo ttParams;
    ttParams.m_bVislManagerTheme = TRUE;
    theApp.GetTooltipManager()->SetTooltipParams(AFX_TOOLTIP_TYPE_ALL,
        RUNTIME_CLASS(CMFCToolTipCtrl), &ttParams);

    // 注册应用程序的文档模板。文档模板
    // 将用作文档、框架窗口和视图之间的连接
    CMultiDocTemplate* pDocTemplate;
    pDocTemplate = new CMultiDocTemplate(IDR_MDITestTYPE,
        RUNTIME_CLASS(CMDITestDoc),
        RUNTIME_CLASS(CChildFrame), // 自定义 MDI 子框架
        RUNTIME_CLASS(CMDITestView));
    if (!pDocTemplate)
        return FALSE;
    AddDocTemplate(pDocTemplate);

    // 创建主 MDI 框架窗口
    CMainFrame* pMainFrame = new CMainFrame;
    if (!pMainFrame || !pMainFrame->LoadFrame(IDR_MAINFRAME))
    {
        delete pMainFrame;
        return FALSE;
    }
    m_pMainWnd = pMainFrame;
    // 仅当具有后缀时才调用 DragAcceptFiles
    //  在 MDI 应用程序中，这应在设置 m_pMainWnd 之后立即发生

    // 分析标准 shell 命令、DDE、打开文件操作的命令行
    CCommandLineInfo cmdInfo;
    ParseCommandLine(cmdInfo);
    // 调度在命令行中指定的命令。如果
    // 用 /RegServer、/Register、/Unregserver 或 /Unregister 启动应用程序，则返回 FALSE。
    if (!ProcessShellCommand(cmdInfo))
        return FALSE;
    // 主窗口已初始化，因此显示它并对其进行更新
    pMainFrame->ShowWindow(m_nCmdShow);
    pMainFrame->UpdateWindow();

    return TRUE;
}
```

在这个初始化过程中，首先创建 CMultiDocTemplate 类型的多文档模块，并使用 AddDocTemplate 把多文档模板对象添加到文档模板管理器。然后创建 CMainFrame 类型的框架窗口，最后显示并更新框架窗口。通过文档模板抽象基类 CDocTemplate,把框架窗口、文档与视图三者关联起来。对于单文档应用程序，需要使用单文档模板类 CSingleDocTemplate,而对于多文档应用程序，需要使用多文档模板类 CMultiDocTemplate。

文档模板之所以有能够与文档、视图和框架窗口三者相关联，是因为文档模板类 CDocTemplate 中定义了文档、视图和框架窗口的 CRuntimeClass 对象指针。而这三个 CRuntimeClass 对象的初始化恰好在文档模板的构造函数中实现。学习的过程中，需要慢慢体会这种设计的巧妙。

文档派生于 CDocument 类，负责存储应用程序的数据，并把这些数据提供给其他部分，可以在源码中查看 CDocument 类定义。

一个文档可以与多个视图关联，每一个视图仅仅是与其相关联文档数据的一种表现形式，在 CDocument 类中定义了一个 CPtrList 类型的变量 m_viewList，用于维护与当前文档对象相关联的视图链表。使用成员函数 AddView 可以添加视图，使用成员函数 RemoveView 可以删除视图。

小经验：MFC 中系统封装了大量的类，对于初学者，在学习的时候不妨多动手，尝试着调用一下这些方法，运行并查看结果，这对于理解这些类很有帮助。

MFC 的 CView 类是所有视图类的基类，主要有两大功能：

*   将与其相关联文档的数据呈现给用户。
*   接受用户对数据的修改，并反馈给文档。

在 CView 类中定义了一个文档对象指针 m_pDocument，在应用程序中可以使用 CView 的成员函数 GetDocument 获取与特定视图对象相关联的文档对象。

当视图中的数据被修改后，除了需要通知文档修改数据外，还需要使用 CDocument 类的成员函数 UpdateAllViews 通知所有与该文档相关联的视图更新显示数据。

在文档/视图结构中，文档负责数据的管理，视图负责数据的显示，框架窗口则负责管理这些界面。框架窗口有两种模式：单文档窗口 SDI 和多文档窗口 MDI。在 SDI 环境下，同时最多只能一个框架窗口；而在 MDI 环境下则没有这个限制。