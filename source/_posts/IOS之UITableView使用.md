---
title: IOS之UITableView使用
date: 2019-07-18 16:05:35
keywords:
    - Ios
    - Swift
tags:
    - Swift
    - Ios
categories:
    - Ios
---

# 简述

iOS的`UITableView`显示单列垂直滚动内容。表格中的每一行都包含一段应用内容。例如，“联系人”应用程序在单独的行中显示每个联系人的姓名，“设置”应用程序的主页面显示可用的设置组。您可以将表配置为显示单个长行列表，也可以将相关行分组为多个部分，以便更轻松地导航内容。

<!-- more -->

![](tableviewpreview.png)

# [UITableView](https://developer.apple.com/documentation/uikit/uitableview)

`UITableView`管理表的基本外观，提供显示实际内容的单元格（对象）。标准单元格配置显示文本和图像的简单组合，但您可以定义显示所需内容的自定义单元格。您还可以提供页眉和页脚视图，以便为单元格组提供其他信息。

`UITableView`有两种风格：`UITableViewStylePlain`和`UITableViewStyleGrouped`。这两者操作起来其实并没有本质区别，只是后者按分组样式显示前者按照普通样式显示而已。大家先看一下两者的应用：

![](groupandnonggroup.png)

**UITableView的层次结构**

![](tableviewintro.png)

## 类方法

### 创建表视图

``` swift
init(frame: CGRect, style: UITableView.Style)
//初始化并返回具有给定框架和样式的表视图对象。

init?(coder: NSCoder)
```

### 提供表格的数据和单元格

``` swift
var dataSource: UITableViewDataSource?
//充当表视图数据源的对象。

var prefetchDataSource: UITableViewDataSourcePrefetching?
//充当表视图的预取数据源的对象，接收即将到来的单元数据要求的通知。

protocol UITableViewDataSource
//您用于管理数据并为表视图提供单元格的对象采用的方法。

protocol UITableViewDataSourcePrefetching
//一种协议，提供表视图数据要求的预先警告，允许您尽早启动可能长时间运行的数据操作。
```

### 重用表视图单元格

``` swift
func register(UINib?, forCellReuseIdentifier: String)
//注册包含具有指定标识符下的表视图的单元格的nib对象。

func register(AnyClass?, forCellReuseIdentifier: String)
//注册用于创建新表格单元格的类。

func dequeueReusableCell(withIdentifier: String, for: IndexPath) -> UITableViewCell
//返回指定重用标识符的可重用表视图单元对象，并将其添加到表中。

func dequeueReusableCell(withIdentifier: String) -> UITableViewCell?
//返回按其标识符定位的可重用表视图单元对象。
```

### 重用部分页眉和页脚

``` swift
func register(UINib?, forHeaderFooterViewReuseIdentifier: String)
//使用指定标识符下的表视图注册包含页眉或页脚的nib对象。

func register(AnyClass?, forHeaderFooterViewReuseIdentifier: String)
//注册一个类，用于创建新的表头或页脚视图。

func dequeueReusableHeaderFooterView(withIdentifier: String) -> UITableViewHeaderFooterView?
//返回由其标识符定位的可重用页眉或页脚视图。
```

### 管理与表的交互

``` swift
var delegate: UITableViewDelegate?
//充当表视图委托的对象。

protocol UITableViewDelegate
//管理选择，配置节页眉和页脚，删除和重新排序单元格以及在表格视图中执行其他操作的方法。
```

### 配置表的外观

``` swift
var style: UITableView.Style
//表格视图的样式。

enum UITableView.Style
//表视图样式的常量。

var tableHeaderView: UIView?
//显示在表格内容上方的视图。

var tableFooterView: UIView?
//显示在表格内容下方的视图。

var backgroundView: UIView?
//表视图的背景视图。
```

### 配置单元格高度和布局

``` swift
var rowHeight: CGFloat
//表视图中每行的默认高度（以磅为单位）。

var estimatedRowHeight: CGFloat
//表视图中行的估计高度。

var cellLayoutMarginsFollowReadableWidth: Bool
//一个布尔值，指示单元格边距是否从可读内容指南的宽度派生。

var insetsContentViewsToSafeArea: Bool
//一个布尔值，指示表视图是否将其内容视图重新定位在当前安全区域内。
```

### 配置页眉和页脚高度

``` swift
var sectionHeaderHeight: CGFloat
//表视图中节标题的高度。

var sectionFooterHeight: CGFloat
//表格视图中部分页脚的高度。

var estimatedSectionHeaderHeight: CGFloat
//表视图中节标题的估计高度。

var estimatedSectionFooterHeight: CGFloat
//表格视图中部分页脚的估计高度。
```

### 自定义分隔符外观

``` swift
var separatorStyle: UITableViewCell.SeparatorStyle
//用作分隔符的表格单元格的样式。

enum UITableViewCell.SeparatorStyle
//用作分隔符的单元格样式。

var separatorColor: UIColor?
//表视图中分隔符行的颜色。

var separatorEffect: UIVisualEffect?
//应用于表分隔符的效果。

var separatorInset: UIEdgeInsets
//单元格分隔符的默认插入。

var separatorInsetReference: UITableView.SeparatorInsetReference
//应该如何解释分隔符插入值的指示符。

enum UITableView.SeparatorInsetReference
//指示如何解释表视图的分隔符插入值的常量。
```

