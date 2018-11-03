# 첫 Blazor 앱 만들기

`노트: Blazor는 아직 기술 지원이 제공되지 않는 실험용 웹 프레임워크로, 실무 개발에 사용되어서는 안됩니다.`

이 튜토리얼에서 여러분은 Blazor 앱을 단계별로 만들어가며, 빠르게 Blazor 프레임워크의 기본 기능을 배우게 됩니다.

[샘플 코드를 보거나 다운로드하려면 여기를 클릭하세요.](https://github.com/aspnet/Blazor.Docs/tree/master/docs/tutorials/build-your-first-blazor-app/samples/) \([다운로드하는 방법](https://blazor.net/docs/index.html#view-and-download-samples)\) [Blazor 시작하기](../get-started.md) 문서를 참고하여 미리 준비해야 할 내용을 확인하세요.

Visual Studio에서 새 프로젝트를 만들려면:

1. **파일** &gt; **새로 만들기** &gt; **프로젝트**를 선택합니다. **웹** &gt; **ASP.NET Core 웹 응용프로그램**을 선택합니다. 프로젝트의 **이름** 필드에 "BlazorApp1" 을 입력합니다. **확인** 버튼을 누릅니다.

   ![&#xC0C8; ASP.NET Core &#xD504;&#xB85C;&#xC81D;&#xD2B8;](https://raw.githubusercontent.com/aspnet/Blazor.Docs/gh-pages/docs/tutorials/build-your-first-blazor-app/_static/new-aspnet-core-project.png)

2. **새 ASP.NET Core 웹 응용프로그램** 대화 상자가 나타납니다. 상단에 **.NET Core** 가 선택되어있는지 확인합니다. 그리고 **ASP.NET Core 2.0** 또는 **ASP.NET Core 2.1**이 선택되어있는지 확인합니다. **Blazor** 템플릿을 선택하고 **확인** 버튼을 클릭합니다.

   ![&#xC0C8; Blazor &#xC571; &#xB300;&#xD654; &#xC0C1;&#xC790;](https://raw.githubusercontent.com/aspnet/Blazor.Docs/gh-pages/docs/tutorials/build-your-first-blazor-app/_static/new-blazor-app-dialog.png)

3. 프로젝트가 생성되면, **Ctrl키와 F5키** 를 눌러 앱을 _디버거 없이_ 실행합니다. 디버거와 함께 시작하는 기능 \(**F5키**\) 은 아직 지원되지 않습니다.

**노트**

Visual Studio 없이 Windows, macOS, 또는 Linux 명령줄에서 새 프로젝트를 만들려면 아래와 같이 입력하세요:

```text
dotnet new blazor -o BlazorApp1
cd BlazorApp1
dotnet run
```

`dotnet run`이 실행된 후에 나타난 콘솔 창의 출력에 기재된 localhost 주소과 포트 번호로 웹 브라우저를 열어 탐색합니다. **Ctrl키와 C키**를 콘솔 창에서 누르면 앱이 종료됩니다.

Blazor 앱이 브라우저에 다음과 같이 표시됩니다:

![Blazor &#xC571; &#xD648;&#xD398;&#xC774;&#xC9C0;](https://user-images.githubusercontent.com/1874516/39509497-5515c3ea-4d9b-11e8-887f-019ea4fdb3ee.png)

## 컴포넌트 만들기    <a id="build-components"></a>

1. 앱의 페이지 3개 \(Home, Counter, FetchData\)를 각각 탐색합니다

   이들 페이지 3개는 _Page_ 폴더 안에 _Index.cshtml_, _Counter.cshtml_, _FetchData.cshtml_ 파일로 구현되어있습니다. 각각의 파일은 Blazor 컴포넌트를 구현하며, 컴포넌트들은 컴파일되어 클라이언트 측의 브라우저에서 실행됩니다.

2. Counter 페이지의 버튼을 선택합니다.

   ![Blazor &#xC571; &#xD648;&#xD398;&#xC774;&#xC9C0;](https://user-images.githubusercontent.com/1874516/39509525-6e367c66-4d9b-11e8-9978-e52a9750c34b.png)

   버튼을 클릭할 때 마다, 페이지 새로 고침 없이 카운터의 값이 증가합니다. 보통은 이런 종류의 클라이언트 측 동작은 JavaScript에서 처리됩니다. 하지만 여기서는 C\#과 .NET으로 `Counter` 컴포넌트의 동작을 구현하였습니다.

3. _Counter.cshtml_ 파일 안의 `Counter` 컴포넌트를 구현한 코드를 살펴봅니다:

   ```text
   @page "/counter"

   <h1>Counter</h1>

   <p>Current count: @currentCount</p>

   <button class="btn btn-primary" onclick="@IncrementCount">Click me</button>

   @functions {
       int currentCount = 0;

       void IncrementCount()
       {
           currentCount++;
       }
   }
   ```

   `Counter` 컴포넌트의 UI는 보통의 HTML을 사용하여 정의되었습니다. 동적 렌더링 로직 \(예를 들어 반복문, 조건문, 표현식\)은 내장된 C\# 문법인 [Razor](https://docs.microsoft.com/aspnet/core/mvc/views/razor)로 추가되었습니다. HTML 마크업과 C\# 렌더링 로직은 컴포넌트 클래스로 빌드 중에 변환됩니다. 만들어지는 .NET 클래스의 이름은 파일의 이름과 같은 이름을 사용합니다.

   컴포넌트 클래스의 멤버들은 `@functions` 블록에 정의됩니다. `@functions` 블록에서는, 컴포넌트 상태 \(속성, 필드\)와 메서드가 이벤트 처리나 다른 컴포넌트 로직을 위하여 추가될 수 있습니다. 이 멤버들은 컴포넌트의 렌더링 로직과 이벤트 처리의 일부분으로 사용될 수 있습니다.

   버튼을 클릭하면, `Counter` 컴포넌트에 등록된 `onclick` 핸들러가 호출되며 \(`IncrementCount` 메서드\) `Counter` 컴포넌트는 렌더 트리를 다시 생성합니다. Blazor는 이전에 만들어진 렌더 트리와 새로 만들어진 렌더 트리를 비교하여 브라우저의 문서 개체 모델 \(DOM\)에 모든 변경된 내용을 반영합니다. 이렇게 표시되는 카운트 숫자가 변경됩니다.

4. `Counter` 컴포넌트의 마크업을 수정하여 헤더 부분을 좀 더 _신나게_ 꾸며봅니다.

   ```text
   @page "/counter"
   <h1><em>Counter!!</em></h1>
   ```

5. 또한 `Counter` 컴포넌트의 C\# 로직을 수정하여 값을 1씩 증가하는 것에서 2씩 증가하는 것으로 변경해봅니다.

   ```text
   @functions {
       int currentCount = 0;

       void IncrementCount()
       {
           currentCount += 2;
       }
   }
   ```

6. 카운터 페이지를 브라우저에서 새로 고침하여 변경된 내용을 확인합니다.

   ![Exciting counter](https://user-images.githubusercontent.com/1874516/39509668-e8949a92-4d9b-11e8-91e9-b6a494695d92.png)

## 컴포넌트 사용하기    <a id="use-components"></a>

컴포넌트가 정의된 후에는 또 다른 컴포넌트에서 새로 정의한 컴포넌트를 사용할 수 있습니다. 다른 컴포넌트를 사용하는 마크업은 보통의 HTML 태그와 다르지 않으며, 컴포넌트 형식의 이름을 지정하는 것만 다릅니다.

1. `Counter` 컴포넌트를 앱의 홈 페이지 \(_Index.cshtml_\)에 추가합니다.

   ```text
   @page "/"

   <h1>Hello, world!</h1>

   Welcome to your new app.

   <SurveyPrompt Title="How is Blazor working for you?" />

   <Counter />
   ```

2. 브라우저에서 홈 페이지를 새로 고침합니다. 여기서 홈 페이지에 추가된 `Counter` 컴포넌트는 별도의 인스턴스입니다.

   ![Blazor &#xD648; &#xD398;&#xC774;&#xC9C0;&#xC5D0; &#xCE74;&#xC6B4;&#xD130; &#xCEF4;&#xD3EC;&#xB10C;&#xD2B8;&#xB97C; &#xCD94;&#xAC00;&#xD55C; &#xBAA8;&#xC2B5;](https://user-images.githubusercontent.com/1874516/39509718-224483f6-4d9c-11e8-9030-b4c7228d669d.png)

## 컴포넌트 매개 변수    <a id="component-parameters"></a>

컴포넌트는 또한 매개 변수를 가질 수 있으며, 컴포넌트 클래스를 정의할 때 private 접근자를 가진 멤버 앞에 `[Parameter]` 특성을 추가하는 방식으로 만들 수 있습니다. 마크업에서는 이렇게 추가한 매개 변수에 어트리뷰트를 통하여 값을 지정합니다.

1. `Counter` 컴포넌트를 수정하여 기본값으로 1을 갖는 `IncrementAmount` 매개 변수를 추가합니다.

   ```text
   @functions {
       int currentCount = 0;

       [Parameter]
       private int IncrementAmount { get; set; } = 1;

       void IncrementCount()
       {
           currentCount += IncrementAmount;
       }
   }
   ```

   **노트**

   Visual Studio에서는, `para` 코드 조각을 사용하여 컴포넌트 매개 변수를 빠르게 추가할 수 있습니다. `para` 를 입력하고 `Tab` 키를 두 번 누르면 됩니다.

2. 홈 페이지 \(_Index.cshtml_\)에서, `Counter` 컴포넌트의 증가값을 앞에서 추가한 `IncrementCount` 프로퍼티와 동일한 어트리뷰트 이름을 기재하고, 이 어트리뷰트의 값으로 10을 지정하여 변경합니다.

   ```text
   <Counter IncrementAmount="10" />
   ```

3. 페이지를 새로 고침합니다.

   홈 페이지의 카운터는 이제 10씩 증가하며, Counter 페이지의 카운터는 여전히 1씩 증가하는 것과는 별개로 동작합니다.

   ![10&#xC529; &#xC99D;&#xAC00;&#xD558;&#xB294; Blazor &#xCE74;&#xC6B4;&#xD130;](https://user-images.githubusercontent.com/1874516/39509798-618f0720-4d9c-11e8-9125-3d4c634dff46.png)

## 컴포넌트로 라우팅    <a id="route-to-components"></a>

_Counter.cshtml_ 파일의 가장 위에 있는 `@page` 지시자는 이 컴포넌트가 요청을 라우팅할 수 있는 페이지 컴포넌트라는 것을 뜻합니다. 구체적으로, `Counter` 컴포넌트는 `/Counter` 로 보내지는 요청을 처리합니다. `@page` 지시자가 없으면, 컴포넌트는 라우팅된 요청을 처리할 수 없지만, 다른 컴포넌트에서 여전히 이 컴포넌트를 사용할 수는 있습니다.

## 의존성 주입    <a id="dependency-injection"></a>

앱의 서비스 공급자와 함께 등록되는 서비스들은 [의존성 주입 \(DI\)](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection)을 통하여 컴포넌트에서 사용할 수 있습니다. 서비스는 `@inject` 지시자를 사용하여 컴포넌트로 주입될 수 있습니다.

_FetchData.cshtml_ 파일 안의 `FetchData` 컴포넌트의 구현을 살펴보겠습니다. `@inject` 지시자가 [HttpClient](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient) 인스턴스를 컴포넌트에 주입할 것을 지시하고 있습니다.

```text
@page "/fetchdata"
@inject HttpClient Http
```

`FetchData` 컴포넌트는 주입된 `HttpClient`를 사용하여 컴포넌트가 초기화될 때 서버로부터 JSON 데이터를 받아옵니다. 내부적으로, `HttpClient`는 Blazor 런타임에 의하여 제공되며, 이 클래스는 JavaScript를 이용하여 브라우저 내부의 Fetch API를 호출함으로서 요청을 보내도록 구현되어 있습니다. \(C\#에서는, 모든 JavaScript 라이브러리 또는 브라우저 API를 자유롭게 호출할 수 있습니다.\) 수신된 데이터는 `WeatherForecast` 개체의 배열 형태로 `forecasts` 변수로 역 직렬화됩니다.

```text
@functions {
    WeatherForecast[] forecasts;

    protected override async Task OnInitAsync()
    {
        forecasts = await Http.GetJsonAsync<WeatherForecast[]>("/sample-data/weather.json");
    }

    class WeatherForecast
    {
        public DateTime Date { get; set; }
        public int TemperatureC { get; set; }
        public int TemperatureF { get; set; }
        public string Summary { get; set; }
    }
}
```

`@foreach` 반복문은 날씨 표의 각 행마다 각각의 forecast 인스턴스의 데이터를 표현하기 위하여 사용됩니다.

```text
<table class="table">
    <thead>
        <tr>
            <th>Date</th>
            <th>Temp. (C)</th>
            <th>Temp. (F)</th>
            <th>Summary</th>
        </tr>
    </thead>
    <tbody>
        @foreach (var forecast in forecasts)
        {
            <tr>
                <td>@forecast.Date.ToShortDateString()</td>
                <td>@forecast.TemperatureC</td>
                <td>@forecast.TemperatureF</td>
                <td>@forecast.Summary</td>
            </tr>
        }
    </tbody>
</table>
```

## 할 일 목록 만들기    <a id="build-a-todo-list"></a>

앱에 새로운 페이지를 추가하여 간단한 할 일 목록을 만들어 보도록 하겠습니다.

1. _Pages_ 폴더에 _Todo.cshtml_ 이라는 빈 텍스트 파일을 하나 추가합니다.
2. 페이지에 다음과 같이 기본 마크업을 넣습니다.

   ```text
   @page "/todo"

   <h1>Todo</h1>
   ```

3. _Shared/NavMenu.cshtml_ 파일을 수정하여 탐색 바에 Todo 페이지를 추가합니다. `NavLink`를 기존의 목록 항목 아래에 할 일 목록 페이지를 위하여 하나 추가합니다.

   ```text
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

4. 브라우저에서 앱을 새로 고침합니다. 새로 추가된 할 일 목록 페이지를 확인합니다.

   ![Blazor &#xD560; &#xC77C; &#xBAA9;&#xB85D;](https://user-images.githubusercontent.com/1874516/39509907-bb27e77a-4d9c-11e8-91e7-ea1e7c01729e.png)

5. _TodoItem.cs_ 파일을 프로젝트 루트에 추가하여 할 일 목록에 대한 정보를 담는 클래스를 추가합니다.
6. 다음의 C\# 코드를 `ToDoItem` 클래스를 위하여 사용합니다.

   ```text
   public class TodoItem
   {
       public string Title { get; set; }
       public bool IsDone { get; set; }
   }
   ```

7. _Todo.cshtml_ 파일 안의 `Todo` 컴포넌트로 되돌아가서, `@functions` 블록 안에 할 일 목록들을 저장하기 위한 필드를 하나 추가합니다. `Todo` 컴포넌트는 이 필드를 사용하여 할 일 목록의 상태를 관리하게 됩니다.

   ```text
   @functions {
       IList<TodoItem> todos = new List<TodoItem>();
   }
   ```

8. 번호 없는 목록 마크업을 하나 추가하고, `foreach` 반복문을 사용하여 각각의 할 일 항목을 목록 형식으로 표시하도록 합니다.

   ```text
   @page "/todo"

   <h1>Todo</h1>

   <ul>
       @foreach (var todo in todos)
       {
           <li>@todo.Title</li>
       }
   </ul>
   ```

9. 앱에 새로운 할 일 목록을 추가할 수 있는 UI가 필요합니다. 텍스트 입력 상자와 버튼을 목록 아래에 다음과 같이 추가합니다.

   ```text
   @page "/todo"

   <h1>Todo</h1>

   <ul>
       @foreach (var todo in todos)
       {
           <li>@todo.Title</li>
       }
   </ul>

   <input placeholder="Something todo" />
   <button>Add todo</button>

   @functions {
       IList<TodoItem> todos = new List<TodoItem>();
   }
   ```

10. 브라우저를 새로 고침합니다.

    ![Add todo &#xBC84;&#xD2BC;](https://user-images.githubusercontent.com/1874516/39512402-bc88ab46-4da5-11e8-9e3f-87b875b56383.png)

    **Add todo** 버튼을 클릭해도 버튼에 이벤트 처리기를 지정한 것이 없기 때문에 아무런 일도 일어나지 않습니다.

11. `AddTodo` 메서드를 `Todo` 컴포넌트에 추가하고, 버튼의 `onclick` 어트리뷰트를 사용하여 버튼의 클릭 이벤트에 등록합니다.

    ```text
    <input placeholder="Something todo" />
    <button onclick="@AddTodo">Add todo</button>

    @functions {
        IList<TodoItem> todos = new List<TodoItem>();

        void AddTodo()
        {
            // Todo: 할 일 목록을 추가합니다.
        }
    }
    ```

    `AddTodo` C\# 메서드는 버튼을 클릭할 때 마다 호출됩니다.

12. 새로운 할 일 정보를 받아오기 위하여, `newTodo` 문자열 필드를 추가하고 텍스트 입력 상자의 `bind` 어트리뷰트에 이 필드를 바인딩합니다.

    ```text
    IList<TodoItem> todos = new List<TodoItem>();
    string newTodo;
    ```

    ```text
    <input placeholder="Something todo" bind="@newTodo" />
    ```

13. `AddTodo` 메서드를 수정하여 `TodoItem`을 지정한 제목으로 목록에 추가합니다. `newTodo` 에 빈 문자열을 지정하여 텍스트 입력 상자의 값을 초기화하는 것을 잊지 않도록 합니다.

    ```text
    @page "/todo"

    <h1>Todo</h1>

    <ul>
        @foreach (var todo in todos)
        {
            <li>@todo.Title</li>
        }
    </ul>

    <input placeholder="Something todo" bind="@newTodo" />
    <button onclick="@AddTodo">Add todo</button>

    @functions {
        IList<TodoItem> todos = new List<TodoItem>();
        string newTodo;

        void AddTodo()
        {
            if (!string.IsNullOrWhiteSpace(newTodo))
            {
                todos.Add(new TodoItem { Title = newTodo });
                newTodo = string.Empty;
            }
        }
    }
    ```

14. 브라우저를 새로 고침합니다. 할 일 목록에 새로운 할 일을 몇 개 추가해봅니다.

    ![&#xD560; &#xC77C; &#xBAA9;&#xB85D; &#xCD94;&#xAC00;&#xD558;&#xAE30;](https://user-images.githubusercontent.com/1874516/39512531-2d2ff62e-4da6-11e8-8b83-291b0efc821b.png)

15. 마지막으로, 할 일 목록에 체크 박스가 빠져 있으니 이상하지 않나요? 각 할 일 항목의 제목 부분은 또한 수정 가능하게 만들 수 있을 것입니다. 체크 박스 입력 상자와 텍스트 입력 상자를 각 할 일 항목에 추가하고, 각각 `Title`과 `IsDone` 속성에 바인딩되도록 하여 값을 연결시키도록 합니다.

    ```text
    <ul>
        @foreach (var todo in todos)
        {
            <li>
                <input type="checkbox" bind="@todo.IsDone" />
                <input bind="@todo.Title" />
            </li>
        }
    </ul>
    ```

16. 값이 제대로 연결되었는지 확인하기 위하여, `h1` 헤더를 수정하여 아직 완료되지 않은 할 일 \(`IsDone`의 값이 `false`\인 항목\)들의 갯수를 표시하도록 합니다.

    ```text
    <h1>Todo (@todos.Where(todo => !todo.IsDone).Count())</h1>
    ```

17. 완성된 전체 `Todo` 컴포넌트는 다음과 같습니다:

    ```text
    @page "/todo"

    <h1>Todo (@todos.Where(todo => !todo.IsDone).Count())</h1>

    <ul>
        @foreach (var todo in todos)
        {
            <li>
                <input type="checkbox" bind="@todo.IsDone" />
                <input bind="@todo.Title" />
            </li>
        }
    </ul>

    <input placeholder="Something todo" bind="@newTodo" />
    <button onclick="@AddTodo">Add todo</button>

    @functions {
        IList<TodoItem> todos = new List<TodoItem>();
        string newTodo;

        void AddTodo()
        {
            if (!string.IsNullOrWhiteSpace(newTodo))
            {
                todos.Add(new TodoItem { Title = newTodo });
                newTodo = string.Empty;
            }
        }
    }
    ```

브라우저를 새로 고침합니다. 그리고 할 일 항목들을 추가해봅니다.

![&#xC644;&#xC131;&#xB41C; Blazor &#xD560; &#xC77C; &#xBAA9;&#xB85D;](https://user-images.githubusercontent.com/1874516/39512621-83406508-4da6-11e8-8244-5bae2ac6b22d.png)

## 게시 및 배포하기    <a id="publish-and-deploy"></a>

Visual Studio를 사용할 때에는, 다음의 단계를 진행하여 Blazor 할 일 목록 앱을 Azure로 게시할 수 있습니다:

1. **솔루션 탐색기** 의 프로젝트를 마우스 오른쪽 버튼으로 클릭하고, **게시** 를 선택합니다.
2. **게시 대상 선택** 대화 상자에서, **앱 서비스**를 선택하고 **새로 만들기**를 선택합니다. 그리고 **게시**를 선택합니다.

   ![&#xAC8C;&#xC2DC; &#xB300;&#xC0C1; &#xC120;&#xD0DD;&#xD558;&#xAE30;](https://raw.githubusercontent.com/aspnet/Blazor.Docs/gh-pages/docs/tutorials/build-your-first-blazor-app/_static/blazor-publish-pick-target.png)

3. **새 앱 서비스 만들기** 대화 상자에서, 앱의 이름을 고른 다음 구독, 리소스 그룹, 그리고 호스팅 계획을 선택합니다. **만들기** 버튼을 클릭하여 앱 서비스를 만들고 앱을 게시합니다.

   ![&#xC571; &#xC11C;&#xBE44;&#xC2A4; &#xB9CC;&#xB4E4;&#xAE30;](https://raw.githubusercontent.com/aspnet/Blazor.Docs/gh-pages/docs/tutorials/build-your-first-blazor-app/_static/blazor-publish-create-appservice2.png)

앱이 배포될 때까지 잠시 기다립니다.

이제 앱이 Azure에서 실행될 것입니다. 여러분의 첫 Blazor 앱 만들기라는 할 일 항목을 하나 만들고 _완료됨_ 으로 체크해보세요. 잘 하셨습니다!

![Azure&#xC5D0;&#xC11C; &#xC2E4;&#xD589;&#xB418;&#xB294; Blazor &#xC571;](https://user-images.githubusercontent.com/1874516/39512621-83406508-4da6-11e8-8244-5bae2ac6b22d.png)

**노트**

만약 Visual Studio를 사용하지 않고, Blazor 앱을 Windows, macOS, 또는 Linux의 명령줄을 사용하여 게시하고 배포하려면:

```text
dotnet publish -c Release
```

배포 아티팩트가 _/bin/Release/&lt;target-framework&gt;/publish_ 폴더에 만들어지게 됩니다. _publish_ 폴더 안의 콘텐츠를 서버나 여러분의 호스팅 서비스로 복사하도록 합니다.

더 자세한 정보는 [호스팅 및 배포](https://github.com/rkttu/asp-net-blazor-krguide/tree/3e61ef5c80612365f81919f605ec2574be9c2f08/tutorials/host-and-deploy/README.md) 토픽을 참고합니다.

## 추가 리소스    <a id="additional-resources"></a>

더 많은 Blazor의 기능을 사용하는 샘플 앱은, GitHub의 [Flight Finder 샘플](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor) 코드를 참고하세요.

![Blazor Flight Finder](https://msdnshared.blob.core.windows.net/media/2018/03/blazor-flight-finder.png)

