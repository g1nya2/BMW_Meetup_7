## 데모 1 : C# 코드에서 JS 함수 호출하기

<br/>

```
dotnet new blazorwasm -o JsInteropDemo
```
터미널에 위 명령어를 실행하여 빈 Blazor WebAssembly 애플리케이션을 생성해줍니다.

<br/>

wwwroot 내부에  ` js `  라는 폴더를 만듭니다. <br/>
그 안에 새 js 파일을 만들고 파일이름을 `app.js`으로 지정해줍니다.
<br/>

```
function sayHello(name) {
    alert(`Hello ${name}`);
}
```
`app.js` 내부에 위와 같은 함수를 js 기본 함수를 넣어줍니다.<br/>
이름을 받아와서 경고문으로 간단히 띄워주는 함수입니다.<br/>

이제 `index.html` 에서 다음 줄을 추가해줍니다.<br/>

```
...
    <script src="js/app.js"></script> // 이 부분을 추가해줍니다.
    <script src="_framework/blazor.webassembly.js"></script>
</body>
```

이를 통해 Js 파일에 대한 참조를 추가합니다.<br/>
이제 Razor 파일에서 JS Interop을 사용하여 이 함수를 테스트할 수 있습니다.<br/>

우리는 `Home.razor` 파일에서 테스트를 진행할 것입니다.<br/>

Home.razor에서 `@page`지시문 아래에 다음과 같이 IJSRuntime를 넣고, 이름을 지정합니다.
```
@page "/"
@inject IJSRuntime Js
```
> `IJSRuntime`은 Blazor 프레임워크 내에서 JavaScript와 상호작용하기 위한 인터페이스입니다.

<br/>

IJSRuntime 인터페이스에는 두 가지 메서드가 있습니다.
> 1. ` InvokeAsync<TValue>` : 값을 반환하는 JavaScript 함수를 호출하는 데 사용
> 2. `InvokeVoidAsync` : 아무것도 반환하지 않는 함수, 즉 C#의 void 메서드에 사용
> 
> 두 메서드는 여러 인수를 공유하는데, 첫 번째 인수 identifier는 본질적으로 호출하려는 JS 함수의 이름입니다.
> 두 번째 인수는 함수에 매개변수를 제공하는 데 사용됩니다.

<br/>

```

@code {
    protected override async Task OnInitializedAsync()
    {
        await Js.InvokeVoidAsync("sayHello", args: "Geunhee");
    }
}
```
반환값이 필요하지 않기때문에 `InvokeVoidAsync` 메서드를 사용해주었습니다. <br/>

`OnInitializedAsync` 를 사용하여, sayHello function을 호출합니다. <br/>

변경 사항을 저장하고 이제 프로젝트를 실행하면 홈페이지가 처음 로드될 때 경고문이 뜨게 됩니다.<br/>

JS 함수가 C# 코드에서 올바르게 호출되었음을 보여줍니다.<br/>

---

이번엔 반환값이 사용되는 데모를 진행해보겠습니다. <br/>

```
function getMessage() {
    var message = prompt("Type in your message");
    return message;
}
```
`app.js`에 위 코드를 추가해줍니다.<br/>

이 ` prompt `함수를 사용해서 사용자의 입력 내용을 가져온 다음, 사용자가 입력한 내용을 반환하고 이를 페이지의 제목에 표시하도록 하겠습니다.<br/>

`Home.razor` 에서 이번에도 테스트를 해줄겁니다. 아까 작성했던 `@code`블록은 지워주고 아래 내용을 추가해줍니다.

```
...
<h2>@message</h2>

@code {
    private string? message;

    protected override async Task OnInitializedAsync()
    {
        message = await Js.InvokeAsync<string>("getMessage");
    }
}
```
이번엔 값을 반환해줘야하기 때문에, `InvokeAsync<string>` 메서드를 적용한 것을 볼 수 있습니다.<br/>

페이지가 로드되면 사이트에서 사용자에게 무언가를 입력하라는 메시지가 표시되고, 입력된 모든 내용은 다음과 같이 h2로 렌더링됩니다.<br/>
