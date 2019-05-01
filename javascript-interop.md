# 자바스크립트 연동

`노트: Blazor는 아직 기술 지원이 제공되지 않는 실험용 웹 프레임워크로, 실무 개발에 사용되어서는 안됩니다.`

Blazor 앱에서는 .NET에서 자바스크립트 함수를 호출하고 자바스크립트 코드에서 .NET 메서드를 호출할 수 있습니다.

### .NET 메서드에서 자바스크립트 함수 호출 <a id="invoke-javascript-functions-from-net-methods"></a>

Blazor .NET 코드에서 JavaScript 함수를 호출해야하는 경우가 있습니다. 예를 들어, 자바스크립트 호출(call)을 통해 브라우저 기능을 노출하거나 자바스크립트 라이브러리의 기능을 Blazor 앱으로 노출시킬 수 있습니다.

.NET에서 자바스크립트를 호출하려면 `JSRuntime.Current`에서 액세스할 수 있는 `IJSRuntime` 추상화를 사용하십시오. `IJSRuntime`의 `InvokeAsync <T>`메소드는 여러 개의 JSON 직렬화가능(serializable) 인수와 함께 호출하고자하는 자바스크립트 함수의 식별자 갖습니다. 함수 식별자는 전역 범위 \ (`window` \)에 따라 달라집니다. `window.someScope.someFunction`을 호출하려면 식별자는 someScope.someFunction이 됩니다. 함수가 호출되기 전에는 함수를 등록할 필요가 없습니다. 또한 반환 유형 'T'는 JSON을 직렬화 할 수 있어야합니다.

셈플 앱에서 다음 두 가지의 자바스크립트 함수를 클라이언트 측 앱에 사용하여 사용자의 입력을 받고 환영 인사말을 표시하기 위해 DOM과 상호작용도록 합니다.

* `showPrompt` – 사용자의 입력\(사용자의 이름\)을 받기위한 프롬프트를 생성하고 호출자에게 해당 이름을 반환합니다.
* `displayWelcome` – 호출자의 환영 인사말을 `id`가 `welcome`인 DOM 객체에 할당합니다.

_wwwroot/exampleJsInterop.js_:

```text
window.exampleJsFunctions = {
  showPrompt: function (text) {
    return prompt(text, 'Type your name here');
  },
  displayWelcome: function (welcomeMessage) {
    document.getElementById('welcome').innerText = welcomeMessage;
  },
    returnArrayAsyncJs: function () {
      DotNet.invokeMethodAsync('BlazorSample', 'ReturnArrayAsync')
        .then(data => {
          data.push(4);
            console.log(data);
    })
  },
  sayHello: function (dotnetHelper) {
    return dotnetHelper.invokeMethodAsync('SayHello')
      .then(r => console.log(r));
  }
};
```

Place the `<script>` tag that references the JavaScript file in the _wwwroot/index.html_ file:

```text
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width">
    <title>BlazorSample</title>
    <base href="/" />
    <link href="css/bootstrap/bootstrap.min.css" rel="stylesheet">
    <link href="css/site.css" rel="stylesheet">
</head>
<body>
    <app>Loading...</app>

    <script src="css/bootstrap/bootstrap-native.min.js"></script>
    <script src="_framework/blazor.webassembly.js"></script>
    <script src="exampleJsInterop.js"></script>
</body>
</html>
```

Don't place a script tag in a component file because the script tag can't be updated dynamically.

.NET methods interop with the JavaScript functions by calling `InvokeAsync<T>` method on `IJSRuntime`.

The sample app uses a pair of C\# methods, `Prompt` and `Display`, to invoke the `showPrompt`and `displayWelcome` JavaScript functions:

_JsInteropClasses/ExampleJsInterop.cs_:

```text
public class ExampleJsInterop
{
    public static Task<string> Prompt(string text)
    {
        // showPrompt is implemented in wwwroot/exampleJsInterop.js
        return JSRuntime.Current.InvokeAsync<string>(
            "exampleJsFunctions.showPrompt",
            text);
    }

    public static Task<string> Display(string welcomeMessage)
    {
        // displayWelcome is implemented in wwwroot/exampleJsInterop.js
        return JSRuntime.Current.InvokeAsync<string>(
            "exampleJsFunctions.displayWelcome",
            welcomeMessage);
    }
    
    public static Task CallHelloHelperSayHello(string name)
    {
        // sayHello is implemented in wwwroot/exampleJsInterop.js
        return JSRuntime.Current.InvokeAsync<object>(
            "exampleJsFunctions.sayHello",
            new DotNetObjectRef(new HelloHelper(name)));
    }
}
```