### 获取行数和节数

``` swift
func numberOfRows(inSection: Int) -> Int
//返回指定节中的行数（表格单元格）。

var numberOfSections: Int
//表视图中的节数。
```

### 获取单元格和基于节的视图

``` swift
func cellForRow(at: IndexPath) -> UITableViewCell?
//返回指定索引路径的表格单元格。

func headerView(forSection: Int) -> UITableViewHeaderFooterView?
//返回与指定节相关联的标题视图。

func footerView(forSection: Int) -> UITableViewHeaderFooterView?
//返回与指定节相关联的页脚视图。

func indexPath(for: UITableViewCell) -> IndexPath?
//返回表示给定表视图单元格的行和部分的索引路径。

func indexPathForRow(at: CGPoint) -> IndexPath?
//返回标识给定点处的行和节的索引路径。

func indexPathsForRows(in: CGRect) -> [IndexPath]?
//索引路径数组，每个索引路径表示由给定矩形包围的行。

var visibleCells: [UITableViewCell]
//表视图中可见的表格单元格。

var indexPathsForVisibleRows: [IndexPath]?
//索引路径数组，每个索引路径标识表视图中的可见行。
```

### 选择行

``` swift
var indexPathForSelectedRow: IndexPath?
//标识所选行的行和部分的索引路径。

var indexPathsForSelectedRows: [IndexPath]?
//表示所选行的索引路径。

func selectRow(at: IndexPath?, animated: Bool, scrollPosition: UITableView.ScrollPosition)
//选择由索引路径标识的表视图中的行，可选择将行滚动到表视图中的某个位置。

func deselectRow(at: IndexPath, animated: Bool)
//取消选择由索引路径标识的给定行，并选择为取消选择设置动画。

var allowsSelection: Bool
//一个布尔值，用于确定用户是否可以选择行。

var allowsMultipleSelection: Bool
//一个布尔值，用于确定用户是否可以在编辑模式之外选择多个行。

var allowsSelectionDuringEditing: Bool
//一个布尔值，用于确定用户在表视图处于编辑模式时是否可以选择单元格。

var allowsMultipleSelectionDuringEditing: Bool
//一个布尔值，控制用户是否可以在编辑模式下同时选择多个单元格。

class let selectionDidChangeNotification: NSNotification.Name
//发布表视图中的选定行发生更改时发布。
```

### 插入，删除和移动行和节

``` swift
func insertRows(at: [IndexPath], with: UITableView.RowAnimation)
//在表视图中插入由索引路径数组标识的位置的行，并提供动画插入的选项。

func deleteRows(at: [IndexPath], with: UITableView.RowAnimation)
//删除索引路径数组指定的行，并带有为删除设置动画的选项。

func insertSections(IndexSet, with: UITableView.RowAnimation)
//在表视图中插入一个或多个部分，并提供动画插入的选项。

func deleteSections(IndexSet, with: UITableView.RowAnimation)
//删除表视图中的一个或多个部分，并带有为删除设置动画的选项。

enum UITableView.RowAnimation
//插入或删除行时使用的动画类型。

func moveRow(at: IndexPath, to: IndexPath)
//将指定位置的行移动到目标位置。

func moveSection(Int, toSection: Int)
//将节移动到表视图中的新位置。
```

### 对行和节执行批量更新

``` swift
func performBatchUpdates((() -> Void)?, completion: ((Bool) -> Void)?)
//动画多个插入，删除，重新加载和移动操作作为一组。

func beginUpdates()
//开始一系列方法调用，插入，删除或选择表视图的行和部分。

func endUpdates()
//结束一系列方法调用，插入，删除，选择或重新加载表视图的行和部分。
```

### 配置表索引

``` swift
var sectionIndexMinimumDisplayRowCount: Int
//用于在表的右边缘显示索引列表的表行数。

var sectionIndexColor: UIColor?
//用于表视图索引文本的颜色。

var sectionIndexBackgroundColor: UIColor?
//用于表视图的节索引背景的颜色。

var sectionIndexTrackingBackgroundColor: UIColor?
//用于表视图的索引背景区域的颜色。

class let indexSearch: String
//用于将放大镜图标添加到表视图的节索引的常量。
```

### 重新加载表视图

``` swift
var hasUncommittedUpdates: Bool
//一个布尔值，指示表视图的外观是否包含未在其数据源中反映的更改。

func reloadData()
//重新加载表视图的行和部分。

func reloadRows(at: [IndexPath], with: UITableView.RowAnimation)
//使用给定的动画效果重新加载指定的行。

func reloadSections(IndexSet, with: UITableView.RowAnimation)
//使用给定的动画效果重新加载指定的部分。

func reloadSectionIndexTitles()
//重新加载表视图右侧索引栏中的项目。
```

###  管理拖动交互

``` swift
var dragDelegate: UITableViewDragDelegate?
//委托对象管理从表视图中拖动项目。

protocol UITableViewDragDelegate
//用于从表视图启动拖动的界面。

var hasActiveDrag: Bool
//一个布尔值，指示是否从表视图中提升行并且尚未删除。

var dragInteractionEnabled: Bool
//一个布尔值，指示表视图是否支持应用之间的拖放。
```

