# 의존성 주입

`노트: Blazor는 아직 기술 지원이 제공되지 않는 실험용 웹 프레임워크로, 실무 개발에 사용되어서는 안됩니다.`

Blazor는 [의존성 주입\(DI\)](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) 를 기본으로 제공합니다. 앱에서는 내장된 서비스를 사용하여 컴포넌트로 의존성을 주입할 수 있습니다. 또한 커스텀 서비스를 정의하고, DI를 통해서 사용할 수도 있습니다.

### 의존성 주입이란? <a id="what-is-dependency-injection"></a>

의존성 주입은 서비스에 접근하기 위한 구성을 한 곳에서 관리하기 위한 기법입니다. 이 방법은 다음과 같은 장점이 있습니다:

* 서비스 클래스의 단일 인스턴스를 여러 컴포넌트에 걸쳐 공유할 수 있습니다. \(싱글톤 서비스라고도 합니다.\)
* 특정 서비스 클래스에서 컴포넌트를 분리하고, 추상화된 내용만을 참조할 수 있습니다. 예를 들어, `IDataAccess` 인터페이스는 `DataAccess` 라는 구체화된 클래스에 의하여 구현되는 상황을 가정할 수 있습니다. 컴포넌트가 DI를 사용하여 `IDataAccess` 인터페이스에 대한 구현체를 받기로 했다면, 컴포넌트는 이 인터페이스를 실제로 구현하는 클래스와는 결합되지 않습니다. 구현체는 교체될 수 있는데, 예를 들어 단위 테스트를 위한 모형의 구현체로 교체할 수 있습니다.

Blazor의 의존성 주입 시스템은 컴포넌트들로 서비스 인스턴스들을 제공하는 역할을 합니다. 의존성 주입은 또한 의존성을 재귀적으로 처리하므로 서비스가 다른 서비스들을 참조하는 것도 가능합니다. 의존성 주입은 앱이 시작될 때 구성됩니다. 이 토픽의 후반부에서 예제를 다루겠습니다.

### 의존성 주입에 서비스 추가하기 <a id="add-services-to-di"></a>

새로운 앱을 만든 후에, `Startup.ConfigureServices` 메서드를 살펴봅니다:

```text
public void ConfigureServices(IServiceCollection services)
{
    // 여기에 커스텀 서비스를 추가합니다.
}
```

`ConfigureServices` 메서드로 [IServiceCollection](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.iservicecollection) 인스턴스가 전달되는데, 이것은 서비스 디스크립터 개체인 \([ServiceDescriptor](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.servicedescriptor)\) 형식의 인스턴스들의 목록입니다. 서비스는 서비스 디스크립터를 서비스 컬렉션에 추가함으로서 의존성 주입에 추가될 수 있습니다. 다음의 코드 예제는 이러한 컨셉을 설명합니다:

```text
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

서비스는 다음과 같이 생명 주기를 설정할 수 있습니다:

| 생명 주기 | 설명 |
| :--- | :--- |
| [Singleton](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.servicedescriptor.singleton#Microsoft_Extensions_DependencyInjection_ServiceDescriptor_Singleton__1_System_Func_System_IServiceProvider___0__) | 의존성 주입에 의하여 서비스의 _싱글턴 인스턴스_ 가 생성됩니다. 이렇게 정의된 서비스를 필요로 하는 컴포넌트에 인스턴스가 전달됩니다. |
| [Transient](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.servicedescriptor.transient) | 서비스에서 컴포넌트를 필요로 할 때 마다, 서비스의 _새로운 인스턴스_ 를 생성하여 전달합니다. |
| [Scoped](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.servicedescriptor.scoped) | Blazor는 현재 의존성 주입의 범위를 지정하는 기능을 제공하지 않습니다. `Scoped`는 `Singleton`처럼 작동하므로, `Scoped` 대신 `Singleton`을 사용하는 것이 좋습니다. |

Blazor의 의존성 주입 시스템은 ASP.NET Core의 의존성 주입 시스템을 기반으로 합니다. 더 자세한 정보는, [ASP.NET Core의 의존성 주입](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)을 참고하십시오.

### 기본 서비스 <a id="default-services"></a>

Blazor는 앱의 서비스 컬렉션에 자동으로 기본 서비스들을 추가하여 제공합니다. 다음의 표에서는 현재 Blazor의 [BrowserServiceProvider](https://blazor.net/api/Microsoft.AspNetCore.Blazor.Browser.Services.BrowserServiceProvider.html)에 의하여 제공되는 기본 서비스들을 나타내고 있습니다.

| 서비스 | 설명 |
| :--- | :--- |
| [IUriHelper](https://blazor.net/api/Microsoft.AspNetCore.Blazor.Services.IUriHelper.html) | URI와 탐색 상태를 제어하는 도우미 서비스입니다. \(싱글턴\). |
| [HttpClient](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient) | URI로 식별되는 리소스에 대해 HTTP 요청을 보내거나 HTTP 응답을 받을 수 있는 수단을 제공합니다. \(싱글턴\) 참고할 것은, `HttpClient` 형식의 인스턴스는 브라우저를 사용하여 HTTP 요청을 백그라운드에서 처리합니다. [HttpClient.BaseAddress](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient.baseaddress) 속성은 자동으로 앱의 기본 URI로 설정됩니다. |

커스텀 서비스 공급자를 `BrowserServiceProvider` 대신 사용하는 것도 가능합니다. 커스텀 서비스 공급자를 사용하면 앞의 표에서 제공되는 서비스가 포함되지 않기 때문에, 직접 해당 서비스들을 명시적으로 제공해야 합니다.

### 컴포넌트에서 서비스 요청하기 <a id="request-a-service-in-a-component"></a>

서비스 컬렉션에 서비스가 추가되면, 컴포넌트의 Razor 템플릿에 `@inject` Razor 지시자를 사용하여 주입할 수 있습니다. `@inject`는 두 개의 매개 변수를 받습니다:

* 형식 이름: 주입하려는 서비스의 형식입니다.
* 프로퍼티 이름: 주입되는 앱 서비스를 받을 속성의 이름을 지정합니다. 프로퍼티를 직접 만들 필요는 없고, 컴파일러가 대신 속성을 새로 정의합니다.

여러 개의 `@inject` 문을 사용하여 여러 달느 서비스들을 주입할 수도 있습니다.

다음의 예제에서는 `@inject`를 어떻게 사용하는지 보여줍니다. `Services.IDataAccess`를 구현하는 서비스를 컴포넌트의 `DataRepository` 속성에 주입합니다. 여기서 코드는 `IDataAccess`가 제공하는 추상화된 부분만을 사용한다는 것에 주목합니다:

```text
@page "/customer-list"
@using Services
@inject IDataAccess DataRepository