The `IJSRuntime` abstraction is asynchronous to allow for server-side scenarios. If the app runs client-side and you want to invoke a JavaScript function synchronously, downcast to `IJSInProcessRuntime` and call `Invoke<T>` instead. We recommend that most JavaScript interop libraries use the async APIs to ensure the libraries are available in all Blazor scenarios, client-side or server-side.

The sample app includes a component to demonstrate JS interop. The component:

* Receives user input via a JS prompt.
* Returns the text to the component for processing.
* Calls a second JS function that interacts with the DOM to display a welcome message.

_Pages/JSInterop.cshtml_:

```text
@page "/JSInterop"
@using BlazorSample.JsInteropClasses
@using Microsoft.JSInterop;

<h1>JavaScript Interop</h1>

<h2>Invoke JavaScript functions from .NET methods</h2>

<button type="button" class="btn btn-primary" onclick="@TriggerJsPrompt">
    Trigger JavaScript Prompt
</button>

<h3 id="welcome" style="color:green;font-style:italic"></h3>

@functions {
    public async void TriggerJsPrompt()
    {
        var name = await ExampleJsInterop.Prompt("What's your name?");
        await ExampleJsInterop.Display($"Hello {name}! Welcome to Blazor!");
    }
}
```

1. When `TriggerJsPrompt` is executed by selecting the component's **Trigger JavaScript Prompt** button, the `ExampleJsInterop.Prompt` method in C\# code is called.
2. The `Prompt` method executes the JavaScript `showPrompt` function provided in the _wwwroot/exampleJsInterop.js_ file.
3. The `showPrompt` function accepts user input \(the user's name\), which is HTML-encoded and returned to the `Prompt` method and ultimately back to the component. The component stores the user's name in a local variable, `name`.
4. The string stored in `name` is incorporated into a welcome message, which is passed to a second C\# method, `ExampleJsInterop.Display`.
5. `Display` calls a JavaScript function, `displayWelcome`, which renders the welcome message into a heading tag.

### Capture references to elements <a id="capture-references-to-elements"></a>

Some [JavaScript interop](https://blazor.net/docs/javascript-interop.html) scenarios require references to HTML elements. For example, a UI library may require an element reference for initialization, or you might need to call command-like APIs on an element, such as `focus` or `play`.

You can capture references to HTML elements in a component by adding a `ref` attribute to the HTML element and then defining a field of type `ElementRef` whose name matches the value of the `ref` attribute.

The following example shows capturing a reference to the username input element:

```text
<input ref="username" ... />

@functions {
    ElementRef username;
}
```

**NOTE**

Do **not** use captured element references as a way of populating the DOM. Doing so may interfere with Blazor's declarative rendering model.

As far as .NET code is concerned, an `ElementRef` is an opaque handle. The _only_ thing you can do with it is pass it through to JavaScript code via JavaScript interop. When you do so, the JavaScript-side code receives an `HTMLElement` instance, which it can use with normal DOM APIs.

For example, the following code defines a .NET extension method that enables setting the focus on an element:

_mylib.js_:

```text
window.myLib = {
  focusElement : function (element) {
    element.focus();
  }
}
```

_ElementRefExtensions.cs_:

```text
using Microsoft.AspNetCore.Blazor;
using Microsoft.JSInterop;
using System.Threading.Tasks;

namespace MyLib
{
    public static class MyLibElementRefExtensions
    {
        public static Task Focus(this ElementRef elementRef)
        {
            return JSRuntime.Current.InvokeAsync<object>("myLib.focusElement", elementRef);
        }
    }
}
```

Now you can focus inputs in any of your components:

```text
@using MyLib

<input ref="username" />
<button onclick="@SetFocus">Set focus</button>

@functions {
    ElementRef username;

    void SetFocus()
    {
        username.Focus();
    }
}
```

**IMPORTANT**

The `username` variable is only populated after the component renders and its output includes the `<input>` element. If you try to pass an unpopulated `ElementRef` to JavaScript code, the JavaScript code receives `null`. To manipulate element references after the component has finished rendering \(to set the initial focus on an element\) use the `OnAfterRenderAsync` or `OnAfterRender` [component lifecycle methods](https://blazor.net/docs/components/index.html#lifecycle-methods).

### Invoke .NET methods from JavaScript functions <a id="invoke-net-methods-from-javascript-functions"></a>

#### Static .NET method call <a id="static-net-method-call"></a>

To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions. Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments. Again, the async version is preferred to support server-side scenarios. To be invokable from JavaScript, the .NET method must be public, static, and decorated with `[JSInvokable]`. By default, the method identifier is the method name, but you can specify a different identifier using the `JSInvokableAttribute`constructor. Calling open generic methods isn't currently supported.

The sample app includes a C\# method to return an array of `int`s. The method is decorated with the `JSInvokable` attribute.

_Pages/JsInterop.cshtml_:

```text
<button type="button" class="btn btn-primary"
        onclick="exampleJsFunctions.returnArrayAsyncJs()">
    Trigger .NET static method ReturnArrayAsync
</button>

@functions {
    [JSInvokable]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

JavaScript served to the client invokes the C\# .NET method.

_wwwroot/exampleJsInterop.js_:

```text
window.exampleJsFunctions = {
  showPrompt: function (text) {
    return prompt(text, 'Type your name here');
  },
  displayWelcome: function (welcomeMessage) {
    document.getElementById('welcome').innerText = welcomeMessage;
  },
    returnArrayAsyncJs: function () {
      DotNet.invokeMethodAsync('BlazorSample', 'ReturnArrayAsync')
        .then(data => {
          data.push(4);
            console.log(data);
    })
  },
  sayHello: function (dotnetHelper) {
    return dotnetHelper.invokeMethodAsync('SayHello')
      .then(r => console.log(r));
  }
};
```

When the **Trigger .NET static method ReturnArrayAsync** button is selected, examine the console output in the browser's web developer tools:

```text
Array(4) [ 1, 2, 3, 4 ]
```

The fourth array value is pushed to the array \(`data.push(4);`\) returned by `ReturnArrayAsync`.

#### Instance method call <a id="instance-method-call"></a>

You can also call .NET instance methods from JavaScript. To invoke a .NET instance method from JavaScript, first pass the .NET instance to JavaScript by wrapping it in a `DotNetObjectRef`instance. The .NET instance is passed by reference to JavaScript, and you can invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions. The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.

**NOTE**

The sample app logs messages to the client-side console. For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.

When the **Trigger .NET instance method HelloHelper.SayHello** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.

_Pages/JsInterop.cshtml_:

```text
<button type="button" class="btn btn-primary" onclick="@TriggerNetInstanceMethod">
    Trigger .NET instance method HelloHelper.SayHello
</button>

@functions {
    public async void TriggerNetInstanceMethod()
    {
        await ExampleJsInterop.CallHelloHelperSayHello("Blazor");
    }
}
```

`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.

_JsInteropClasses/ExampleJsInterop.cs_:

```text
public class ExampleJsInterop
{
    public static Task<string> Prompt(string text)
    {
        // showPrompt is implemented in wwwroot/exampleJsInterop.js
        return JSRuntime.Current.InvokeAsync<string>(
            "exampleJsFunctions.showPrompt",
            text);
    }

    public static Task<string> Display(string welcomeMessage)
    {
        // displayWelcome is implemented in wwwroot/exampleJsInterop.js
        return JSRuntime.Current.InvokeAsync<string>(
            "exampleJsFunctions.displayWelcome",
            welcomeMessage);
    }
    
    public static Task CallHelloHelperSayHello(string name)
    {
        // sayHello is implemented in wwwroot/exampleJsInterop.js
        return JSRuntime.Current.InvokeAsync<object>(
            "exampleJsFunctions.sayHello",
            new DotNetObjectRef(new HelloHelper(name)));
    }
}
```

_wwwroot/exampleJsInterop.js_:

```text
window.exampleJsFunctions = {
  showPrompt: function (text) {
    return prompt(text, 'Type your name here');
  },
  displayWelcome: function (welcomeMessage) {
    document.getElementById('welcome').innerText = welcomeMessage;
  },
    returnArrayAsyncJs: function () {
      DotNet.invokeMethodAsync('BlazorSample', 'ReturnArrayAsync')
        .then(data => {
          data.push(4);
            console.log(data);
    })
  },
  sayHello: function (dotnetHelper) {
    return dotnetHelper.invokeMethodAsync('SayHello')
      .then(r => console.log(r));
  }
};
```

The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property. When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.

_JsInteropClasses/HelloHelper.cs_:

```text
public class HelloHelper
{
    public HelloHelper(string name)
    {
        Name = name;
    }

    public string Name { get; set; }

    [JSInvokable]
    public string SayHello() => $"Hello, {Name}!";
}
```

Console output in the browser's web developer tools:

```text
Hello, Blazor!
```

### Share interop code in a Blazor class library <a id="share-interop-code-in-a-blazor-class-library"></a>

JavaScript interop code can be included in a Blazor class library \(`dotnet new blazorlib`\), which allows you to share the code in a NuGet package.

The Blazor class library handles embedding JavaScript resources in the built assembly. The JavaScript files are placed in the _wwwroot_ folder, and the tooling takes care of embedding the resources when the library is built.

The built NuGet package is referenced in the project file of a Blazor app just as any normal NuGet package is referenced. After the app has been restored, app code can call into JavaScript as if it were C\#.

