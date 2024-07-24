## 데모 2 : JavaScript에서 C# 코드 호출하기
<br/>

```
dotnet new blazorwasm -o JsInteropDemo2
```
터미널에 위 명령어를 실행하여 빈 Blazor WebAssembly 애플리케이션을 생성해줍니다.
<br/>

wwwroot 내부에  ` js `  라는 폴더를 만듭니다. <br/>
그 안에 새 js 파일을 만들고 파일이름을 `app.js`으로 지정해줍니다.
<br/>

이제 `index.html` 에서 다음 줄을 추가해줍니다.<br/>

```
...
    <script src="js/app.js"></script> // 이 부분을 추가해줍니다.
    <script src="_framework/blazor.webassembly.js"></script>
</body>
```

이를 통해 Js 파일에 대한 참조를 추가합니다.<br/>
이제 JavaScript에서 C# 메서드를 호출하는 방법을 볼 수 있습니다<br/>

`Page` 폴더 내부에 `CallDotNetFromJavaScript.razor`, `CallDotNetFromJavaScript.razr.cs`파일을 만듭니다.<br/>

.razor 파일을 수정하여 경로 지시문과 제목을 추가해 보겠습니다.<br/>
```
@page "/dotnetinjs"
<h2>Call DotNet From JavaScript</h2>
<br />
```

이제 이 파일을 `Home.razor` 파일에 링크로 달아줍니다.
```
<ul>
    <li>
        <a href="/dotnetinjs">
            How to call .NET code from JavaScript
        </a>
    </li>
</ul>
```

#### 지금부터 본격적으로 JavaScript에서 C# 코드 호출하기를 JavaScript에서 정적 C# 메서드 호출을 통해 해보겠습니다.

`CallDotNetFromJavaScript.razor.cs` 파일에 새로운 정적 메서드를 만들어줍니다.

```
using Microsoft.JSInterop;

public partial class CallDotNetFromJavaScript
{
    [JSInvokable]
    public static string CalculateSquareRoot(int number)
    {
        var result = Math.Sqrt(number);
        return $"The square root of {number} is {result}";
    }
}
```
- `JSInvokable` 속성은 Microsoft.JSInterop 네임스페이스에 정의되어 있습니다.
- `JSInvokable`: 메서드가 JavaScript에서 호출 가능하다는 것을 나타냅니다. 이 속성이 붙은 메서드는 JavaScript 코드에서 호출할 수 있습니다.

이건 숫자의 제곱근을 계산하는 아주 기본적인 논리입니다. 
