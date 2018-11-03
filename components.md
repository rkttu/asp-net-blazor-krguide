# 컴포넌트

`노트: Blazor는 아직 기술 지원이 제공되지 않는 실험용 웹 프레임워크로, 실무 개발에 사용되어서는 안됩니다.`

[View or download sample code](https://github.com/aspnet/Blazor.Docs/tree/master/docs/common/samples/) \([how to download](https://blazor.net/docs/index.html#view-and-download-samples)\). See the [Get started](https://blazor.net/docs/get-started.html) topic for prerequisites.

Blazor apps are built using _components_. A component is a self-contained chunk of user interface \(UI\), such as a page, dialog, or form. A component includes both the HTML markup to render along with the processing logic needed to inject data or respond to UI events. Components are flexible and lightweight, and they can be nested, reused, and shared between projects.

### Component classes <a id="component-classes"></a>

Blazor components are typically implemented in _\*.cshtml_ files using a combination of C\# and HTML markup. The UI for a component is defined using HTML. Dynamic rendering logic \(for example, loops, conditionals, expressions\) is added using an embedded C\# syntax called [Razor](https://docs.microsoft.com/aspnet/core/mvc/views/razor). When a Blazor app is compiled, the HTML markup and C\# rendering logic are converted into a component class. The name of the generated class matches the name of the file.

Members of the component class are defined in a `@functions` block \(more than one `@functions` block is permissible\). In the `@functions` block, component state \(properties, fields\) is specified along with methods for event handling or for defining other component logic.

Component members can then be used as part of the component's rendering logic using C\# expressions that start with `@`. For example, a C\# field is rendered by prefixing `@` to the field name. The following example evaluates and renders:

* `_headingFontStyle` to the CSS property value for `font-style`.
* `_headingText` to the content of the `<h1>` element.

```text
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@functions {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

After the component is initially rendered, the component regenerates its render tree in response to events. Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model \(DOM\).

### Using components <a id="using-components"></a>

Components can include other components by declaring them using HTML element syntax. The markup for using a component looks like an HTML tag where the name of the tag is the component type.

The following markup renders a `HeadingComponent` \(_HeadingComponent.cshtml_\) instance:

```text
<HeadingComponent />
```

### Component parameters <a id="component-parameters"></a>

Components can have _component parameters_, which are defined using _non-public_ properties on the component class decorated with `[Parameter]`. Use attributes to specify arguments for a component in markup.

In the following example, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`:

_ParentComponent.cshtml_:

```text
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent">
    Child content of the child component is supplied by the parent component.
</ChildComponent>
```

_ChildComponent.cshtml_:

```text
<div class="panel panel-success">
    <div class="panel-heading">@Title</div>
    <div class="panel-body">@ChildContent</div>
</div>

@functions {
    [Parameter]
    private string Title { get; set; }

    [Parameter]
    private RenderFragment ChildContent { get; set; }
}
```

### Child content <a id="child-content"></a>

Components can set the content in another component. The assigning component provides the content between the tags that specify the receiving component. For example, a `ParentComponent` can provide content that is to be rendered by a `ChildComponent` by placing the content inside **&lt;ChildComponent&gt;** tags.

_ParentComponent.cshtml_:

```text
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent">
    Child content of the child component is supplied by the parent component.
</ChildComponent>
```

The child component has a `ChildContent` property that represents a [RenderFragment](https://blazor.net/api/Microsoft.AspNetCore.Blazor.RenderFragment.html). The value of `ChildContent` is positioned in the child component's markup where the content should be rendered. In the following example, the value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.

_ChildComponent.cshtml_:

```text
<div class="panel panel-success">
    <div class="panel-heading">@Title</div>
    <div class="panel-body">@ChildContent</div>
</div>

@functions {
    [Parameter]
    private string Title { get; set; }

    [Parameter]
    private RenderFragment ChildContent { get; set; }
}
```

**NOTE**

The property receiving the `RenderFragment` content must be named `ChildContent` by convention.

### Data binding <a id="data-binding"></a>

Data binding to both components and DOM elements is accomplished with the `bind` attribute. The following example binds the `ItalicsCheck` property to the check box's checked state:

```text
<input type="checkbox" class="form-check-input" id="italicsCheck" bind="@_italicsCheck" />
```

When the check box is selected and cleared, the property's value is updated to `true` and `false`, respectively.

The check box is updated in the UI only when the component is rendered, not in response to changing the property's value. Since components render themselves after event handler code executes, property updates are usually reflected in the UI immediately.

Using `bind` with a `CurrentValue` property \(`<input bind="@CurrentValue" />`\) is essentially equivalent to the following:

```text
<input value="@CurrentValue" 
    onchange="@((UIValueEventArgs __e) => CurrentValue = __e.Value)" />
```

When the component is rendered, the `value` of the input element comes from the `CurrentValue` property. When the user types in the text box, the `onchange` is fired and the `CurrentValue` property is set to the changed value. In reality, the code generation is a little more complex because `bind` deals with a few cases where type conversions are performed. In principle, `bind` associates the current value of an expression with a `value` attribute and handles changes using the registered handler.

**Format strings**

Data binding works with [DateTime](https://docs.microsoft.com/dotnet/api/system.datetime) format strings \(but not other format expressions at this time, such as currency or number formats\):

```text
<input bind="@StartDate" format-value="yyyy-MM-dd" />

@functions {
    [Parameter]
    private DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

The `format-value` attribute specifies the date format to apply to the `value` of the `input`element. The format is also used to parse the value when an `onchange` event occurs.

**Component parameters**

Binding also recognizes component parameters, where `bind-{property}` can bind a property value across components.

The following component uses `ChildComponent` and binds the `ParentYear` parameter from the parent to the `Year` parameter on the child component:

Parent component:

```text
@page "/ParentComponent"

<h1>Parent Component</h1>

<p>ParentYear: @ParentYear</p>

<ChildComponent bind-Year="@ParentYear" />

<button class="btn btn-primary" onclick="@ChangeTheYear">Change Year to 1986</button>

@functions {
    [Parameter]
    private int ParentYear { get; set; } = 1978;

    void ChangeTheYear()
    {
        ParentYear = 1986;
    }
}
```

Child component:

```text
<h2>Child Component</h2>

<p>Year: @Year</p>

@functions {
    [Parameter]
    private int Year { get; set; }

    [Parameter]
    private Action<int> YearChanged { get; set; }
}
```

The `Year` parameter is bindable because it has a companion `YearChanged` event that matches the type of the `Year` parameter.

Loading the `ParentComponent` produces the following markup:

```text
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

If the value of the `ParentYear` property is changed by selecting the button in the `ParentComponent`, the `Year` property of the `ChildComponent` is updated. The new value of `Year` is rendered in the UI when the `ParentComponent` is rerendered:

```text
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

### Event handling <a id="event-handling"></a>

Blazor provides event handling features. For an HTML element attribute named `on<event>` \(for example, `onclick`, `onsubmit`\) with a delegate-typed value, Blazor treats the attribute's value as an event handler. The attribute's name always starts with `on`.

The following code calls the `UpdateHeading` method when the button is selected in the UI:

```text
<button class="btn btn-primary" onclick="@UpdateHeading">
    Update heading
</button>

@functions {
    void UpdateHeading(UIMouseEventArgs e)
    {
        ...
    }
}
```

The following code calls the `CheckboxChanged` method when the check box is changed in the UI:

```text
<input type="checkbox" class="form-check-input" onchange="@CheckboxChanged" />

@functions {
    void CheckboxChanged()
    {
        ...
    }
}
```

Event handlers can also be asynchronous and return a `Task`. There's no need to manually call `StateHasChanged()`. Exceptions are logged when they occur.

```text
<button class="btn btn-primary" onclick="@UpdateHeading">
    Update heading
</button>

@functions {
    async Task UpdateHeading(UIMouseEventArgs e)
    {
        ...
    }
}
```

For some events, event-specific event argument types are permitted. If access to one of these event types isn't necessary, it isn't required in the method call.

The list of supported event arguments is:

* UIEventArgs
* UIChangeEventArgs
* UIKeyboardEventArgs
* UIMouseEventArgs

Lambda expressions can also be used:

```text
<button onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

### Capture references to components <a id="capture-references-to-components"></a>

Component references provide a way get a reference to a component instance so that you can issue commands to that instance, such as `Show` or `Reset`. To capture a component reference, add a `ref` attribute to the child component and then define a field with the same name and the same type as the child component.

```text
<MyLoginDialog ref="loginDialog" ... />

@functions {
    MyLoginDialog loginDialog;

    void OnSomething()
    {
        loginDialog.Show();
    }
}
```

When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog`child component instance. You can then invoke .NET methods on the component instance.

**IMPORTANT**

The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element because until then there is nothing to reference. To manipulate components references after the component has finished rendering, use the `OnAfterRenderAsync` or `OnAfterRender` lifecycle methods.

While capturing component references uses a similar syntax to [capturing element references](https://blazor.net/docs/javascript-interop.html#capture-references-to-elements), it isn't a [JavaScript interop](https://blazor.net/docs/javascript-interop.html) feature. Component references aren't passed to JavaScript code; they're only used in .NET code.

**NOTE**

Do **not** use component references to mutate the state of child components. Instead, always use normal declarative parameters to pass data to child components. This causes child components to rerender at the correct times automatically.

### Lifecycle methods <a id="lifecycle-methods"></a>

`OnInitAsync` and `OnInit` execute code after the component has been initialized. To perform an asynchronous operation, use `OnInitAsync` and use the `await` keyword:

```text
protected override async Task OnInitAsync()
{
    await ...
}
```

For a synchronous operation, use `OnInit`:

```text
protected override void OnInit()
{
    ...
}
```

`OnParametersSetAsync` and `OnParametersSet` are called when a component has received parameters from its parent and the values are assigned to properties. These methods are executed after `OnInit` during component initialization.

```text
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

```text
protected override void OnParametersSet()
{
    ...
}
```

`OnAfterRenderAsync` and `OnAfterRender` are called each time after a component has finished rendering. Element and component references are populated at this point. Use this stage to perform additional initialization steps using the rendered content, such as activating third-party JavaScript libraries that operate on the rendered DOM elements.

```text
protected override async Task OnAfterRenderAsync()
{
    await ...
}
```

```text
protected override void OnAfterRender()
{
    ...
}
```

`SetParameters` can be overridden to execute code before parameters are set:

```text
public override void SetParameters(ParameterCollection parameters)
{
    ...

    base.SetParameters(parameters);
}
```

If `base.SetParameters` isn't invoked, the custom code can interpret the incoming parameters value in any way required. For example, the incoming parameters aren't required to be assigned to the properties on the class.

`ShouldRender` can be overridden to suppress refreshing of the UI. If the implementation returns `true`, the UI is refreshed. Even if `ShouldRender` is overridden, the component is always initially rendered.

```text
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

### Component disposal with IDisposable <a id="component-disposal-with-idisposable"></a>

If a component implements [IDisposable](https://docs.microsoft.com/dotnet/api/system.idisposable), the [Dispose method](https://docs.microsoft.com/dotnet/standard/garbage-collection/implementing-dispose) is called when the component is removed from the UI. The following component uses `@implements IDisposable` and the `Dispose` method:

```text
@using System
@implements IDisposable

...

@functions {
    public void Dispose()
    {
        ...
    }
}
```

### Routing <a id="routing"></a>

Routing in Blazor is achieved by providing a route template to each accessible component in the app.

When a _\*.cshtml_ file with an `@page` directive is compiled, the generated class is given a [RouteAttribute](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.routeattribute) specifying the route template. At runtime, the router looks for component classes with a `RouteAttribute` and renders whichever component has a route template that matches the requested URL.

Multiple route templates can be applied to a component. The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:

```text
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

### Route parameters <a id="route-parameters"></a>

Blazor components can receive route parameters from the route template provided in the `@page`directive. The Blazor client-side router uses route parameters to populate the corresponding component parameters.

_RouteParameter.cshtml_:

```text
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>Blazor is @Text!</h1>

@functions {
    [Parameter]
    private string Text { get; set; } = "fantastic";
}
```

Optional parameters aren't supported, so two `@page` directives are applied in the example above. The first permits navigation to the component without a parameter. The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.

### Base class inheritance for a "code-behind" experience <a id="base-class-inheritance-for-a-code-behind-experience"></a>

Blazor component files \(_\*.cshtml_\) mix HTML markup and C\# processing code in the same file. The `@inherits` directive can be used to provide Blazor with a "code-behind" experience that separates component markup from processing code.

The [sample app](https://github.com/aspnet/Blazor.Docs/tree/master/docs/common/samples/) shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.

_BlazorRocks.cshtml_:

```text
@page "/BlazorRocks"
@*
    The inherit directive provides the properties and methods
    of the BlazorRocksBase class to this component.
*@
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

_BlazorRocksBase.cs_:

```text
using Microsoft.AspNetCore.Blazor.Components;

public class BlazorRocksBase : BlazorComponent
{
    public string BlazorRocksText { get; set; } = "Blazor rocks the browser!";
}
```

The base class should derive from [BlazorComponent](https://blazor.net/api/Microsoft.AspNetCore.Blazor.Components.BlazorComponent.html).

### Razor support <a id="razor-support"></a>

**Razor directives**

Razor directives active with Blazor apps are shown in the following table.

| Directive | Description |
| :--- | :--- |
| [@functions](https://docs.microsoft.com/aspnet/core/mvc/views/razor#functions) | Adds a C\# code block to a component. |
| `@implements` | Implements an interface for the generated component class. |
| [@inherits](https://docs.microsoft.com/aspnet/core/mvc/views/razor#inherits) | Provides full control of the class that the component inherits. |
| [@inject](https://docs.microsoft.com/aspnet/core/mvc/views/razor#inject) | Enables service injection from the [service container](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection). For more information, see [Dependency injection into views](https://docs.microsoft.com/aspnet/core/mvc/views/dependency-injection). |
| `@layout` | Specifies a layout component. Layout components are used to avoid code duplication and inconsistency. |
| [@page](https://docs.microsoft.com/aspnet/core/mvc/razor-pages#razor-pages) | Specifies that the component should handle requests directly. The `@page`directive can be specified with a route and optional parameters. Unlike Razor Pages, the `@page` directive doesn't need to be the first directive at the top of the file. For more information, see [Routing](https://blazor.net/docs/routing.html). |
| [@using](https://docs.microsoft.com/aspnet/core/mvc/views/razor#using) | Adds the C\# `using` directive to the generated component class. |
| [@addTagHelper](https://docs.microsoft.com/aspnet/core/mvc/views/razor#tag-helpers) | Use `@addTagHelper` to use a component in a different assembly than the app's assembly. |

**Conditional attributes**

Blazor conditionally renders attributes based on the .NET value. If the value is `false` or `null`, Blazor won't render the attribute. If the value is `true`, the attribute is rendered minimized.

In the following example, `IsCompleted` determines if `checked` is rendered in the control's markup.

```text
<input type="checkbox" checked="@IsCompleted" />

@functions {
    [Parameter]
    private bool IsCompleted { get; set; }
}
```

If `IsCompleted` is `true`, the check box is rendered as:

```text
<input type="checkbox" checked />
```

If `IsCompleted` is `false`, the check box is rendered as:

```text
<input type="checkbox" />
```

**Additional information on Razor**

For more information on Razor, see the [Razor syntax reference](https://docs.microsoft.com/aspnet/core/mvc/views/razor). Note that not all of the features of Razor are available in Blazor at this time.

### Raw HTML <a id="raw-html"></a>

Blazor normally renders strings using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text. To render raw HTML, wrap the HTML content in a `MarkupString` value, which is then parsed as HTML or SVG and inserted into the DOM.

**WARNING**

Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!

The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:

```text
@((MarkupString)myMarkup)

@functions {
    string myMarkup = "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

### Templated components <a id="templated-components"></a>

Templated components are components that accept one or more UI templates as parameters, which can then be used as part of the component's rendering logic. Templated components allow you to author higher-level components that are more reusable than regular components. A couple of examples include:

* A table component that allows a user to specify templates for the table's header, rows, and footer.
* A list component that allows a user to specify a template for rendering items in a list.

#### Template parameters <a id="template-parameters"></a>

A templated component is defined by specifying one or more component parameters of type `RenderFragment` or `RenderFragment<T>`. A render fragment represents a segment of UI that is rendered by the component. A render fragment optionally takes a parameter that can be specified when the render fragment is invoked.

_Components/TableTemplate.cshtml_:

```text
@typeparam TItem

<table class="table">
    <thead>
        <tr>@TableHeader</tr>
    </thead>
    <tbody>
        @foreach (var item in Items)
        {
            <tr>@RowTemplate(item)</tr>
        }
    </tbody>
    <tfoot>
        <tr>@TableFooter</tr>
    </tfoot>
</table>

@functions {
    [Parameter] RenderFragment TableHeader { get; set; }
    [Parameter] RenderFragment<TItem> RowTemplate { get; set; }
    [Parameter] RenderFragment TableFooter { get; set; }
    [Parameter] IReadOnlyList<TItem> Items { get; set; }
}
```

When using a templated component, the template parameters can be specified using child elements that match the names of the parameters \(`TableHeader` and `RowTemplate` in the following example\):

```text
<TableTemplate Items="@pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@context.PetId</td>
        <td>@context.Name</td>
    </RowTemplate>
</TableTemplate>
```

#### Template context parameters <a id="template-context-parameters"></a>

Component arguments of type `RenderFragment<T>` passed as elements have an implicit parameter named `context` \(for example from the preceding code sample, `@context.PetId`\), but you can change the parameter name using the `Context` attribute on the child element. In the following example, the `RowTemplate` element's `Context` attribute specifies the `pet` parameter:

```text
<TableTemplate Items="@pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate Context="pet">
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

Alternatively, you can specify the `Context` attribute on the component element. The specified `Context` attribute applies to all specified template parameters. This can be useful when you want to specify the content parameter name for implicit child content \(without any wrapping child element\). In the following example, the `Context` attribute appears on the `TableTemplate`element and applies to all template parameters:

```text
<TableTemplate Items="@pets" Context="pet">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

#### Generic-typed components <a id="generic-typed-components"></a>

Templated components are often generically typed. For example, a generic ListView component can be used to render `IEnumerable<T>` values. To define a generic component, use the `@typeparam` directive to specify type parameters.

_Components/ListViewTemplate.cshtml_:

```text
@typeparam TItem

<ul>
    @foreach (var item in Items)
    {
        @ItemTemplate(item)
    }
</ul>

@functions {
    [Parameter] RenderFragment<TItem> ItemTemplate { get; set; }
    [Parameter] IReadOnlyList<TItem> Items { get; set; }
}
```

When using generic-typed components, the type parameter is inferred if possible:

```text
<ListViewTemplate Items="@pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

Otherwise, the type parameter must be explicitly specified using an attribute that matches the name of the type parameter. In the following example, `TItem="Pet"` specifies the type:

```text
<ListViewTemplate Items="@pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

### Razor templates <a id="razor-templates"></a>

Render fragments can be defined using Razor template syntax. Razor templates are a way to define a UI snippet and assume the following format:

```text
@<tag>...</tag>
```

The following example illustrates how to specify `RenderFragment` and `RenderFragment<T>`values.

_RazorTemplates.cshtml_:

```text
@{ 
    RenderFragment template = @<p>The time is @DateTime.Now.</p>;
    RenderFragment<Pet> petTemplate = (pet) => @<p>Your pet's name is @pet.Name.</p>;
}
```

Render fragments defined using Razor templates can be passed as arguments to templated components or rendered directly. For example, the previous templates are directly rendered with the following Razor markup:

```text
@template

@petTemplate(new Pet { Name = "Rex" })
```

Rendered output:

```text
The time is 10/04/2018 01:26:52.

Your pet's name is Rex.
```

