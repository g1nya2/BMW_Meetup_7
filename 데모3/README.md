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
> 이 부분이 Popper.js 라이브러리를 래핑(wrapping)하는 단계입니다.
<br/>

js 래퍼 파일도 프로젝트에 추가하기 위해, wwwroot/index.html 파일을 수정합니다.
```
...
<script src="https://unpkg.com/@popperjs/core@2"></script>
<script src="popper-wrapper.js"></script>
</head>
```
js 래퍼 파일을 로드합니다.<br/>