### 管理丢弃交互

``` swift
var dropDelegate: UITableViewDropDelegate?
//管理将内容删除到表视图中的委托对象。

protocol UITableViewDropDelegate
//用于处理的接口在表视图中丢弃。

var hasActiveDrop: Bool
//一个布尔值，指示表视图当前是否正在跟踪放置会话。
```

### 滚动表视图

``` swift
func scrollToRow(at: IndexPath, at: UITableView.ScrollPosition, animated: Bool)
//滚动表视图，直到索引路径标识的行位于屏幕上的特定位置。

func scrollToNearestSelectedRow(at: UITableView.ScrollPosition, animated: Bool)
//滚动表格视图，以便最接近表格视图中指定位置的选定行位于该位置。

enum UITableView.ScrollPosition
//表格视图中的位置（顶部，中间，底部），滚动给定行。
```

### 将表置于编辑模式

``` swift
func setEditing(Bool, animated: Bool)
//切换表格视图进出编辑模式。

var isEditing: Bool
//一个布尔值，用于确定表视图是否处于编辑模式。
```

### 获取表格的绘图区域

``` swift
func rect(forSection: Int) -> CGRect
//返回表视图的指定部分的绘图区域。

func rectForRow(at: IndexPath) -> CGRect
//返回由索引路径标识的行的绘图区域。

func rectForFooter(inSection: Int) -> CGRect
//返回指定节的页脚的绘图区域。

func rectForHeader(inSection: Int) -> CGRect
//返回指定节的标题的绘图区域。
```

### 记住最后一个聚焦的单元格

``` swift
var remembersLastFocusedIndexPath: Bool
//一个布尔值，指示表视图是否应自动将焦点返回到上一个焦点索引路径处的单元格。
```

# [UITableViewCell](https://developer.apple.com/documentation/uikit/uitableviewcell)

UITableViewCell对象是管理单个表行内容的专用视图类型。您主要使用单元格来组织和显示应用程序的自定义内容，但UITableViewCell提供了一些特定的自定义以支持与表相关的行为：

- 将选定内容或突出显示颜色应用于单元格。

- 添加标准附件视图，如详细信息或披露控制。

- 将单元格置于可编辑状态。

- 缩进单元格内容以在表中创建可视层次结构。

> A `UITableViewCell` object is a specialized type of view that manages the content of a single table row. You use cells primarily to organize and present your app’s custom content, but `UITableViewCell` provides some specific customizations to support table-related behaviors, including:
>
> - Applying a selection or highlight color to the cell.
> - Adding standard accessory views, such as a detail or disclosure control.
> - Putting the cell into an editable state.
> - Indenting the cell's content to create a visual hierarchy in your table.
>
> Your app’s content occupies most of the cell’s bounds, but the cell may adjust that space to make room for other content. Cells display accessory views on the trailing edge of their content area. When you put your table into edit mode, the cell adds a delete control to the leading edge of its content area, and optionally swaps out an accessory view for a reorder control.

**UITableViewCellStyle/UITableViewCell.CellStyle**

``` swift
extension UITableViewCell {
		public enum CellStyle : Int {
        case `default`  //具有文本标签（黑色和左对齐）和可选图像视图的单元格的简单样式。
        case value1			//单元格样式，单元格左侧带有标签，左对齐和黑色文本; 在右侧是一个标签，蓝色文字较小，右对齐。“设置”应用程序使用此样式的单元格。
        case value2			//单元格样式，单元格左侧带有标签，文本右对齐，蓝色; 在单元格的右侧是另一个标签，其中较小的文本是左对齐和黑色。电话/联系人应用程序使用此样式的单元格。
        case subtitle 	//单元格的样式，顶部带有左对齐标签，下面带有左对齐标签，带有较小的灰色文本。
    }
}
```

``` objc
typedef NS_ENUM(NSInteger, UITableViewCellStyle) {
    UITableViewCellStyleDefault,    
    UITableViewCellStyleValue1,        
    UITableViewCellStyleValue2,        
    UITableViewCellStyleSubtitle    
};
```

![](cellstyle.png)

**Cell的层次结构**

![](cellintro.png)

## 类方法

### 创建表视图单元格

``` swift
init(style: UITableViewCell.CellStyle, reuseIdentifier: String?)
//使用样式和重用标识符初始化表格单元格并将其返回给调用者。

enum UITableViewCell.CellStyle
//各种样式细胞的枚举。

init?(coder: NSCoder)
```

### 重用

``` swift
var reuseIdentifier: String?
//用于标识可重用单元的字符串。

func prepareForReuse()
//准备一个可重用的单元格以供表视图委托重用。
```

### 指定标准单元格样式的内容

``` swift
var textLabel: UILabel?
//用于表格单元格主要文本内容的标签。

var detailTextLabel: UILabel?
//表格单元格的辅助标签（如果存在）。

var imageView: UIImageView?
//表格单元格的图像视图。
```

### 访问单元格对象的视图

