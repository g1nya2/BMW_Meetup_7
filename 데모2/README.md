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

이건 숫자의 제곱근을 계산하기 위해 만들어졌습니다.

이제 앱을 실행한다음, Call .NET code from JavaScript로 이동하셔서 `F12`를 통해 개발자 도구를 열어줍니다.

콘솔에 `DotNet`을 입력해 보겠습니다.

> `DotNet` : JavaScript에서 정적 C# 메서드를 호출하는 데 사용하는 객체.
> `invokeMethod`와 `invokeMethodAsync`라는 두 가지 속성이 포함되어 있으며, 이를 사용하여 정적 C# 메서드를 호출할 수 있습니다.
> `invokeMethod`는 동기적으로 C# 메서드를 호출. 즉, 호출이 완료될 때까지 JavaScript 코드 실행이 중단됩니다. 
> `invokeMethodAsync`는 비동기적으로 C# 메서드를 호출합니다. 이 방법은 호출이 완료될 때까지 JavaScript 코드 실행을 중단하지 않습니다.

<br/>

이제 `app.js`에 내용을 추가해주겠습니다.
```
var jsFunctions = {};
```
먼저 `jsFunction` 객체를 생성해줍니다. 이 객체는 JavaScript 함수들을 담는데 사용됩니다.
<br/>

```
jsFunctions.calculateSquareRoot = function () {}
```
`jsFunctions` 객체에 `calculateSquareRoot`라는 함수를 추가합니다. 이 함수는 사용자가 입력한 숫자의 제곱근을 계산하는 역할을 합니다.<br/>

```
const number = prompt("Enter your number");
```
`prompt` 함수를 사용하여 사용자로부터 숫자를 입력받습니다. `prompt` 함수는 입력된 값을 문자열로 반환합니다.<br/>

```
 DotNet.invokeMethodAsync("JsInteropDemo2", "CalculateSquareRoot", parseInt(number))
        .then(result => {
            var el = document.getElementById("string-result");
            el.innerHTML = result;
        });
```
`DotNet.invokeMethodAsync` 함수를 사용하여 C#의 CalculateSquareRoot 메서드를 비동기적으로 호출합니다.

> 첫 번째 인수: C# 메서드가 포함된 어셈블리 이름입니다 (BlazorWasmJSInteropExamples).
> 두 번째 인수: 호출할 C# 메서드 이름입니다 (CalculateSquareRoot).
> 세 번째 인수: C# 메서드에 전달할 매개변수입니다 (parseInt(number)). parseInt 함수를 사용하여 입력된 문자열을 정수로 변환합니다.

결론적으로 들어가야할 내용은 다음과 같습니다.
```
var jsFunctions = {};
jsFunctions.calculateSquareRoot = function () {
  const number = prompt("Enter your number");
  DotNet.invokeMethodAsync(
    "JsInteropDemo2",
    "CalculateSquareRoot",
    parseInt(number)
  ).then((result) => {
    var el = document.getElementById("string-result");
    el.innerHTML = result;
  });
};

```

<br/>

마지막으로 `CallDotNetFromJavaScript.razor` 파일을 수정해야 합니다.
```
<div class="row">
    <div class="col-md-4">
        <h4>Calling static method from JS</h4>
    </div>
    <div class="col-md-2">
        <button type="button" class="btn btn-success" onclick="jsFunctions.calculateSquareRoot()">
            Calculate
        </button>
    </div>
    <div class="col-md-4">
        <span id="string-result" class="form-text"></span>
    </div>
</div>
```
> .cs 파일에서 메서드를 호출하지 않기 때문에 `@onclick` 이벤트 핸들러를 사용하지 않는다는 것입니다. 우리가 하는 일은 자바스크립트 함수를 호출하는 것이므로 `onclick`이벤트 핸들러를 호출하게 됩니다.

<br/>
이제 실행시키고 프롬프트에 숫자를 입력하면 제곱근이 나오는 것을 확인할 수 있습니다.

 ##### JavaScript 에서 c#코드를 호출해보았습니다.
