메모리게임을 만들던 중 이벤트가 발생한 후 상태값을 초기화할 방법을 고심하던 중 상상도 못한 onAnimationEnd라는 이벤트가 있다는 것을 알게되었다.


## SyntheticEvent(합성이벤트)
리액트 공식문서에 따르면 이벤트 핸들러는 모든 브라우저에서 이벤트를 동일하게 처리하기 위한 이벤트 래퍼 SyntheticEvent 객체를 전달받습니다. stopPropagation() 와 preventDefault()를 포함해서 인터페이스는 브라우저의 고유 이벤트와 같지만 모든 브라우저에서 동일하게 동작합니다.

합성 이벤트는 브라우저 고유 이벤트에 직접 대응되지 않으며 다릅니다. 예를 들어 onMouseLeave에서 event.nativeEvent는 mouseout 이벤트를 가리킵니다. 구체적인 연결은 공개된 API의 일부가 아니며 언제든지 변경될 수 있습니다. 

### onAnimationStart, onAnimationEnd, onAnimationIteration