``` swift
var contentView: UIView
//单元格对象的内容视图。

var backgroundView: UIView?
//该视图用作单元格的背景。

var selectedBackgroundView: UIView?
//视图在选中时用作单元格的背景。

var multipleSelectionBackgroundView: UIView?
//当表视图允许多行选择时，用于选定单元格的背景视图。
```

### 管理附件视图

``` swift
var accessoryType: UITableViewCell.AccessoryType
//单元应使用的标准附件视图的类型（正常状态）。

var accessoryView: UIView?
//在单元格的右侧（正常状态）使用的视图，通常用作控件。

var editingAccessoryType: UITableViewCell.AccessoryType
//单元格应在表格视图的编辑状态中使用的标准附件视图的类型。

var editingAccessoryView: UIView?
//在编辑模式下，通常用作单元格右侧控件的视图。

enum UITableViewCell.AccessoryType
//单元使用的标准附件控件的类型。
```

### 管理单元格选择和突出显示

``` swift
var selectionStyle: UITableViewCell.SelectionStyle
//细胞的选择方式。

enum UITableViewCell.SelectionStyle
//选定单元格的样式。

var isSelected: Bool
//一个布尔值，指示是否选择了单元格。

func setSelected(Bool, animated: Bool)
//设置单元格的选定状态，可选地为状态之间的过渡设置动画。

var isHighlighted: Bool
//一个布尔值，指示单元格是否突出显示。

func setHighlighted(Bool, animated: Bool)
//设置单元格的突出显示状态，可选地为状态之间的过渡设置动画。
```

### 编辑单元格

``` swift
var isEditing: Bool
//一个布尔值，指示单元格是否处于可编辑状态。

func setEditing(Bool, animated: Bool)
//切换接收器进入和退出编辑模式。

var editingStyle: UITableViewCell.EditingStyle
//单元格的编辑样式。

enum UITableViewCell.EditingStyle
//单元格使用的编辑控件。

var showingDeleteConfirmation: Bool
//一个布尔值，指示单元格当前是否显示删除确认按钮。

var showsReorderControl: Bool
//一个布尔值，用于确定单元格是否显示重新排序控件。
```

### 拖动行

``` swift
var userInteractionEnabledWhileDragging: Bool
//一个布尔值，指示用户在拖动单元格时是否可以与单元格进行交互。

func dragStateDidChange(UITableViewCell.DragState)
//通知单元格其拖动状态已更改。

enum UITableViewCell.DragState
//指示拖动操作中涉及的行的当前状态的常量。
```

### 适应状态转变

``` swift
func willTransition(to: UITableViewCell.StateMask)
//被调用以通知单元格它将要转换到新的单元状态。

func didTransition(to: UITableViewCell.StateMask)
//被调用以通知单元格它已转换到新的单元状态。

struct UITableViewCell.StateMask
//常量用于在状态之间转换时确定单元格的新状态。
```

### 管理内容缩进

``` swift
var indentationLevel: Int
//单元格内容的缩进级别。

var indentationWidth: CGFloat
//单元格内容的每个缩进级别的宽度。

var shouldIndentWhileEditing: Bool
//一个布尔值，用于控制表视图处于编辑模式时是否缩进单元格背景。

var separatorInset: UIEdgeInsets
//在单元格下方绘制的分隔线的插入值。
```

### 管理焦点

``` swift
var focusStyle: UITableViewCell.FocusStyle
//聚焦时的外观。

enum UITableViewCell.FocusStyle
//聚焦的风格。
```

### 枚举

``` swift
enum UITableViewCell.SeparatorStyle
用作分隔符的单元格样式。
```

# [UITableViewDelegate](https://developer.apple.com/documentation/uikit/uitableviewdelegate)

`UITableViewDelegate`用来管理选择，配置节页眉和页脚，删除和重新排序单元格以及在表格视图中执行其他操作的方法。例如：

- 创建和管理自定义页眉和页脚视图。
- 指定行，页眉和页脚的自定义高度。
- 提供高度估计以获得更好的滚动支持。
- 缩进行内容。
- 响应行选择。
- 响应表行中的滑动和其他操作。
- 支持编辑表格的内容。

## **协议方法：**

### 配置表视图行

```swift
func tableView(UITableView, willDisplay: UITableViewCell, forRowAt: IndexPath)
//告诉委托表视图是否要为特定行绘制单元格。

func tableView(UITableView, indentationLevelForRowAt: IndexPath) -> Int
//要求委托者返回给定部分中某行的缩进级别。

func tableView(UITableView, shouldSpringLoadRowAt: IndexPath, with: UISpringLoadedInteractionContext) -> Bool
//被调用以允许您微调表中行的弹簧加载行为。
```

### 响应行选择

``` swift
func tableView(UITableView, willSelectRowAt: IndexPath) -> IndexPath?
//告诉委托者即将选择指定的行。

func tableView(UITableView, didSelectRowAt: IndexPath)
//告诉委托者现在选择了指定的行。

func tableView(UITableView, willDeselectRowAt: IndexPath) -> IndexPath?
//告诉委托者即将取消选择指定的行。

func tableView(UITableView, didDeselectRowAt: IndexPath)
//告诉代理现在取消选择指定的行。
```

###  提供自定义页眉和页脚视图

