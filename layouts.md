# 레이아웃

`노트: Blazor는 아직 기술 지원이 제공되지 않는 실험용 웹 프레임워크로, 실무 개발에 사용되어서는 안됩니다.`

Blazor apps typically contain more than one page. Layout elements, such as menus, copyright messages, and logos, must be present on all pages. Copying the code of these layout elements into all of the pages of an app isn't an efficient solution. Such duplication is hard to maintain and probably leads to inconsistent content over time. _Layouts_ solve this problem.

### What are layouts? <a id="what-are-layouts"></a>

Technically, a layout is just another Blazor component. A layout is defined in a Razor template or in C\# code and can contain data binding, dependency injection, and other ordinary features of components. Two additional aspects turn a _component_ into a _layout_:

* The layout component must inherit from [BlazorLayoutComponent](https://blazor.net/api/Microsoft.AspNetCore.Blazor.Layouts.BlazorLayoutComponent.html). `BlazorLayoutComponent` defines a `Body` property that contains the content to be rendered inside the layout.
* The layout component uses the `Body` property to specify where the body content should be rendered using the Razor syntax `@Body`. During rendering, `@Body` is replaced by the content of the layout.

The following code sample shows the Razor template of a layout component. Note the use of `BlazorLayoutComponent` and `@Body`:

```text
@inherits BlazorLayoutComponent

<header>
    <h1>ERP Master 3000</h1>
</header>

<nav>
    <a href="master-data">Master Data Management</a>
    <a href="invoicing">Invoicing</a>
    <a href="accounting">Accounting</a>
</nav>

@Body

<footer>
    &copy; by @CopyrightMessage
</footer>

@functions {
    public string CopyrightMessage { get; set; }
    ...
}
```

### Use a layout in a component <a id="use-a-layout-in-a-component"></a>

Use the Razor directive `@layout` to apply a layout to a component. The compiler converts this directive into a [LayoutAttribute](https://blazor.net/api/Microsoft.AspNetCore.Blazor.Layouts.LayoutAttribute.html), which is applied to the component class.

The following code sample demonstrates the concept. The content of this component is inserted into the _MasterLayout_ at the position of `@Body`:

```text
@layout MasterLayout

@page "/master-data"

<h2>Master Data Management</h2>
...
```

### Centralized layout selection <a id="centralized-layout-selection"></a>

Every folder of a Blazor app can optionally contain a template file named _\_ViewImports.cshtml_. The compiler includes the directives specified in the view imports file in all of the Razor templates in the same folder and recursively in all of its subfolders. Therefore, a _\_ViewImports.cshtml_ file containing `@layout MainLayout` ensures that all of the components in a folder use the _MainLayout_ layout. There's no need to repeatedly add `@layout` to all of the _\*.cshtml_ files.

Note that the default template for Blazor apps uses the _\_ViewImports.cshtml_ mechanism for layout selection. A newly created app contains the _\_ViewImports.cshtml_ file in the _Pages_ folder.

### Nested layouts <a id="nested-layouts"></a>

Blazor apps can consist of nested layouts. A component can reference a layout which in turn references another layout. For example, nesting layouts can be used to reflect a multi-level menu structure.

The following code samples show how to use nested layouts. The _CustomersComponent.cshtml_ file is the component to display. Note that the component references the layout `MasterDataLayout`.

_CustomersComponent.cshtml_:

```text
@layout MasterDataLayout

@page "/master-data/customers"

<h1>Customer Maintenance</h1>
...
```

The _MasterDataLayout.cshtml_ file provides the `MasterDataLayout`. The layout references another layout, `MainLayout`, where it's going to be embedded.

_MasterDataLayout.cshtml_:

```text
@layout MainLayout
@inherits BlazorLayoutComponent

<nav>
    <!-- Menu structure of master data module -->
    ...
</nav>

@Body
```

Finally, `MainLayout` contains the top-level layout elements, such as the header, footer, and main menu.

_MainLayout.cshtml_:

```text
@inherits BlazorLayoutComponent

<header>...</header>
<nav>...</nav>

@Body
```