<ul>
    @if (Customers != null)
    {
        @foreach (var customer in Customers)
        {
            <li>@customer.FirstName @customer.LastName</li>
        }
    }
</ul>

@functions {
    private IReadOnlyList<Customer> Customers;

    protected override async Task OnInitAsync()
    {
        // The property DataRepository received an implementation
        // of IDataAccess through dependency injection. Use 
        // DataRepository to obtain data from the server.
        Customers = await DataRepository.GetAllCustomersAsync();
    }
}
```

내부적으로, 생성된 프로퍼티 \(`DataRepository`\)는 [InjectAttribute](https://blazor.net/api/Microsoft.AspNetCore.Blazor.Components.InjectAttribute.html) 어트리뷰트가 붙습니다. 보통 이 어트리뷰트는 직접 사용되지 않습니다. 구성 요소에 기본 클래스가 필요하고 주입된 속성이 기본 클래스에도 필요하면 `InjectAttribute`를 수동으로 추가 할 수 있습니다:

```text
public class ComponentBase : BlazorComponent
{
    // 컴포넌트의 기본 클래스에 InjectAttribute를 사용하더라도
    // Blazor의 의존성 주입이 적용됩니다.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

기본 클래스로부터 컴포넌트가 상속되면, `@inject` 지시자가 필요하지 않습니다. 기본 클래스에 `InjectAttribute`를 사용하는 것만으로도 충분합니다:

```text
@page "/demo"
@inherits ComponentBase

<h1>...</h1>
...
```

### 서비스로 의존성 주입 <a id="dependency-injection-in-services"></a>

복잡한 서비스의 경우 더 많은 서비스들을 필요로 할 수 있습니다. 앞의 예시인 `DataAccess` 서비스는 Blazor의 기본 서비스인 `HttpClient`를 필요로 할 수 있습니다. `@inject` 또는 `InjectAttribute`를 서비스 안에서 사용할 수 없습니다. 이 경우 _생성자 주입_ 이 대신 사용되어야 합니다. 필요한 서비스들을 서비스의 생성자의 매개 변수에 추가하는 방법으로 지정할 수 있습니다. 의존성 주입에 의하여 서비스가 만들어지면, 생성자에서 필요로하는 서비스들을 인식하여 적절하게 제공합니다.

다음의 코드 예제에서는 이 컨셉을 보여줍니다:

```text
public class DataAccess : IDataAccess
{
    // 생성자는 의존성 주입에 의하여 HttpClient의 인스턴스를 받게 됩니다.
    // HttpClient는 Blazor가 기본으로 제공하는 서비스입니다.
    public DataAccess(HttpClient client)
    {
        ...
    }
    ...
}
```

생성자 주입을 사용하려면 다음의 조건을 만족해야 합니다:

* 의존성 주입에 의하여 매개 변수를 제공할 수 있는 생성자가 한 개만 있어야 합니다. 단, 기본값이 지정된 매개 변수들은 의존성 주입에 의하여 처리되지 않더라도 사용할 수 있습니다.
* 반드시 _public_ 으로 선언된 생성자여야 합니다.
* 조건에 만족하는 생성자가 하나만 있어야 합니다. 조건을 만족하는 생성자가 여러 개여서 모호한 상황이 되면, 의존성 주입 처리 도중 예외를 발생시키게 됩니다.

### 추가 리소스 <a id="additional-resources"></a>

* [ASP.NET Core에서의 의존성 주입](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)