``` swift
func tableView(UITableView, viewForHeaderInSection: Int) -> UIView?
//要求委托使视图对象显示在表视图的指定部分的标题中。

func tableView(UITableView, viewForFooterInSection: Int) -> UIView?
//要求委托使视图对象显示在表视图的指定部分的页脚中。

func tableView(UITableView, willDisplayHeaderView: UIView, forSection: Int)
//告诉委托表该表即将显示指定节的标题视图。

func tableView(UITableView, willDisplayFooterView: UIView, forSection: Int)
//告诉代理该表即将显示指定部分的页脚视图。
```

### 提供页眉，页脚和行高

``` swift
func tableView(UITableView, heightForRowAt: IndexPath) -> CGFloat
//询问代理用于指定位置的行的高度。

func tableView(UITableView, heightForHeaderInSection: Int) -> CGFloat
//询问代理用于特定部分标题的高度。

func tableView(UITableView, heightForFooterInSection: Int) -> CGFloat
//询问代理用于特定部分的页脚的高度。

class let automaticDimension: CGFloat
//表示给定维度的默认值的常量。
```

### 估计表格内容的高度

``` swift
func tableView(UITableView, estimatedHeightForRowAt: IndexPath) -> CGFloat
//询问代理指定位置中行的估计高度。

func tableView(UITableView, estimatedHeightForHeaderInSection: Int) -> CGFloat
//询问代理特定部分标题的估计高度。

func tableView(UITableView, estimatedHeightForFooterInSection: Int) -> CGFloat
//询问代理特定部分的页脚估计高度。
```

### 管理附件视图

``` swift
func tableView(UITableView, accessoryButtonTappedForRowWith: IndexPath)
//告诉代理用户点击了指定行的详细信息按钮。
```

### 响应行动作

``` swift
func tableView(UITableView, leadingSwipeActionsConfigurationForRowAt: IndexPath) -> UISwipeActionsConfiguration?
//返回要在行的前沿显示的滑动操作。

func tableView(UITableView, trailingSwipeActionsConfigurationForRowAt: IndexPath) -> UISwipeActionsConfiguration?
//返回要在行的后缘显示的滑动操作。

func tableView(UITableView, shouldShowMenuForRowAt: IndexPath) -> Bool
//询问代理是否应该为某一行显示编辑菜单。 --弃用

func tableView(UITableView, canPerformAction: Selector, forRowAt: IndexPath, withSender: Any?) -> Bool
//询问代理是否编辑菜单应省略给定行的复制或粘贴命令。-- 弃用

func tableView(UITableView, performAction: Selector, forRowAt: IndexPath, withSender: Any?)
//告知委托对给定行的内容执行复制或粘贴操作。-- 弃用

func tableView(UITableView, editActionsForRowAt: IndexPath) -> [UITableViewRowAction]?
//询问代理是否响应指定行中的滑动而显示的操作。
```

### 管理表格视图亮点

``` swift
func tableView(UITableView, shouldHighlightRowAt: IndexPath) -> Bool
//询问代理是否应突出显示指定的行。

func tableView(UITableView, didHighlightRowAt: IndexPath)
//告诉代理指出的行已突出显示。

func tableView(UITableView, didUnhighlightRowAt: IndexPath)
//告诉委托，突出显示已从指定索引路径的行中删除。
```

### 编辑表行

``` swift
func tableView(UITableView, willBeginEditingRowAt: IndexPath)
//告诉代理表视图即将进入编辑模式。

func tableView(UITableView, didEndEditingRowAt: IndexPath?)
//告诉代理表视图已离开编辑模式。

func tableView(UITableView, editingStyleForRowAt: IndexPath) -> UITableViewCell.EditingStyle
//向代表询问表视图中特定位置的行的编辑样式。

func tableView(UITableView, titleForDeleteConfirmationButtonForRowAt: IndexPath) -> String?
//更改删除确认按钮的默认标题。

func tableView(UITableView, shouldIndentWhileEditingRowAt: IndexPath) -> Bool
//询问代理在表视图处于编辑模式时是否应缩进指定行的背景。
```

###  重新排序表行

``` swift
func tableView(UITableView, targetIndexPathForMoveFromRowAt: IndexPath, toProposedIndexPath: IndexPath) -> IndexPath
//要求委托者返回一个新的索引路径以重新定位建议的行移动。
```

### 跟踪删除视图

``` swift
func tableView(UITableView, canFocusRowAt: IndexPath) -> Bool
//询问代理是否指定索引路径的单元格本身是可聚焦的。

func tableView(UITableView, shouldUpdateFocusIn: UITableViewFocusUpdateContext) -> Bool
//询问代理是否允许发生上下文指定的焦点更新。

func tableView(UITableView, didUpdateFocusIn: UITableViewFocusUpdateContext, with: UIFocusAnimationCoordinator)
//告诉委托者刚刚发生了上下文指定的焦点更新。

func indexPathForPreferredFocusedView(in: UITableView) -> IndexPath?
//向委托询问首选焦点视图的表视图的索引路径。
```

# UITableViewDataSource

`UITableViewDataSource`用于管理数据并为表视图提供单元格的对象采用的方法。

