## 데모 3 : Popper.js wrapping을 통해 Blazor 기능 극대화하기
Popper.js는 툴팁, 드롭다운 메뉴와 같은 요소들을 다른 참조 요소(reference element) 옆에 정확하고 쉽게 위치시키기 위한 라이브러리입니다.

<br/>

> *Popper.js wrapping을 통해 Blazor 기능 극대화하기 전반적인 흐름 
> 1. JavaScript 라이브러리 로드:
> Popper 라이브러리를 JavaScript로 로드하고 사용하기 위해 popper-wrapper.js 파일을 설정.
> index.html에서 Popper와 래퍼 파일을 로드하도록 설정.
> 2. Blazor 서비스 래핑:
> Blazor에서 Popper 라이브러리를 사용할 수 있도록 Popper 클래스를 생성하고, 이 클래스를 DI(종속성 주입) 컨테이너에 등록.
> 3. Blazor 컴포넌트에서 사용:
> Popper 클래스를 Blazor 컴포넌트에 주입하여 HTML 요소에 Popper를 적용.

<br/>

```
dotnet new blazorwasm -o PopperBlazorApp
cd PopperBlazorApp
```
새로운 Blazor WebAssembly 프로젝트를 생성합니다.

<br/>

Popper.js 라이브러리를 프로젝트에 추가하기 위해, wwwroot/index.html 파일을 수정합니다.

```
...
<script src="https://unpkg.com/@popperjs/core@2"></script>
</head>
```
Popper.js 라이브러리를 CDN을 통해 로드합니다.<br/>

`wwwroot` 폴더에 `popper-wrapper.js` 파일을 추가하고 다음 코드를 작성합니다.<br/>
```
window.PopperWrapper = {
  createPopper: function (reference, popper, options) {
    if (options != null) {
      Popper.createPopper(reference, popper, options);
    } else {
      Popper.createPopper(reference, popper);
    }
  },
};
```
JavaScript에서 Popper.js 라이브러리를 감싸는 단순한 래퍼(wrapper) 함수입니다.<br/>
`PopperWrapper` 객체를 `window`에 추가하고 그 안에 `createPopper`라는 메서드를 추가합니다. 이 메서드는 `Popper.js`의 `createPopper` 함수를 호출하는 역할을 합니다.

<br/>

js 래퍼 파일도 프로젝트에 추가하기 위해, wwwroot/index.html 파일을 수정합니다.
```
...
<script src="https://unpkg.com/@popperjs/core@2"></script>
<script src="popper-wrapper.js"></script>
</head>
```
js 래퍼 파일을 로드합니다.<br/>

Blazor에서 Popper를 사용하려면 Popper 클래스를 작성하여 JavaScript 래퍼와 상호작용할 수 있도록 합니다. Blazor 애플리케이션에서 JavaScript 상호 운용을 통해 Popper.js 라이브러리를 사용하는 방법을 보여줍니다.

`Popper.cs`라는 파일을 `Data` 폴더에 생성하고 다음 내용을 추가합니다:

```
using Microsoft.JSInterop;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
```
- `Microsoft.JSInterop` : Blazor에서 JavaScript와 상호 작용할 수 있게 함.
- `System.Threading.Tasks` : 비동기 프로그래밍을 지원.
- `Microsoft.AspNetCore.Components` : Blazor 컴포넌트의 렌더링, 이벤트 처리, DOM 조작 등을 지원.
  
네임 스페이스들을 추가.

```
public class Popper
{
    private readonly IJSRuntime _jsRuntime;

    public Popper(IJSRuntime jsRuntime)
    {
        _jsRuntime = jsRuntime;
    }

    public async Task CreatePopperAsync(ElementReference reference, ElementReference popper, object options = null)
    {
        await _jsRuntime.InvokeVoidAsync("PopperWrapper.createPopper", reference, popper, options);
    }
}
```

> Popper 클래스: Blazor와 Popper.js 간의 상호작용을 캡슐화하는 클래스.
> 먼저 `jSRuntime`를 주입하고 클래스의 생성자를 만듭니다.
> 비동기 메서드는 CreatePopperAsync로 명명하며, 이 메서드는 두 개의 ElementReference를 인수로 받습니다.
>>  ElementReference는 Blazor에서 HTML 요소를 참조하는 방식입니다.
> 그런 다음 InvokeVoidAsync 메서드를 사용하여 JavaScript 메서드를 호출합니다.
