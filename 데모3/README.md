## 데모 3 : Popper.js wrapping을 통해 Blazor 기능 극대화하기

<br/>

> *Popper.js wrapping을 통해 Blazor 기능 극대화하기 전반적인 흐름 
> 1. JavaScript 라이브러리 로드:
> Popper 라이브러리를 JavaScript로 로드하고 사용하기 위해 popper-wrapper.js 파일을 설정했습니다.
> index.html에서 Popper와 래퍼 파일을 로드하도록 설정했습니다.
> 2. Blazor 서비스 래핑:
> Blazor에서 Popper 라이브러리를 사용할 수 있도록 Popper 클래스를 생성하고, 이 클래스를 DI(종속성 주입) 컨테이너에 등록했습니다.
> 3. Blazor 컴포넌트에서 사용:
> Popper 클래스를 Blazor 컴포넌트에 주입하여 HTML 요소에 Popper를 적용하도록 했습니다.

<br/>

```
dotnet new blazorwasm -o PopperBlazorApp
cd PopperBlazorApp
```
