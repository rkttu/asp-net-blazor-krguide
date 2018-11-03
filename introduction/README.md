# Blazor 소개

`노트: Blazor는 아직 기술 지원이 제공되지 않는 실험용 웹 프레임워크로, 실무 개발에 사용되어서는 안됩니다.`

Blazor는 브라우저 안에서 WebAssembly로 실행되는 C\#/Razor와 HTML을 사용하는 실험용 .NET 웹 프레임워크입니다. Blazor는 클라이언트 측에서 .NET을 사용하여 얻을 수 있는 (부수적으로 서버에서 사용하는 경우까지 포함하여) 클라이언트 측 웹 UI 프레임워크의 모든 이점을 제공합니다.

### 왜 브라우저에서 .NET을 사용합니까? <a id="why-use-net-in-the-browser"></a>

지난 수년간 웹 개발 기술은 다양한 방식으로 개선되어왔지만, 여전히 모던 웹 앱을 만드는 것은 도전적인 면이 있습니다. .NET을 브라우저에서 사용하면 웹 개발을 더 쉽고 더 생산적으로 만드는데 도움을 주는 여러 장점들이 있습니다:

* **안정성과 일관성**: .NET은 여러 플랫폼에 걸쳐서 안정적이고, 풍분한 기능을 제공하며, 사용하기 쉬운 표준화된 프로그래밍 프레임워크를 제공합니다.
* **혁신적인 모던 프로그래밍 언어**: .NET의 언어들은 혁신적인 새로운 언어 기능과 함께 꾸준히 개선되어 왔습니다.
* **업계 표준의 도구**: Visual Studio 제품군은 환상적인 .NET 개발 환경을 Windos, Linux, 그리고 macOS에 대해 제공합니다.
* **속도와 확장성**: .NET은 앱 개발을 위한 성능, 안정성, 그리고 보안에 대한 강한 기반을 가지고 있습니다. .NET을 풀 스택 솔루션으로 사용하면 빠르고, 안정적이며, 안전한 앱을 만드는 것이 더 쉬워집니다.
* **기존 스킬을 활용하는 풀 스택 개발**: C\#/Razor 개발자는 기존의 C\#/Razor 스킬을 이용하여 클라이언트 측 코드를 작성하고, 서버와 클라이언트 로직을 앱 간에 공유할 수 있습니다.
* **폭 넓은 브라우저 지원**: Blazor는 별도의 플러그인이나 코드 변환없이 브라우저에서 공개 웹 표준을 사용하여 실행되는 환경 위의 .NET에서 실행됩니다. 모바일 브라우저를 포함한 모든 최신 웹 브라우저에서 동작합니다.

### Blazor가 브라우저 안에서 .NET을 실행하는 원리 <a id="how-blazor-runs-net-in-the-browser"></a>