`UITableView`仅管理其数据的表示; 他们不管理数据本身。要管理数据，请为表提供数据源对象，即实现协议的对象。数据源对象响应来自表的与数据相关的请求。它还可以直接管理表格的数据，或与应用程序的其他部分协调以管理该数据。

- 指定表中的节和行数。
- 为表的每一行提供单元格。
- 为节标题和页脚提供标题。
- 配置表的索引（如果有）。
- 响应需要更改基础数据的用户或表启动的更新。

**指定行和节的位置**

`UITableView`使用`NSIndexPath`对象的`row`和`section`属性来进行单元格的位置定位。行索引和节索引是从零开始的，所以第一节位于索引0，第二节位于索引1，依此类推。同样，每个节的第一行位于索引0处，这意味着您需要节值和行值来唯一标识行。如果表没有节，则只需要行值。

![](rowandsection.png)

**该协议必须实现两个方法**

``` swift
// Return the number of rows for the table.     
override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
   return 0
}

// Provide a cell object for each row.
override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
   // Fetch a cell of the appropriate type.
   let cell = tableView.dequeueReusableCell(withIdentifier: "cellTypeIdentifier", for: indexPath)
   
   // Configure the cell’s contents.
   cell.textLabel!.text = "Cell text"
       
   return cell
}
```

