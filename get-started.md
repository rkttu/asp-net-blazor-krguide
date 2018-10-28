# Blazor 시작하기

`노트: Blazor는 아직 기술 지원이 제공되지 않는 실험용 웹 프레임워크로, 실무 개발에 사용되어서는 안됩니다.`

### 설치하기 <a id="setup"></a>

다음을 설치하세요:

1. [.NET Core 2.1 SDK](https://go.microsoft.com/fwlink/?linkid=873092) \(2.1.402 이상\).
2. [Visual Studio 2017](https://go.microsoft.com/fwlink/?linkid=873093) \(15.8 이상\)을 _ASP.NET과 웹 개발 워크로드_를 선택하여 함께 설치하세요.
3. 최신 버전의 [Blazor 언어 서비스 확](https://go.microsoft.com/fwlink/?linkid=870389)을 Visual Studio 마켓플레이스를 통해 설치하세요.
4. 명령줄 도구에서 다음과 같이 Blazor 템플릿을 설치합니다:

   ```text
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates
   ```

Visual Studio에서 첫 프로젝트를 만들려면:

1. **파일** &gt; **새 프로젝트** &gt; **웹** &gt; **ASP.NET Core 웹 애플리케이션**을 선택하세요.
2. 반드시 **.NET Core**와 **ASP.NET Core 2.1**을 창 위에서 선택하세요.
3. Blazor 템플릿을 선택하고 **확인**을 누르세요.

   ![New Blazor app dialog](https://msdnshared.blob.core.windows.net/media/2018/07/new-blazor-app-dialog-0.5.0.png)

4. 키보드에서 **Ctrl키와 F5키**를 눌러 _디버거 없이 앱을 실행_합니다. 디버거를 연결하여 실행하는 기능 \(**F5**\)은 아직 지원되지 않습니다.

명령줄에서 새 Blazor 앱을 만들려면:

```text
dotnet new blazor -o BlazorApp1
cd BlazorApp1
dotnet run
```

축하합니다! 여러분은 이제 첫 Blazor 앱을 실행하셨습니다!

![Blazor &#xC571; &#xD648;&#xD398;&#xC774;&#xC9C0;](https://msdnshared.blob.core.windows.net/media/2018/04/blazor-bootstrap-4.png)

### 도움말과 피드백 <a id="help--feedback"></a>

여러분의 피드백은 Blazor의 실험용 버전을 개발하는 데에 매우 요긴하게 사용됩니다. 만약 Blazor를 실행하면서 문제가 발생하거나 질문이 있다면, 꼭 알려주세요!

* 어떤 종류이든 관계없이 문제가 발생하였거나, 혹은 제안과 개선 사항이 있다면 [GitHub에 이슈를 ](https://github.com/aspnet/blazor/issues)해주세요.
* 만약 문제가 발생하거나 Blazor를 여러분이 원하는대로 동작하도록 수정한 경험이 있다면 Blazor 개발자들과 [Gitter](https://gitter.im/aspnet/blazor) 커뮤니티에서 대화하여 공유해주세요.

Blazor를 테스트해본 다음에는, 제품과 함께 제공되는 설문 조사에 응답하셔서 여러분의 의견을 알려주세요. 기본 제공되는 Blazor 프로젝트 템플릿을 아무거나 하나 선택하여 앱을 실행하고, 아래 화면과 같이 앱 홈페이지에 있는 설문 조사 링크를 클릭하면 됩니다.

![Blazor &#xC124;&#xBB38; &#xC870;&#xC0AC;](https://msdnshared.blob.core.windows.net/media/2018/05/blazor-survey-new.png)

### 다음 단계 <a id="whats-next"></a>

[첫 Blazor 앱 만들기](tutorials/build-your-first-blazor-app.md)

