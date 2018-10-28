# 자주 묻는 질문들

`노트: Blazor는 아직 기술 지원이 제공되지 않는 실험용 웹 프레임워크로, 실무 개발에 사용되어서는 안됩니다.`

### Blazor란 무엇인가요? <a id="what-is-blazor"></a>

Blazor는 단일 페이지 웹 앱 (SPA) 프레임워크로 브라우저 안에서 웹 어셈블리로 실행되며, .NET으로 만들어졌습니다.

### .NET을 처음 들어보는데, .NET은 무엇인가요? <a id="im-new-to-net-whats-net"></a>

[.NET](https://www.microsoft.com/net) 은 무료로 제공되며, 크로스 플랫폼이면서, 서로 다른 수많은 종류의 앱 \(데스크탑, 모바일, 게임, 웹\)을 만들기 위한 오픈 소스 개발자 플랫폼입니다. .NET에는 관리되는 런타임, 표준화된 [라이브러리](https://docs.microsoft.com/dotnet/api)가 포함되어있으며, 그리고 여러 모던 프로그래밍 언어들: [C\#](https://docs.microsoft.com/dotnet/csharp/), [F\#](https://docs.microsoft.com/dotnet/fsharp/), 그리고 [VB](https://docs.microsoft.com/dotnet/visual-basic/)를 지원합니다. [10분 안에 .NET 시작하기](https://www.microsoft.com/net/learn/get-started/windows) 가이드를 참고하여 .NET을 배울 수 있습니다.

### .NET을 웹 개발을 위하여 왜 사용해야 합니까? <a id="why-would-i-use-net-for-web-development"></a>

브라우저 안에서 .NET을 사용하면 웹 개발을 보다 쉽고 생산적으로 만들기 위한 많은 장점을 누릴 수 있습니다:

* **안정성과 일관성**: .NET은 표준 API, 도구, 그리고 빌드 인프라를 모든 .NET 플랫폼에 걸쳐 안정적이고, 풍부한 기능을 제공하면서도, 사용하기 쉽게 제공합니다.
* **혁신적인 모던 언어**: .NET의 여러 언어들 중 [C\#](https://docs.microsoft.com/dotnet/csharp/) 과 [F\#](https://docs.microsoft.com/dotnet/fsharp/)은 여러 새롭고 혁신적인 언어 기능들을 통하여 프로그래밍을 더 재미있고 더 향상시킬 수 있게 도와줍니다.
* **업계를 선도하는 도구**: [Visual Studio](https://www.visualstudio.com/) 제품군은 최고의 .NET 개발 환경을 Windows, Linux, 그리고 macOS에서 제공합니다.
* **빠른 속도와 확장성**: .NET은 성능, 안정성, 그리고 보안을 서버에서 제공하기 위한 긴 경험을 가지고 있습니다. .NET을 풀 스택 솔루션으로서 사용하면 여러분의 앱을 더 손쉽게 확장할 수 있습니다.

### .NET을 웹 브라우저에서 어떻게 실행할 수 있는건가요? <a id="how-can-you-run-net-in-a-web-browser"></a>

.NET을 브라우저에서 실행할 수 있는 것은 새로운 웹 표준 기술인 [웹 어셈블리](http://webassembly.org/)에 의하여 가능해 졌습니다. 웸 어셈블리는 "웹으로의 컴파일에 적합한 이식 가능한 크기 및 빠른 로딩 시간을 보장하는 형식"입니다. 웹 어셈블리로 컴파일된 코드는 어떤 브라우저에서든 네이티브와 같은 속도로 실행할 수 있습니다. .NET 바이너리를 웹 브라우저에서 실행하기 위해서는, 웹 어셈블리로 컴파일된 .NET 런타임 \(구체적으로는 [Mono](http://www.mono-project.com/news/2017/08/09/hello-webassembly/)\)을 사용해야 합니다.

### Blazor는 내가 만든 .NET 기반의 앱을 통째로 웹 어셈블리로 컴파일합니까? <a id="does-blazor-compile-my-entire-net-based-app-to-webassembly"></a>

아니오, 하나의 Blazor 앱은 보통의 컴파일된 .NET 어셈블리로 웹 어셈블리 기반의 .NET 런타임을 사용하여 웹 브라우저에 의하여 다운로드되고 실행됩니다. .NET 런타임 그 자체만이 웹 어셈블리로서 컴파일됩니다. 나중에는 앱의 전체 코드를 [정적 \(AoT\) 컴파일](http://www.mono-project.com/news/2018/01/16/mono-static-webassembly-compilation/) 기능을 사용하여 웹 어셈블리로 변환하는 것을 추가하게 될 수도 있습니다.

### .NET 런타임을 포함하는 것이라면, 앱의 전체 다운로드 크기가 매우 크지 않을까요? <a id="wouldnt-the-app-download-size-be-huge-if-it-also-includes-a-net-runtime"></a>

반드시 그렇지는 않습니다. .NET 런타임은 모든 형태의 크기로 제공됩니다. 초기의 Blazor 프로토타입은 경량화된 .NET 런타임 \(어셈블리 실행, 가비지 컬렉션, 스레딩 기능을 제공하는\) 을 사용하여 60KB 내외의 웹 어셈블리로 컴파일되도록 만들어졌습니다. 이제 Blazor는 이전보다 훨씬 크기가 큰 Mono 위에서 실행됩니다. 하지만, 그러나 런타임 및 응용 프로그램 바이너리의 병합 및 트리밍을 비롯하여, 아직 전송될 파일들의 크기에 관해서는 최적화할 여지가 많이 남았습니다. 그 외에도, 캐싱과 CDN을 사용하는 전략도 생각하고 있습니다.

### Blazor에서는 앞으로 어떤 기능을 지원하게 됩니까? <a id="what-features-will-blazor-support"></a>

Blazor는 모던 SPA 프레임워크라면 기대할 수 있는 다음의 기능들을 지원할 것입니다:

* 조합 가능한 UI를 만들기 위한 컴포넌트 모델
* 라우팅
* 레이아웃
* 폼과 유효성 검사
* 의존성 주입
* JavaScript 연동
* 개발 중에 동적 새로 고침 지원
* 서버 측 렌더링
* 브라우저와 IDE에서 모두 사용 가능한 완전한 .NET 디버깅 지원
* 개선된 IntelliSense 지원과 도구
* asm.js를 통한 오래된 \(웹 어셈블리를 지원하지 않는\) 브라우저에서의 실행 지원
* 게시 및 앱 크기 최적화

### 서버가 .NET을 사용하지 않아도 Blazor 앱을 실행할 수 있습니까? <a id="can-i-use-blazor-without-running-net-on-the-server"></a>

예. Blazor 앱은 정적 파일들로 배포되기 때문에 서버가 .NET을 지원하는지의 여부는 아무런 상관이 없습니다.

### Blazor를 서버의 ASP.NET Core와 함께 사용할 수 있습니까? <a id="can-i-use-blazor-with-aspnet-core-on-the-server"></a>

예! Blazor는 부수적으로 [ASP.NET Core](https://docs.microsoft.com/aspnet/core)와의 연동을 통하여 자연스럽고 일관성있는 풀 스택 웹 개발 솔루션을 제공합니다.

### Blazor는 기존 JavaScript 프레임워크를 .NET으로 포팅한 버전입니까? <a id="is-blazor-a-net-port-of-an-existing-javascript-framework"></a>

Blazor는 기존의 모던 SPA 앱 프레임워크인 React, Angular, 그리고 Vue에서 영감을 얻어 만든 _새로운 프레임워크_ 입니다.

### Blazor를 어떻게 테스트해볼 수 있나요? <a id="how-can-i-try-out-blazor"></a>

첫 Blazor 웹 앱을 만들기 위해서는 [Blazor 시작하기](get-started.md) 문서를 참고하세요.

### 왜 Blazor는 "실험용" 프로젝트인가요? <a id="why-is-blazor-an-experimental-project"></a>

Blazor는 실험적 프로젝트입니다. 왜냐하면 쓰임새와 유용성에 대해서 풀어야 할 과제들이 많이 남았기 때문입니다. 이 실험용 프로젝트의 초기 목적은 다음과 같습니다.

* Work through technical issues.
* Gauge interest and to listen to feedback.

Blazor의 미래에 대해서는 낙관적으로 보고 있습니다. 그러나 지금 Blazor는 완전한 제품이 아니며, 극 초기 단계의 프로토타입으로서만 고려되어야 합니다.

### Silverlight와 같은 일을 새로 시작하는건가요? <a id="is-this-silverlight-all-over-again"></a>

아니오, Blazor는 HTML과 CSS를 기반으로 하는 .NET 웹 프레임워크로 공개된 웹 표준을 사용하여 브라우저 안에서 실행됩니다. 플러그인은 전혀 필요하지 않으며, 모바일 장치와 오래된 브라우저에서도 실행됩니다.

### Blazor는 XAML을 사용합니까? <a id="does-blazor-use-xaml"></a>

아니오, Blazor는 HTML, CSS, 그리고 다른 표준 웹 기술을 기반으로하는 웹 프레임워크입니다.

### 웹 어셈블리는 모든 브라우저에서 지원됩니까? <a id="is-webassembly-supported-in-all-browsers"></a>

Yes, WebAssembly has achieved cross-browser consensus and [all modern browsers now support WebAssembly](https://caniuse.com/#search=webassembly).

### Does Blazor work on mobile browsers? <a id="does-blazor-work-on-mobile-browsers"></a>

Yes, [modern mobile browsers also support WebAssembly](https://caniuse.com/#search=webassembly).

### What about older browsers that don't support WebAssembly? For example, does Blazor work in IE? <a id="what-about-older-browsers-that-dont-support-webassembly-for-example-does-blazor-work-in-ie"></a>

For older browsers that don't support WebAssembly, Blazor falls back to using an asm.js-based .NET runtime. Using asm.js is slower and has a larger download size but is still quite functional.

### Can I use .NET Standard libraries with Blazor? <a id="can-i-use-net-standard-libraries-with-blazor"></a>

Yes, the .NET runtime used for Blazor supports .NET Standard 2.0. APIs that aren't supported in the browser throw _Not Supported_ exceptions.

### Don't you need features like garbage collection and threading added to WebAssembly to make this work? <a id="dont-you-need-features-like-garbage-collection-and-threading-added-to-webassembly-to-make-this-work"></a>

No, WebAssembly in its current state is sufficient. The .NET runtime handles its own garbage collection and threading concerns.

### Can I use existing JavaScript libraries with Blazor? <a id="can-i-use-existing-javascript-libraries-with-blazor"></a>

Yes, Blazor apps can call into JavaScript through JavaScript interop APIs.

### Can I access the DOM from a Blazor app? <a id="can-i-access-the-dom-from-a-blazor-app"></a>

You can access the DOM through JavaScript interop from .NET code. However, Blazor is a component-based framework that minimizes the need to access the DOM directly.

### Why Mono? Why not .NET Core or CoreRT? <a id="why-mono-why-not-net-core-or-corert"></a>

[Mono](http://www.mono-project.com/) is a Microsoft-sponsored open source implementation of the .NET Framework. Mono is used by [Xamarin](https://www.xamarin.com/) to build native client apps for Android, iOS, and macOS. Mono is also used by [Unity](https://unity3d.com/) for game development. Microsoft's Xamarin team [announced their plans](http://www.mono-project.com/news/2017/08/09/hello-webassembly/) to add support for WebAssembly to Mono, and they've been making steady progress \([Mono and WebAssembly - Updates on Static Compilation 1/16/2018](http://www.mono-project.com/news/2018/01/16/mono-static-webassembly-compilation/)\). Because Blazor is a client-side web UI framework targeted at WebAssembly, Mono is a natural fit.

By comparison, [.NET Core](https://www.microsoft.com/net/learn/get-started/windows) is primarily used for server apps and for cross-platform console apps. .NET Core can be used for creating an ASP.NET Core backend for a Blazor app but not for building the client app itself. [CoreRT](https://github.com/dotnet/corert) is a .NET Core runtime optimized for AoT compilation; and while it has WebAssembly aspirations, the project is still a work-in-progress and not a shipping product.

### Where did the name "Blazor" come from? <a id="where-did-the-name-blazor-come-from"></a>

Blazor makes heavy use of [Razor](https://docs.microsoft.com/aspnet/core/mvc/views/razor?view=aspnetcore-2.1), a markup syntax for HTML and C\#. **Browser + Razor = Blazor!**When pronounced, it's also the name of a swanky jacket worn by hipsters that have excellent taste in fashion, style, and programming languages.

