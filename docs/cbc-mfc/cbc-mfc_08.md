# MFC 中所提供的各种视类介绍

MFC 中提供了丰富的视类供开发者使用，下面对各个类进行介绍：
1) CView 类是最基本的视类只支持最基本的操作。

2) CScrollView 类提供了滚动的功能，你可以利用 void CScrollView::SetScrollSizes( int nMapMode, SIZE sizeTotal, const SIZE& sizePage = sizeDefault, const SIZE& sizeLine = sizeDefault )设置滚动尺寸，和坐标映射模式。但是在绘图和接收用户输入时需要对坐标进行转换。

3) CFormView 类提供用户在资源文件中定义界面的能力，并可以将子窗口和变量进行绑定。通过 UpdateData 函数让数据在变量和子窗口间交换。

4) CTreeView 类利用 TreeCtrl 界面作为视界面，通过调用 CTreeCtrl& CTreeView::GetTreeCtrl( ) const 得到 CTreeCtrl 的引用。

5) CListView 类利用 ListCtrl 界面作为视界面，通过调用 CTreeCtrl& CTreeView::GetTreeCtrl( ) const 得到 CListCtrl 的引用。

6) CEditView 类利用 Edit 接收用户输入，它具有输入框的一切功能。通过调用 CEdit& CEditView::GetEditCtrl( ) const 得到 Edit&的引用。void CEditView::SetPrinterFont( CFont* pFont )可以设置打印字体。

7) CRichEditView 类作为 Rich Text Edit（富文本输入）的视类，提供了可以按照格式显示文本的能力，在使用时需要 CRichEditDoc 的支持。