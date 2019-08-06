- [Binding](#binding)
- [属性](#%e5%b1%9e%e6%80%a7)
- [事件](#%e4%ba%8b%e4%bb%b6)
- [命令](#%e5%91%bd%e4%bb%a4)
- [资源](#%e8%b5%84%e6%ba%90)
- [模板](#%e6%a8%a1%e6%9d%bf)
- [XAML](#xaml)
- [布局](#%e5%b8%83%e5%b1%80)
- [异步修改UI](#%e5%bc%82%e6%ad%a5%e4%bf%ae%e6%94%b9ui)
- [MVVM](#mvvm)

***
***
# Binding
> - 自定义的类想要实现binding的功能，需要实现 **INotifyPropertyChanged** 接口
> - 数据源：只要是一个对象，并且通过属性( Property )公开自己的数据，就可以作为数据源
> - binding数据源与控件 `this.textboxName.SetBinding(TextBox.TextProperty,new Binding("Name"){Source = stu})`
> - 除了在C#代码中Binding，还可以直接在XAML代码中Binding其他控件的属性。
> - 通常Binding数据的方法有：
>   - 普通CLR类型的单个对象作为Source，包括.NET Framework 自带类型的对象和用具自定义的对象
>   - 普通CLR集合类型对象作为Source，包括数组，List< T >,  ObservableCollection< T >
>   - 将 ADO.NET 数据对象指定为Source， 包括 DataTable 和 DataView等
>   - 使用 XmlDataProvider 把 xml 数据指定为Source
>   - 将依赖（Dependency Object）指定为Source
>   - 将容器的 DataContext 指定为 Source
>   - 通过ElementName 指定 Source
>   - 通过 Binding的 RelativeSource 属性相对地指定 Source
>   - 将 ObjectDataProvider 对象指定为 Source
>   - 将Linq检索得到的数据对象作为 Source
> - 容器的 DataContext 属性是会被子元素继承的属性。


# 属性
> - 依赖属性
>   - 自己没有值，但是能通过使用binding从数据源获取值得属性
>   - 拥有依赖属性的对象称为依赖对象
>   - wpf所有UI控件都是依赖对象
> - 在依赖对象中声明依赖属性需要使用 `public static readonly`
> - 依赖属性已经实现了`INotifyPropertyChanged`接口
> - 附加属性
>   - 附加属性的本质是依赖属性。
>   - 将属性与宿主解耦，让数据类型的设计更加灵活。
> - ??


# 事件
> - CLR事件模型中，分为：
>   - 事件拥有者
>   - 事件
>   - 事件响应者
>   - 事件处理者
>   - 订阅关系
> - CLR事件模型的弊端：
>   - 每队消息是“发送-->相应”关系，必须建立显示的点对点订阅关系。
>   - 事件的宿主必须能够直接访问事件的响应者，不然无法建立订阅关系。
> - WPF路由事件机制：
>   - 事件拥有者和事件响应者没有直接的订阅关系
>   - 事件拥有者只负责激发事件，将事件在visual tree中传播
>   - 事件响应者有事件侦听器，当事件传递到该节点时，其事件处理器就会被调用，并决定是否继续传播事件（使用`RoutedEventArgs.Handled`标记）
> - 可以在cs代码中为控件绑定事件侦听器，也可以在XAML中直接在控件商绑定事件侦听器
> - 路由事件有三种路由策略：
>   - Bubble,冒泡式：路由事件由事件的激发者出发向它的上级容器一层一层路由，直至最外层容器。
>   - Tunnel，隧道式：由Visual Tree的树根向事件激发控件路由。
>   - Direct，直达式：模仿CLR直接事件，直接将事件消息送达事件处理器。
> - 附加事件：在非UIElement派生类中注册的路由事件（`本质上只算是路由事件的一种用法而非一种新概念`）
>   - 无法自己激发路由事件也无法侦听路由事件
>   - 附加事件路由第一站是激发它的元素
>   - 附加事件一般定义在Binding、Mouse、Keyboard这种全局Helper类中。


# 命令
> - 命令与事件的区别：
>   - 命令可以约束消息处理代码
>   - 命令可以约束步骤逻辑
> - 命令系统的要素：
>   - 命令：实现`ICommand`接口，使用最多的是`RoutedCommand`
>   - 命令源：命令发送者，实现`ICommandSource`接口（只包含`Command`、`CommandParameter`、`CommandTarget`三个属性）
>   - 命令目标：实现`IInputElement`接口的类
>   - 命令关联：将外围逻辑与命令关联起来，比如**执行前对命令是否可以执行进行判断，命令执行之后还有哪些后续工作等**
> - 使用自定义命令：
>   - 声明并定义命令
>   - 将命令赋值给命令源（发送者）并**指定快捷键（命令可以方便地指定快捷键）**
>   - 指定命令目标   `Tips:这一步有没有必要？因为在XAML中声明命令是不需要指定命令目标的`
>   - 创建命令关联
>   - 将命令关联安置在外围控件上
> - **`CanExecute`事件的激发频率较高，为了尽量避免降低性能，在处理完后需要将`e.Handled`设置为`true`**
> - **命令关联（CommandBinding**一定要绑定在命令目标的外围控件上，不然无法捕获到`CanExecute`和`Executed`等路由事件  
> - 常用命令库：
>   - `ApplicationCommands`
>   - `ComponentCommands`
>   - `NavigationCommands`
>   - `MediacCommands`
>   - `EditingCommands`
>
>  
> - 命令参数：给命令目标传递参数，可以使同一个命令实现不同的操作。
> - 

# 资源
> - 每个WPF界面元素都有`Resources`属性，这个属性类型为`ResouresDictionary`，使用 **键-值** 对的形式存储资源
> - 静态资源与动态资源
>   - 静态资源（`Static`）在程序载入内存时对资源一次性引用，之后就不再访问这个资源。
>   - 动态资源（`Dynamic`）在程序运行过程中仍然会访问这个资源，在程序运行过程中还有可能修改的资源应该使用`DynamciResource`
> - 在**资源词典**里的资源称为“WPF资源”或者“对象资源”，应用程序内嵌的资源称为“程序集资源”或“二进制资源”
> - 向WPF中添加二进制资源：
>   - 如果添加的是字符串而非文件，则可以使用`Properties`名称空间中的`Resources.resx`资源文件。该文件也用**键-值**对储存数据。
>   - **为了让XAML编译器能够访问`Resources`类，一定要把`Resources.resx`的访问级别由Internal改为Public**
> - 使用`Resources.resx`最大的好处是便于国际化，修改界面中的值直接修改资源即可。

# 模板
> - WPF中控件分为两大类：
>   - `ControlTemplate`是算法内容的表现形式，决定了控件的外观，是控件的外衣
>   - `DataTemplate`是数据内容的表现形式，决定数据的显示样式，是数据的外衣
> - `DataTemplate`完成了数据驱动界面，常用于三处：
>   - ContentControl的ContentTemplate属性，相当于给ContentControl的内容穿外衣。
>   - ItemsControl的ItemTemplate属性，相当于给ItemControl的数据条目穿衣。
>   - GridViewColumn的CellTemplate属性，相当于给GridViewColumn单元格里的数据穿衣服。
> - `ControlTemplate`主要用于两处：
>   - 更换ControlTemplate改变控件外观，使之具有更优的用户使用体验及外观。
>   - 借助ControlTemplate，程序员与设计师并行工作，先使用标准控件编程，设计完成后只需把新的ControlTemplate应用到程序中即可。


# XAML
> - `x:Name`实例化控件对象的引用变量的名称，方便查找相应控件
> - `x:Key`为资源添加用于检索的索引。形成`Key-Value`键值对。
> - LogicalTree：描述了界面的整体框架；WPF大部分特性（属性值继承，事件路由以及样式）都是通过逻辑树进行工作的。
> - VisualTree：描述了界面的所有细节；使用样式可以改变视觉树上的元素，可以使用Style.TargetType属性来选择希望修改的特定元素，甚至当空间属性发生变化时，通过触发器自动完成修改。可以为控件创建新的模板。

# 布局
> - Grid：网格布局，可以自定义行与列的数量，行高，列宽开调整控件布局。类似以Html中的Table，适用于：
>   - UI布局的大框架设计
>   - 大量UI元素需要成行或者成列对齐
>   - UI整体尺寸改变时，元素需要保持固有的高度和宽度比例
>   - UI后期可能有焦大的变更或拓展
> - StackPanel：栈式面板。可将包含的元素在竖直或者水平方向上排成一条直线，移除一个元素后，后排的元素会自动向前移动填补空缺。适用于：
>   - 同类元素需要紧凑排列
>   - 移除其中的元素后能够自动补缺的布局或者动画
> - Canvas：画布。内部元素可以使用以像素为单位的绝对坐标（`Canvas.X`和`Canvas.Y`）进行定位。适用于：
>   - 一经设计基本上不会再有改动的小型布局（如：图标）
>   - 艺术性比较强的布局
>   - 需要大量使用横纵坐标进行绝对定位的布局。
>   - 依赖于横纵坐标的动画
> - DockPanel：泊靠式面板。元素内部可以选择泊靠方向。
>   - 内部元素根据`DockPanel.Dock`属性规定的方向泊靠。
>   - `LastChildFill`属性，默认为True，此时DockPanel内最后一个元素会将内部所有剩余空间充满。
> - WrapPanel：自动折行面板。内部元素在排满一行后能自动折行，类似Html中的流式布局。

# 异步修改UI
> - BackgroundWorker：可以直接使用控件，每一步修改可以直接应用于控件
> - Dispatcher：不可以直接使用控件，每一步修改都需要向Dispatcher队列注册。

# MVVM
> - MVVM = Model-View-ViewModel
> - Model层负责定义数据，获取数据
> - ViewModel层负责逻辑处理，处理Model层的数据，用于View层的数据绑定。
> - View层，即XAML代码，负责UI，与ViewModel层绑定。
> - 从而解耦UI与逻辑，消灭XAML的后台代码。