웹 브라우저 안에서 .NET 코드를 실행하는 것은 비교적 최신 기술인 [WebAssembly](http://webassembly.org/) \(이하 _wasm_ 으로 표기합니다.\) 덕분에 가능했습니다. WebAssembly는 공개 웹 표준으로 플러그인 없이 브라우저 안에서 사용할 수 있습니다. WebAssembly는 빠른 다운로드와 실행 속도에 최적화된 최소한의 바이트코드 포맷입니다.

WebAssembly 코드는 JavaScrpt 연동을 통하여 브라우저의 모든 기능에 접근할 수 있습니다. 동시에, WebAssembly 코드는 JavaScript가 실행되는 것과 동일한 안전한 샌드박스 안에서 실행되어, 클라이언트 장치에 위협을 줄 수 있는 동작이 차단됩니다.

Blazor 앱을 만들고 브라우저 안에서 실행할 때에는:

1. C\# 코드 파일과 Razor 파일이 .NET 어셈블리로 컴파일됩니다.
2. 브라우저가 어셈블리와 .NET 런타임을 다운로드합니다.
3. Blazor는 JavaScript를 사용하여 .NET 런타임을 초기화하고, 필요한 어셈블리들을 불러들이도록 런타임을 구성합니다. 문서 개체 모델 \(DOM\) 을 조작하고 브라우저 API 호출을 JavaScript 상호 연동을 통하여 Blazor가 처리하게 됩니다.

WebAssembly를 지원하지 않는 오래된 브라우저를 지원하기 위하여, Blazor는 [asm.js](https://wikipedia.org/wiki/Asm.js) 기반의 .NET 런타임을 대신 사용합니다.

### Blazor 컴포넌트 <a id="blazor-components"></a>

Blazor 앱은 _컴포넌트_ 들로 구성됩니다. 하나의 컴포넌트는 페이지, 대화 상자, 또는 데이터 입력 양식과 같은 UI 조각입니다. 컴포넌트는 중첩되거나, 재사용되거나, 프로젝트 간에 공유될 수 있습니다.

Blazor에서, 컴포넌트는 .NET 클래스입니다. 클래스는 C\# 클래스 \(_\*.cs_\) 로 직접 작성되거나, 혹은 더 많이 사용되는 형태로 Razor 마크업 페이지 \(_\*.cshtml_\)를 통하여 만들어 질 수 있습니다.

[Razor](https://docs.microsoft.com/aspnet/core/mvc/views/razor) 는 C\# 코드와 HTML 마크업을 결합한 문법입니다. Razor는 개발자가 마크업와 C\#을 한 파일 안에서 [IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense)를 사용하여 번갈아가며 사용할 수 있도록, 개발자의 생산성을 위하여 설계되었습니다. 다음은 _DialogComponent.cshtml_ Razor 파일 안에 정의한 기본적인 커스텀 대화 상자 마크업의 예시입니다:

```text
<div>
    <h2>@Title</h2>
    @BodyContent
    <button onclick=@OnOK>OK</button>
</div>

@functions {
    public string Title { get; set; }
    public RenderFragment BodyContent { get; set; }
    public Action OnOK { get; set; }
}
```

이 컴포넌트가 앱 안의 다른 위치에서 사용될 때, IntelliSense는 문법과 파라미터 자동 완성을 지원해줍니다.

컴포넌트는 다음과 같은 특징이 있습니다:

* 중첩 가능합니다.
* Razor \(_\*.cshtml_\) 또는 C\# \(_\*.cs_\) 코드로 만들 수 있습니다.
* 클래스 라이브러리를 통하여 공유할 수 있습니다.
* 브라우저 DOM 없이 단위 테스트를 만들 수 있습니다.

### 구조 <a id="infrastructure"></a>

Blazor는 대부분의 앱이 필요로 하는 다음과 같은 내부 기능들을 제공합니다:

* 레이아웃
* 라우팅
* 의존성 주입

이 기능들은 모두 부수적으로 사용할 수 있습니다. 앱에서 사용하지 않는 기능이 있으면, 앱이 나중에 [중간 언어 \(IL\) 링커](https://blazor.net/docs/host-and-deploy/configure-linker.html)를 통하여 내보내질 때 해당 기능의 구현이 제거된 채로 내보내집니다.

### 코드 공유와 .NET Standard <a id="code-sharing-and-net-standard"></a>

Blazor 앱은 기존의 [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard) 라이브러리를 참조하여 사용할 수 있습니다. .NET Standard는 여러 .NET 구현체들간의 공통된 일반화된 .NET API들의 사양입니다. Blazor는 .NET Standard 2.0 및 이후 버전을 지원합니다. 웹 브라우저 안에서 사용할 수 없는 API들 \(예를 들어, 파일 시스템에 접근하거나, 소켓을 열거나, 다중 스레드를 다루는 등\) 을 사용하려고 하면 [PlatformNotSupportedException](https://docs.microsoft.com/dotnet/api/system.platformnotsupportedexception) 예외가 발생합니다. .NET Standard 클래스 라이브러리들은 서버 코드와 브라우저 기반 앱 간에 공유할 수 있습니다.

### JavaScript 연동 <a id="javascript-interop"></a>

서드파티 JavaScript 라이브러리와 브라우저 API가 필요한 앱을 위하여, WebAssembly는 JavaScript와 연동할 수 있도록 설계되어있습니다. Blazor는 모든 JavaScript 라이브러리와 API를 사용할 수 있습니다. C\# 코드는 JavaScript 코드를 호출할 수 있으며, JavaScript 코드는 C\# 코드를 호출할 수 있습니다. 더 자세한 정보는, [JavaScript 연동](javascript-interop.md) 문서를 참고하세요.

### 최적화 <a id="optimization"></a>

클라이언트 측 앱의 경우, 데이터 다운로드 크기는 매우 중요한 문제입니다. Blazor는 다운로드 크기를 최적화하여 다운로드 시간을 최소화합니다. 예를 들어, .NET 어셈블리에서 사용되지 않는 부분을 빌드 과정 중에 제거합니다. 그리고 HTTP 압축을 사용하거나, .NET 런타임과 어셈블리는 브라우저에서 캐시할 수 있습니다.

### 배포 <a id="deployment"></a>

Blazor를 사용하여 완전히 독립된 클라이언트 측 앱을 만들거나, 서버와 클라이언트 앱을 모두 포함하는 풀 스택 ASP.NET Core 앱을 만들 수 있습니다.

* **독립된 클라이언트 측 앱**을 만들면, Blazor 앱은 _dist_ 폴더 안에 정적 파일들로만 구성된 형태로 컴파일됩니다. 이 파일들은 Azure App Service, GitHub Pages, IIS \(정적 파일 제공 기능을 활성화해야 합니다.\), Node.js 서버, 그리고 수많은 서버와 서비스들 위에서 호스팅할 수 있습니다. 이 방식에서 서버에 .NET 런타임은 필요하지 않습니다.
* **풀 스택 ASP.NET Core 앱**을 만들면, 코드를 서버와 클라이언트에서 공유할 수 있습니다. 만들어지는 ASP.NET Core 서버 앱은 Blazor의 클라이언트 측 UI과 서버 사이트 API 엔드포인트를 제공하며, ASP.NET Core를 지원하는 환경이라면 클라우드와 온 프레미스 환경 어디서나 실행 가능합니다.
