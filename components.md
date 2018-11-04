# 컴포넌트

`노트: Blazor는 아직 기술 지원이 제공되지 않는 실험용 웹 프레임워크로, 실무 개발에 사용되어서는 안됩니다.`

[샘플 코드를 보거나 다운로드하려면 여기를 클릭하세요.](https://github.com/aspnet/Blazor.Docs/tree/master/docs/common/samples/) \([다운로드하는 방법](https://blazor.net/docs/index.html#view-and-download-samples)\) [Blazor 시작하기](../get-started.md) 문서를 참고하여 미리 준비해야 할 내용을 확인하세요.

Blazor 앱은 _컴포넌트_ 를 사용하여 만들어집니다. 컴포넌트는 페이지, 대화 상자 또는 양식과 같이 그 자체로 하나의 사용자 인터페이스 \(UI\) 조각입니다. 컴포넌트에는 화면에 그릴 HTML 마크업과, 데이터를 주입하거나 UI 이벤트에 응답하기 위한 처리 로직을 모두 포함하고 있습니다. 컴포넌트는 유연하며 매우 가볍고, 중첩되거나, 재사용되거나, 여러 프로젝트에 걸쳐 공유할 수 있습니다.

### 컴포넌트 클래스 <a id="component-classes"></a>

Blazor 컴포넌트는 보통 _|*.cshtml_ 파일 안에 C\#과 HTML 마크업을 조합하여 구현합니다. 컴포넌트의 UI는 HTML을 사용하여 정의됩니다. 동적 렌더링 로직 \(예를 들어, 반복문, 조건문, 표현식\)은 [Razor](https://docs.microsoft.com/aspnet/core/mvc/views/razor) 라는 내장된 C\# 문법을 사용하여 추가합니다. Blazor 앱이 컴파일되면, HTML 마크업과 C\# 렌더링 로직이 컴포넌트 클래스로 변환됩니다. 만들어지는 클래스의 이름은 파일의 이름과 같은 이름을 사용합니다.

컴포넌트 클래스의 멤버들은 `@functions` 블록에 정의됩니다. \(`@functions` 블록을 여러번 쓰는 것도 가능합니다.\) `@functions` 블록 안에서 컴포넌트의 상태 \(프로퍼티, 필드\)가 이벤트 처리를 위한 메서드나 다른 컴포넌트 로직을 위하여 추가될 수 있습니다.

그리고 컴포넌트 멤버는 `@` 으로 시작하는 C\# 표현식을 사용하는 컴포넌트의 렌더링 로직의 일부로서 사용할 수 있습니다. 예를 들어, 필드의 값을 렌더링하기 위하여 C\# 필드 이름 앞에 `@` 접두사를 붙여 사용할 수 있습니다. 다음과 같이 사용하면 값이 평가되고 렌더링됩니다.

* CSS의 `font-style` 프로퍼티에 지정할 값으로 `_headingFontStyle` 변수 사용
* `<h1>` 요소의 내용으로 `_headingText` 변수 사용

```text
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@functions {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

컴포넌트가 처음 렌더링되면, 컴포넌트는 이벤트에 반응하여 렌더 트리를 다시 생성합니다. Blazor는 그 다음 새로 만들어진 트리와 기존의 트리를 비교하여 브라우저의 문서 개체 모델 \(DOM\)에 반영해야 할 변경 사항들을 적용합니다.

### 컴포넌트 사용하기 <a id="using-components"></a>

컴포넌트는 HTML 요소 문법을 사용하여 다른 컴포넌트를 포함할 수 있습니다. 컴포넌트를 사용하기 위한 마크업 문법은 HTML 태그처럼 보이지만 태그의 이름은 컴포넌트 형식의 이름을 사용합니다.

아래의 마크업은 `HeadingComponent` \(_HeadingComponent.cshtml_\) 인스턴스를 렌더링합니다:

```text
<HeadingComponent />
```

### 컴포넌트 매개 변수 <a id="component-parameters"></a>

컴포넌트는 `[Parameter]` 어트리뷰트가 적용된 컴포넌트 클래스 상의 _비 공개_ 프로퍼티로 정의되는 _컴포넌트 매개 변수_ 라는 것을 가질 수 있습니다. 이 어트리뷰트를 사용하여 컴포넌트 마크업에서 인자를 지정할 수 있게 합니다.

다음의 예제에서는, `ParentComponent`는 `ChildComponent`의 `Title` 프로퍼티에 값을 설정합니다.

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

### 자식 콘텐츠 <a id="child-content"></a>

컴포넌트는 다른 컴포넌트의 내용을 설정할 수 있습니다. 할당하는 컴포넌트는 내용을 받는 컴포넌트를 가리키는 태그 사이에 들어갈 내용을 지정할 수 있습니다. 예를 들어, `ParentComponent` 는 `ChildComponent` 가 렌더링할 내용을 **&lt;ChildComponent&gt;** 태그 사이에 지정할 수 있습니다.

_ParentComponent.cshtml_:

```text
@page "/ParentComponent"

<h1>Parent-child example</h1>

<ChildComponent Title="Panel Title from Parent">
    Child content of the child component is supplied by the parent component.
</ChildComponent>
```

자식 컴포넌트는 [RenderFragment](https://blazor.net/api/Microsoft.AspNetCore.Blazor.RenderFragment.html)로 표현되는 `ChildContent` 프로퍼티를 가지고 있습니다. `ChildContent`의 내용은 자식 컴포넌트의 마크업에 들어있으며, 이 내용을 렌더링해야 합니다. 다음의 예제에서는, `ChildContent`의 내용을 부모 컴포넌트로부터 받아서, Bootstrap 패널의 `panel-body` 안에 렌더링합니다.

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

**노트**

`RenderFragment` 콘텐츠를 받는 프로퍼티의 이름은 관례상 반드시 `ChildContent`로 지정해야 합니다.

### 데이터 바인딩 <a id="data-binding"></a>

데이터 바인딩을 컴포넌트와 DOM 요소에 적용하기 위하여 `bind` 어트리뷰트를 사용할 수 있습니다. 다음의 예제는 `ItalicsCheck` 프로퍼티를 체크 박스의 체크 상태에 바인딩합니다.

```text
<input type="checkbox" class="form-check-input" id="italicsCheck" bind="@_italicsCheck" />
```

체크 박스가 선택되거나 선택되지 않았을 때, 프로퍼티의 값은 각각 `true`와 `false`로 수정됩니다.

체크 박스는 프로퍼티의 값 변경이 일어날 때가 아니라 컴포넌트가 렌더링 될 때만 UI에서 업데이트됩니다. 이벤트 처리기 코드가 실행 된 후에 컴포넌트가 렌더링되므로 대개 프로퍼티 업데이트는 UI에 즉시 반영됩니다.

`bind` 어트리뷰트를 `CurrentValue` 프로퍼티 \(`<input bind="@CurrentValue" />`\)에 사용하면 기본적으로 다음과 같은 의미가 됩니다:

```text
<input value="@CurrentValue" 
    onchange="@((UIValueEventArgs __e) => CurrentValue = __e.Value)" />
```

컴포넌트가 렌더링될 때, 텍스트 박스의 `value` 어트리뷰트의 값은 `CurrentValue` 프로퍼티로부터 옵니다. 사용자가 텍스트 박스 안에 글자를 입력하면, `onchange` 이벤트가 호출되어 `CurrentValue` 프로퍼티의 값이 바뀐 값으로 변경됩니다. 실제로 만들어지는 코드는 이보다 좀 더 복잡한데, `bind` 어트리뷰트가 몇몇 형식 변환을 처리하기 때문입니다. 원칙적으로, `bind` 어트리뷰트는 현재 표현식의 값을 `value` 어트리뷰트에 연관시키고, 등록된 처리기를 사용하여 변화를 관리합니다.

**문자열 서식**

[DateTime](https://docs.microsoft.com/dotnet/api/system.datetime)과 함께 데이터 바인딩을 하면 문자열로 형식화할 수 있습니다: \(단, 통화나 숫자 형식과 같은 형식은 현재 지원되지 않습니다.\)

```text
<input bind="@StartDate" format-value="yyyy-MM-dd" />

@functions {
    [Parameter]
    private DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

`format-value` 어트리뷰트에는 `input` 요소의 `value` 어트리뷰트에 적용할 날짜 서식을 지정합니다. 서식은 또한 `onchange` 이벤트가 발생하였을 때 값을 해석하기 위해서도 사용됩니다.

**컴포넌트 매개 변수**

Binding은 또한 `bind-{property}`와 같이 사용하여, 컴포넌트에 걸쳐 프로퍼티 값을 바인딩 할 수 있는 컴포넌트 매개 변수를 인식합니다.

다음 컴포넌트는 `ChildComponent`라는 자식 컴포넌트를 사용하며, 부모 컴포넌트의 `ParentYear` 매개 변수를 자식 컴포넌트의 `Year` 매개 변수에 바인딩합니다.

부모 컴포넌트:

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

자식 컴포넌트:

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

`Year` 매개 변수는 `Year` 매개 변수의 형식과 일치하는 `YearChanged` 라는 보조 이벤트를 가지고 있기 때문에 바인딩을 지원합니다.

`ParentComponent`를 불러들이면 다음과 같은 마크업이 만들어지게 됩니다:

```text
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

만약 `ParentYear` 프로퍼티가 `ParentComponent` 안의 버튼을 클릭함으로서 값이 변경되면, `ChildComponent`의 `Year` 프로퍼티가 수정됩니다. `ParentComponent`가 렌더링될 때 새로운 값을 가진 `Year` 프로퍼티가 UI에 렌더링됩니다.

```text
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

### 이벤트 처리 <a id="event-handling"></a>

Blazor는 이벤트 처리 기능을 제공합니다. HTML 요소의 어트리뷰트 중 `on<event>` \(예를 들어, `onclick`, `onsubmit` 같은\) 어트리뷰트에 대리자 형식의 값을 지정하여, Blazor가 어트리뷰트의 값을 이벤트 처리기로 취급합니다. 이런 어트리뷰트의 이름은 항상 `on`으로 시작합니다.

아래의 코드는 UI에서 버튼이 눌릴 때 마다 `UpdateHeading` 메서드를 호출합니다:

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

아래의 코드는 UI에서 체크 박스의 선택 상태가 바뀔 때 마다 `CheckboxChanged` 메서드를 호출합니다:

```text
<input type="checkbox" class="form-check-input" onchange="@CheckboxChanged" />

@functions {
    void CheckboxChanged()
    {
        ...
    }
}
```

이벤트 처리기는 또한 비동기 메서드로 정의될 수 있고, `Task` 를 반환할 수 있습니다. `StateHasChanged()` 같은 메서드를 호출할 필요가 없습니다. 예외가 발생할 경우 자동으로 기록됩니다.

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

몇몇 이벤트의 경우, 특정 이벤트에 대한 상세 정보를 담은 인자 형식이 제공됩니다. 만약 이들 이벤트 형식을 이용할 필요가 없다면, 메서드 호출을 할 때에도 사용하지 않고 생략할 수 있습니다.

지원되는 이벤트 인자 형식들은 다음과 같습니다:

* UIEventArgs
* UIChangeEventArgs
* UIKeyboardEventArgs
* UIMouseEventArgs

람다 표현식도 사용할 수 있습니다:

```text
<button onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

### 컴포넌트의 참조 가져오기 <a id="capture-references-to-components"></a>

컴포넌트 참조를 통하여 컴포넌트 인스턴스에 대한 참조를 얻어 인스턴스에 대해 `Show`나 `Reset` 같은 명령을 내릴 수 있습니다. 컴포넌트의 참조를 얻으려면, 자식 컴포넌트에 `ref` 어트리뷰트를 추가하고, 자식 컴포넌트와 동일한 타입을 가진, `ref` 어트리뷰트에 지정했던 이름과 같은 이름의 필드를 정의합니다.

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

컴포넌트가 렌더링되면, `loginDialog` 필드가 `MyLoginDialog` 자식 컴포넌트 인스턴스의 참조를 가리키게 됩니다. 이제 컴포넌트 인스턴스에 대해 .NET 메서드를 호출할 수 있습니다.

**중요**

`loginDialog` 변수는 컴포넌트가 렌더링 된 후에만 참조가 할당되고, 그 결과는 참조 할 것이 없기 때문에 `MyLoginDialog` 요소를 포함합니다. 컴포넌트의 렌더링이 끝난 후 컴포넌트의 참조를 다루기 위해서는, `OnAfterRenderAsync` 또는 `OnAfterRender` 라이프사이클 메서드를 사용해야 합니다.

구성 요소 참조를 캡처하는 동안 [요소 참조 캡처] (https://blazor.net/docs/javascript-interop.html#capture-references-to-elements)와 유사한 구문을 사용하지만, 이것은 [JavaScript 연동](https://blazor.net/docs/javascript-interop.html) 기능을 사용하는 것이 아닙니다. 구성 요소 참조는 JavaScript 코드로 전달되지 않습니다. .NET 코드에서만 사용됩니다.

**노트**

컴포넌트 참조를 자식 컴포넌트의 상태를 바꾸기 위해서 **사용하면 안됩니다.** 대신, 항상 보통의 선언적 매개 변수를 사용하여 자식 컴포넌트로 데이터를 전달하도록 합니다. 이렇게 하면 렌더러에 의하여 자식 컴포넌트가 정확한 시점에 렌더링됩니다.

### 라이프사이클 메서드 <a id="lifecycle-methods"></a>

`OnInitAsync`과 `OnInit` 메서드는 컴포넌트가 초기화된 이후에 호출되는 메서드입니다. 비동기 작업을 수행하기 위해서는, `OnInitAsync`와 `await` 키워드를 다음과 같이 사용합니다:

```text
protected override async Task OnInitAsync()
{
    await ...
}
```

동기 방식으로 작업을 수행하기 위해서는, `OnInit`을 다음과 같이 사용합니다:

```text
protected override void OnInit()
{
    ...
}
```

`OnParametersSetAsync`와 `OnParametersSet` 메서드는 컴포넌트가 부모 컴포넌트로부터 매개 변수의 값을 받았고, 그 값이 프로퍼티에 설정되었을 때 실행됩니다. 이들 메서드는 컴포넌트 초기화 중에 `OnInit` 메서드 다음에 실행됩니다.

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

`OnAfterRenderAsync`와 `OnAfterRender` 메서드는 컴포넌트의 렌더링이 끝날 때 마다 호출됩니다. 요소와 컴포넌트 참조가 이 시점부터 사용 가능해집니다. 이 단계에서 렌더링된 콘텐츠에 대한 추가 작업을 진행할 수 있는데, 예를 들면 렌더링된 DOM 요소에 대해 서드파티 자바스크립트 라이브러리를 호출할 수 있습니다.

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

`SetParameters`는 매개 변수들이 설정되기 전에 호출될 내용으로 재정의할 수 있습니다:

```text
public override void SetParameters(ParameterCollection parameters)
{
    ...

    base.SetParameters(parameters);
}
```

만약 `base.SetParameters`가 호출되지 않으면, 커스텀 코드는 필요한 모든 방식으로 들어오는 매개 변수 값을 해석 할 수 있습니다. 예를 들어 들어오는 매개 변수를 클래스의 프로퍼티에 할당 할 필요는 없습니다.

`ShouldRender`는 UI가 새로 고쳐지는 것을 방지하기 위해 재정의할 수 있습니다. 메서드에서 `true`를 반환하면 UI가 새로 고쳐집니다. `ShouldRender`가 재정의 되었더라도 항상 최초 한 번 컴포넌트가 렌더링됩니다.

```text
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

### IDisposable로 컴포넌트 할당 해제하기 <a id="component-disposal-with-idisposable"></a>

만약 컴포넌트가 [IDisposable](https://docs.microsoft.com/dotnet/api/system.idisposable) 인터페이스를 구현했다면, UI에서 컴포넌트가 제거될 때에 [Dispose 메서드](https://docs.microsoft.com/dotnet/standard/garbage-collection/implementing-dispose)가 호출됩니다. 아래 컴포넌트는 `@implements IDisposable` 지시자와 `Dispose` 메서드를 사용하는 예시입니다:

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

### 라우팅 <a id="routing"></a>

Blazor에서 라우팅은 앱 안에서 접근 가능한 각각의 컴포넌트마다 라우팅 템플릿을 지정하는 것으로 달성할 수 있습니다.

_\*.cshtml_ 파일을 `@page` 지시자와 함께 컴파일하면, 만들어지는 클래스에 [RouteAttribute](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.mvc.routeattribute)가 추가됩니다. 런타임에서는, 라우터가 `RouteAttribute`가 첨부된 컴포넌트 클래스들을 찾고, 요청한 URL과 일치하는 라우팅 템플릿을 가진 컴포넌트를 렌더링하게 됩니다.

여러 개의 라우팅 템플릿을 컴포넌트에 적용할 수 있습니다. 아래의 컴포넌트는 `/Blazorroute`와 `/DifferentBlazorRoute` 경로에 대한 요청이 있을 때 작동하게 됩니다:

```text
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

### 라우팅 매개 변수 <a id="route-parameters"></a>

Blazor 컴포넌트는 라우팅 매개 변수를 `@page` 지시자로 지정한 라우팅 템플릿으로부터 받을 수 있습니다. Blazor의 클라이언트 측 라우터는 라우팅 매개 변수를 사용하여 적절한 컴포넌트 매개 변수를 획득합니다.

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

매개 변수를 생략하는 것은 지원되지 않으며, 그래서 2개의 `@page` 지시자를 예제에서 지정한 것입니다. 처음 사용한 지시자는 매개 변수를 지정하지 않았을 때에도 컴포넌트에 대한 탐색을 허용하기 위함이며, 두 번째 `@page` 지시자는 `{text}` 라우팅 파라미터를 받아, 컴포넌트의 `Text` 프로퍼티에 값을 할당하기 위하여 사용되었습니다.

### "코드 비하인드" 방식으로 기본 클래스 상속하기 <a id="base-class-inheritance-for-a-code-behind-experience"></a>

Blazor 컴포넌트 파일 \(_\*.cshtml_\)은 HTML 마크업와 C\# 코드를 하나의 파일 안에 담고 있습니다. `@inherits` 지시자는 Blazor에서 컴포넌트 마크업와 코드를 분리하는 "코드 비하인드" 방식의 프로그래밍 기법을 사용할 수 있게 해줍니다.

이 [예제 앱](https://github.com/aspnet/Blazor.Docs/tree/master/docs/common/samples/)에서는 컴포넌트가 어떻게 `BlazorRocksBase` 라는 클래스를 상속받아서 컴포넌트의 프로퍼티과 메서드를 제공하는지 방법을 보여줍니다.

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

상속의 대상이 되는 기본 클래스는 반드시 [BlazorComponent](https://blazor.net/api/Microsoft.AspNetCore.Blazor.Components.BlazorComponent.html) 클래스에서 상속받아야 합니다.

### Razor 지원 <a id="razor-support"></a>

**Razor 지시자**

아래 표에 나와있는 Razor 지시자들을 Blazor 앱에서 사용할 수 있습니다.

| 지시자 | 설명 |
| :--- | :--- |
| [@functions](https://docs.microsoft.com/aspnet/core/mvc/views/razor#functions) | C\# 코드 블록을 컴포넌트에 추가합니다. |
| `@implements` | 만들어지는 컴포넌트 클래스가 구현할 인터페이스를 지정합니다. |
| [@inherits](https://docs.microsoft.com/aspnet/core/mvc/views/razor#inherits) | 구성 요소가 상속하는 클래스를 완벽하게 제어합니다. |
| [@inject](https://docs.microsoft.com/aspnet/core/mvc/views/razor#inject) | [서비스 컨테이너](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)로 부터 서비스 주입이 가능하게 합니다. 자세한 내용은, [뷰에 의존성 주입하기](https://docs.microsoft.com/aspnet/core/mvc/views/dependency-injection) 문서를 참고하세요. |
| `@layout` | 레이아웃 컴포넌트를 지정합니다. 레이아웃 컴포넌트는 코드 중복과 비 일관성을 방지하기 위하여 사용합니다. |
| [@page](https://docs.microsoft.com/aspnet/core/mvc/razor-pages#razor-pages) | 컴포넌트가 요청을 직접 처리해야 할 지의 여부를 지정합니다. `@page` 지시자는 라우팅 경로와 선택적 매개 변수와 함께 지정할 수 있습니다. Razor 페이지와는 달리, `@page` 지시자는 파일의 맨 처음이 아닌 곳에 등장해도 됩니다. 자세한 내용은, [라우팅](https://blazor.net/docs/routing.html) 문서를 참고하세요. |
| [@using](https://docs.microsoft.com/aspnet/core/mvc/views/razor#using) | C\#의 `using` 지시자를 생성되는 컴포넌트 클래스 코드에 추가합니다. |
| [@addTagHelper](https://docs.microsoft.com/aspnet/core/mvc/views/razor#tag-helpers) | `@addTagHelper` 지시자를 사용하여 앱의 어셈블리가 아닌 다른 어셈블리의 컴포넌트를 사용할 수 있습니다. |

**조건부 어트리뷰트**

Blazor는 .NET의 값을 기초로 어트리뷰트의 렌더링 여부를 선택적으로 처리할 수 있습니다. 만약 값이 `false` 또는 `null`인 경우, Blazor는 어트리뷰트 렌더링을 생략합니다. 만약 값이 `true`이면, 프로퍼티는 값이 지정되지 않은 상태로 렌더링됩니다.

다음 예제에서, `IsCompleted`는 `checked` 어트리뷰트가 컨트롤의 마크업에 렌더링될 지 여부를 결정합니다.

```text
<input type="checkbox" checked="@IsCompleted" />

@functions {
    [Parameter]
    private bool IsCompleted { get; set; }
}
```

만약 `IsCompleted`가 `true`이면, 체크박스는 다음과 같이 렌더링됩니다:

```text
<input type="checkbox" checked />
```

만약 `IsCompleted`가 `false`이면, 체크박스는 다음과 같이 렌더링됩니다:

```text
<input type="checkbox" />
```

**Razor에 대한 더 자세한 내용**

Razor에 대한 더 자세한 내용을 알고 싶다면, [Razor 문법 레퍼런스](https://docs.microsoft.com/aspnet/core/mvc/views/razor) 문서를 참고하세요. 참고할 것은, 이 문서에서 설명하는 Razor의 기능들 중 Blazor에서 아직 지원하지 않는 것이 있을 수 있습니다.

### HTML 직접 사용 <a id="raw-html"></a>

Blazor는 보통 DOM의 텍스트 노드에 들어갈 텍스트를 렌더링하는데, 이는 텍스트에 마크업이 사용되더라도 마크업이 아닌 일반 텍스트로 취급됨을 의미합니다. HTML 그 자체를 렌더링하기 위해서는, HTML 콘텐츠를 `MarkupString` 형식의 값으로 포장하여, DOM에 HTML이나 SVG 그 자체로 해석하여 추가되도록 해야 합니다.

**경고**

신뢰할 수 없는 출처에서 만들어진 HTML을 직접 추가하는 것은 **보안 위험 요소**가 될 수 있으며, 반드시 피해야 합니다!

다음의 예제에서는 `MarkupString` 형식을 사용하여 컴포넌트의 출력 결과에 정적 HTML 콘텐츠 블록을 추가하는 방법을 보여줍니다:

```text
@((MarkupString)myMarkup)

@functions {
    string myMarkup = "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

### 템플릿 컴포넌트 <a id="templated-components"></a>

템플릿 컴포넌트는 하나 이상의 UI 템플릿을 매개 변수로서 받아들이는 컴포넌트이며, 컴포넌트의 렌더링 로직의 일부로서 사용할 수 있습니다. 템플릿 컴포넌트는 보통의 컴포넌트보다 더 재사용성이 좋은 높은 수준의 컴포넌트를 만들 수 있게 해줍니다. 다음은 몇 가지 예시입니다:

* 테이블의 머리글, 본문, 바닥글을 커스터마이징할 수 있는 테이블 컴포넌트.
* 사용자가 리스트의 렌더링 항목을 직접 지정할 수 있는 리스트 컴포넌트.

#### 템플릿 매개 변수 <a id="template-parameters"></a>

템플릿 컴포넌트는 하나 이상의 `RenderFragment`나 `RenderFragment<T>` 타입의 매개 변수를 지정할 수 있도록 정의된 컴포넌트입니다. 렌더링 조각은 컴포넌트에 의하여 렌더링될 UI 조각을 나타냅니다. 렌더링 조각은 선택적으로 렌더링 조각이 호출될 때 지정할 수 있는 매개 변수를 받습니다.

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

템플릿 구성 요소를 사용하는 경우 템플릿 매개 변수는 매개 변수 \(아래 예제의 `TableHeader`  및 `RowTemplate`\)의 이름과 일치하는 하위 요소를 사용하여 지정할 수 있습니다.

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

#### 템플릿 컨텍스트 매개 변수 <a id="template-context-parameters"></a>

요소로 전달된 `RenderFragment<T>` 형식의 컴포넌트 매개 변수는 `context` 라는 이름의 암시적 매개 변수로 사용할 수 있지만 \(앞의 코드 샘플의 예시에서는 `@context.PetId` 였습니다.\), 자식 요소의 `Context` 어트리뷰트을 사용하여 매개 변수 이름을 바꿀 수 있습니다. 다음 예제에서 `RowTemplate` 요소의 `Context` 어트리뷰트에서 `pet`으로 매개 변수의 이름을 지정하는 방법을 보여줍니다.

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

또는 `Context` 어트리뷰트를 컴포넌트 요소에 지정할 수도 있습니다. `Context` 어트리뷰트에 지정한 이름은 모든 템플릿 파라미터에 영향을 줍니다. 이 방법은 암시적으로 하위 콘텐츠에 대한 매개 변수 이름을 지정하려는 경우에 유용할 수 있습니다. \(하위 요소를 감싸거나 할 필요가 없습니다.\) 다음 예제에서는, `Context` 어트리뷰트가 `TableTemplate` 요소에 나타나고, 모든 템플릿 파라미터의 이름이 지정된 이름으로 변경됩니다:

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

#### 제네릭 형식의 컴포넌트 <a id="generic-typed-components"></a>

템플릿화된 컴포넌트는 보통 제네릭 형식으로 만들어집니다. 예를 들어, 제네릭 ListView 컴포넌트는 `IEnumerable<T>` 의 값을 렌더링하기 위하여 사용될 수 있습니다. 제네릭 컴포넌트를 정의하기 위해서는, `@typeparam` 지시자를 매개 변수에 지정해야 합니다.

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

제네릭 형식의 컴포넌트가 사용될 때에는, 형식 매개 변수를 최대한 유추하려고 시도합니다:

```text
<ListViewTemplate Items="@pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

그렇지 않으면, 형식 매개 변수의 이름과 같은 어트리뷰트에 형식 이름을 반드시 지정해야 합니다. 다음의 예시에서는, `TItem="Pet"`으로 형식을 지정하고 있습니다:

```text
<ListViewTemplate Items="@pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

### Razor 템플릿 <a id="razor-templates"></a>

렌더링 조각은 Razor 템플릿 문법을 사용하여 정의할 수 있습니다. Razor 템플릿은 UI 조각을 정의하기 위한 하나의 방법이고, 다음과 같은 형식을 따릅니다:

```text
@<tag>...</tag>
```

다음의 예제에서는 어떻게 `RenderFragment`와 `RenderFragment<T>`의 값을 지정하는지 보여줍니다.

_RazorTemplates.cshtml_:

```text
@{ 
    RenderFragment template = @<p>The time is @DateTime.Now.</p>;
    RenderFragment<Pet> petTemplate = (pet) => @<p>Your pet's name is @pet.Name.</p>;
}
```

Razor 템플릿을 사용하여 정의되는 렌더링 조각은 템플릿화된 컴포넌트의 인자로 전달되거나 그 자체로 렌더링될 수 있습니다. 예를 들어, 앞의 템플릿은 다음과 같은 Razor 마크업으로 직접 렌더링될 수 있습니다:

```text
@template

@petTemplate(new Pet { Name = "Rex" })
```

렌더링된 결과:

```text
The time is 10/04/2018 01:26:52.

Your pet's name is Rex.
```