> 使用此协议的其他方法为表启用特定功能。例如，您必须实现该方法以启用行的滑动到删除功能。[`tableView(_:commit:forRowAt:)`](https://developer.apple.com/documentation/uikit/uitableviewdatasource/1614871-tableview)

## **协议方法**

### 提供行数和节数

``` swift
func tableView(UITableView, numberOfRowsInSection: Int) -> Int
//告诉数据源返回表视图的给定部分中的行数。-- 需要。

func numberOfSections(in: UITableView) -> Int
//要求数据源返回表视图中的节数。
```

### 提供单元格，页眉和页脚

``` swift
func tableView(UITableView, cellForRowAt: IndexPath) -> UITableViewCell
//要求单元格的数据源插入表视图的特定位置。-- 需要。

func tableView(UITableView, titleForHeaderInSection: Int) -> String?
//向数据源询问表视图的指定部分的标题的标题。

func tableView(UITableView, titleForFooterInSection: Int) -> String?
//向数据源询问表视图的指定部分的页脚标题。
```

### 插入或删除表格行

``` swift
func tableView(UITableView, commit: UITableViewCell.EditingStyle, forRowAt: IndexPath)
//要求数据源提交插入或删除接收器中的指定行。

func tableView(UITableView, canEditRowAt: IndexPath) -> Bool
//要求数据源验证给定行是否可编辑。
```

### 重新排序表行

``` swift
func tableView(UITableView, canMoveRowAt: IndexPath) -> Bool
//询问数据源是否可以将给定行移动到表视图中的另一个位置。

func tableView(UITableView, moveRowAt: IndexPath, to: IndexPath)
//告知数据源将表视图中特定位置的行移动到另一个位置。
```

### 配置索引

``` swift
func sectionIndexTitles(for: UITableView) -> [String]?
//要求数据源返回表视图部分的标题。

func tableView(UITableView, sectionForSectionIndexTitle: String, at: Int) -> Int
//要求数据源返回具有给定标题和节标题索引的节的索引。
```

# UITableViewController

一个用于专门管理UITableView的控制器。

当接口由表视图和很少或没有其他内容组成时，子类UITableViewController。表视图控制器已经采用了管理表视图内容和响应更改所需的协议。此外，UITableViewController实现以下行为：

- 它自动加载存档在故事板或NIB文件中的表视图。使用TableView属性访问表视图。
- 它将表视图的数据源和委托设置为self。
- 它实现了`viewWillAppear(_:)`方法，并在第一次出现时自动为其表视图重新加载数据。每次显示表视图时，它都会清除其选择（有动画或无动画，具体取决于请求）；您可以通过更改`ClearSSelectionOnView`将显示属性中的值来禁用此行为。
- 它实现了`viewDidAppear(_:) `，并在表视图第一次出现时自动闪烁滚动指示器。
- 它实现` setEditing(_:animated:) `方法，并在用户点击导航栏中的编辑完成按钮时自动切换表的编辑模式。
- 它会自动调整其表视图的大小，以适应屏幕键盘的外观或消失。

为所管理的每个表视图创建UITableViewController的自定义子类。初始化表视图控制器时，必须指定表视图的样式（普通或分组）。您还必须重写数据源和委托方法，以便用数据填充表。您可以重写`loadView()`或任何其他超类方法，但如果这样做，请确保调用该方法的超类实现，通常作为第一个方法调用。

# UITableView的静态单元格

静态单元格：不会随数据的改变而改变，当在storyboard中创建好后，显示的数据内容和模板样式都固定不变。需要修改，只能在storyboard中修改。

**在storyboard文件中配置静态表：**

1. 将[`UITableViewController`](https://developer.apple.com/documentation/uikit/uitableviewcontroller)对象添加到故事板。
2. 选择表视图控制器的表视图。
3. 将表视图的“内容”属性（在“属性”检查器中）更改为`Static Cells`。
4. 使用表视图的Sections属性指定表的节数。
5. 将每个部分的Row属性设置为所需的行数。
6. 使用所需的视图和内容配置每个单元格。

**注意**：不能通过在UIView中拖拽UITableView的方式来使用静态单元格，需要创建新的UITableViewController，并在属性中将content改为Static Cells。否则会报该错误：

```bash
error: Static table views are only valid when embedded in UITableViewController instances [12]
```

使用静态数据的表视图需要一个[`UITableViewController`](https://developer.apple.com/documentation/uikit/uitableviewcontroller)对象来管理该数据。

**单元格Cell的Accessory属性**

通过设置单元格的Accessory属性来改变单元格右侧的图标样式，该属性名称为accessoryType，它是一个UITableViewCellAccessoryType类型的枚举，枚举量主要有以下几种：

``` objc
UITableViewCellAccessoryNone
  //单元格右侧没有标志
UITableViewCellAccessoryDisclosureIndicator
  //单元格右侧有向右的小箭头标志
UITableViewCellAccessoryDetailDisclosureButton
  //单元格右侧有一个详细信息标志和一个向右小箭头标志
UITableViewCellAccessoryCheckmark
  //单元格右侧有一个对勾标志
UITableViewCellAccessoryDetailButton
  //单元格右侧有一个详细信息标志
```

> 通过cell的`accessoryView`属性来自定义辅助指示视图,优先级比`accessoryType`高
>
> 关于它的使用：如果某个界面从加载完毕后就始终不会变化，则可以使用静态单元格。否则应使用动态单元格。静态单元格是在storyboard中创建的，但是它仍然是可以交互的。

## 案例1：仿一下微信我的

![](wechatme.jpeg)

在storyboard中创建一个UItableViewController，并将TableView的类型设置为`static cells`,并设置节数：

![](staticlizi1.png)

将TableVIew的样式设置为Group：

![](staticlizi6.png)

添加`TableViewCell`：

![](staticlizi2.png)

设置`TableViewCell`的样式，案例设置为Basic即可。

![](staticlizi3.png)

设置标题，图片，配置每节中的行数。设置完后的样式：

![](staticlizi4.png)

也可以配置一下section header的高度

![](staticlizi5.png)

如果需要设置小箭头则可以选择Accessory属性

![](staticlizi8.png)

运行效果：

![](staticlizi7.png)

## 案例2：仿设置

我们通常会在一些app中看到一些设置界面，一般都会使用静态单元格来实现，但是来看下面一个例子，

![](staticlizi22.png)

可以看到推送按钮是经过一些动态变化的，那么如何用静态单元格实现呢，来仿造一下。

在storyboard中，使用UITableViewController进行图中的设置

![](staticlizi23.png)

然后新建一个SettingTableViewController，在storyboard中将controller指向该类，实现代码：

``` swift
import UIKit

class SettingTableViewController: UITableViewController {
    
    @IBOutlet var cell: UITableViewCell!
    
    override func viewDidLoad() {
        super.viewDidLoad()
        let settingswitch = UISwitch(frame: .zero)
        settingswitch.isOn = true
        cell.detailTextLabel?.isHidden = settingswitch.isOn
        settingswitch.addTarget(self, action: #selector(onSwitch(sender:)), for: UIControl.Event.valueChanged)
        cell.accessoryView = settingswitch
        // Uncomment the following line to preserve selection between presentations
        // self.clearsSelectionOnViewWillAppear = false

        // Uncomment the following line to display an Edit button in the navigation bar for this view controller.
        // self.navigationItem.rightBarButtonItem = self.editButtonItem
    }
    
    @objc func onSwitch(sender: UISwitch) {
        cell.detailTextLabel?.isHidden = sender.isOn
        if sender.isOn {
            cell.detailTextLabel?.text = nil
        } else {
            cell.detailTextLabel?.text = "你可能错过重要通知，点击打开"
        }
        cell.layoutSubviews()
//        print(cell.detailTextLabel?.text)
        
    }
}
```

运行结果：

![](staticlizi24.png)

# UITableView的动态单元格

使用表格（**UITableView**）时，我们可以选择其采用静态单元格（**Static Cell**）还是动态单元格（**Dynamic Cell**）。前者使用方便，但是后者更加灵活。上面已经把一些需要用到的协议，类已经介绍了一遍。这里我们用案例来实战一遍。

假如这里我们有一批英雄联盟英雄的数据，存放在项目里，是一个plist文件。

![](dataplist.png)

建立一个模型Hero类

``` swift
public class Hero {
    
    var name: String?
    var icon: String?
    var intro: String?
    var url: String?
    
    public init(dict: [String: AnyObject]) {
        self.name = dict["name"] as? String
        self.icon = dict["icon"] as? String
        self.intro = dict["intro"] as? String
        self.url = dict["url"] as? String
    }

}
```

这里不使用`UITableViewController`，我们使用`UIViewController`来建立一个`UITableViewController`。并实现`UITableViewDelegate`, `UITableViewDataSource`协议。

``` swift
class DymViewController: UIViewController, UITableViewDelegate, UITableViewDataSource
```

新建一个`UITableView`成员变量，并在viewDIdLoad初始化。

``` swift
open var tableview: UITableView!

override func viewDidLoad() {
  super.viewDidLoad()
  //如果不指定tableView的样式，则默认使用UITableViewStylePlain样式。
  self.tableview = UITableView(frame: self.view.frame, style: UITableView.Style.plain);
  self.tableview.delegate = self;
  self.tableview.dataSource = self;
  self.view.addSubview(self.tableview)
}
```

新建一个data类来管理用于展示UITableView的数据类

``` swift
var data: [Hero] {
  let plistdata = NSArray(contentsOfFile: Bundle.main.path(forResource: "heros", ofType: "plist")!)!
  var heros: [Hero] = [Hero]()
  var hero: Hero
  for item in plistdata {
    hero = Hero(dict: item as! [String: AnyObject])
    heros.append(hero)
  }
  return heros
}
```

实现UITableVIewDataSource重要的两个协议方法：

``` swift
func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
  return data.count;
}
    
func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
  //创建样式为Basic的UItableViewCell
  let cell = UITableViewCell(style: UITableViewCell.CellStyle.subtitle, reuseIdentifier: nil)
  cell.imageView?.image = UIImage(named: data[indexPath.row].icon!)
  cell.textLabel?.text = data[indexPath.row].name
  cell.detailTextLabel?.text = data[indexPath.row].intro
  return cell;
}
```

运行效果：

![](dymlizi1.png)

> 性能优化在这里不考虑

**但是英雄也有分类，比如有战士、法师、射手等的分类，我们再修改一下plist的文件。**

![](dymlizi2.png)

再新建一个plist存放归类的译文。

![](dymlizi3.png)

新建一个DymGroupViewController，这里不在原来代码修改。

``` swift
class DymGroupViewController: UIViewController, UITableViewDelegate, UITableViewDataSource
```

新建一个`UITableView`成员变量，并在viewDIdLoad初始化。这里注意，TableView的样式要做改变。

``` swift
open var tableview: UITableView!

override func viewDidLoad() {
  super.viewDidLoad()
  //如果不指定tableView的样式，则默认使用UITableViewStylePlain样式。
  self.tableview = UITableView(frame: self.view.frame, style: UITableView.Style.grouped);
  self.tableview.delegate = self;
  self.tableview.dataSource = self;
  self.view.addSubview(self.tableview)
}
```

这里的数据获取也有很大的改动

``` swift
//分类key数组：例如Support、Tank
var categoryname: [String] {
  let plistdata = NSDictionary(contentsOfFile: Bundle.main.path(forResource: "category", ofType: "plist")!)!
  return plistdata.allKeys as! [String]
}
    
//对应分类key数组的译文：例如Support对应辅助，Tank对应坦克
var categoryvalue: [String] {
  let plistdata = NSDictionary(contentsOfFile: Bundle.main.path(forResource: "category", ofType: "plist")!)!
  return plistdata.allValues as! [String]
}
    
//所有英雄数据的存放，并分类存放了英雄的分类，即用key来存放对应一个英雄的列表数组
var data: [String: Array<Hero>] {
  let plistdata = NSDictionary(contentsOfFile: Bundle.main.path(forResource: "herosgroup", ofType: "plist")!)!
  var rd: [String: Array<Hero>] = [String: Array<Hero>]()
  var heros: [Hero]
  for (items,value) in plistdata {
    heros = [Hero]()
    var hero: Hero
    for item in value as! [[String: AnyObject]] {
      hero = Hero(dict: item)
      heros.append(hero)
    }
    rd[items as! String] = heros
  }
  return rd
}
```

设置好对应的section数和对应section数的行数，并设置section header的标题。

``` swift
//对数据进行分组展示
func numberOfSections(in tableView: UITableView) -> Int {
  return categoryname.count
}

//对应每个分组下的英雄列表数量
func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
  return data[categoryname[section]]!.count
}
    
//列表的展示
func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
  let cell: UITableViewCell = UITableViewCell(style: UITableViewCell.CellStyle.subtitle, reuseIdentifier: nil)
  let hero = data[categoryname[indexPath.section]]![indexPath.row]
  cell.imageView?.image = UIImage(named: hero.icon!)
  cell.textLabel?.text = hero.name
  cell.detailTextLabel?.text = hero.intro
  return cell
}
    
func tableView(_ tableView: UITableView, titleForHeaderInSection section: Int) -> String? {
  return categoryvalue[section]
}
```

运行效果：

![](dymlizi4.png)

到这一步后，我们会发现点击列表没有反应，我们来实现一下跳转到网页，英雄数据里面有一个url属性就是跳转的地址。

``` swift
func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
  print(indexPath.row, indexPath.section)
  let url = URL(string: data[categoryname[indexPath.section]]![indexPath.row].url!)!
  UIApplication.shared.openURL(url)
}
```

跳转后效果：

![](dymlizi5.png)

# 自定义UITableViewCell

## 静态单元格中使用自定义Cell



# 参考资料

https://developer.apple.com/documentation/uikit/uitableview

https://developer.apple.com/documentation/uikit/uitableviewdatasource

https://developer.apple.com/documentation/uikit/uitableviewdelegate

https://developer.apple.com/documentation/uikit/uitableviewcell

https://developer.apple.com/documentation/uikit/views_and_controls/table_views/filling_a_table_with_data

https://developer.apple.com/documentation/uikit/views_and_controls/table_views/configuring_the_cells_for_your_table