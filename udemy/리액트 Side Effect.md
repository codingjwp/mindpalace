# Side Effect & useEffect

**Side Effect** 는 현재 컴포넌트 렌더링 주기에 영향을 주지 않는 작업입니다.

아래와 같이 UI에 대한 직접적인 변화를 주는 부분과  
**"사용자의 위치를 알아내는 코드"**에서 시간이 걸리며 UI에 변화를 주지 않는 코드를 `Side Effect`가 존재합니다


```javascript
function App() {
  const modal = useRef();
  const selectedPlace = useRef();
  const [pickedPlaces, setPickedPlaces] = useState([]);
  // 사용자의 위치를 알아내는 내용
  navigator.geolocation.getCurrentPosition((position) => {
    const sortedPlaces = sortPlacesByDistance(AVAILABLE_PLACES, position.coords.latitude, position.coords.longitude);
  });
  // ... 생략
```

## SideEffect 잘못된 사용법

아래코드는 SideEffect를 잘못 사용한 방법입니다.

이유는 컴포넌트에서 setState를 사용하는 것은 재렌더링을 발생 시킵니다.  
즉 위치를 알아내는 내부의 함수에 사용을 하여 위치값을 state에 저장할 경우  
다시 랜더링됨가 동시에 다시 위치를 알아내는 함수가 실행되서 **무한 loop**가 발생하게 됩니다.

```javascript
function App() {
  const [availablePlaces, setAvailablePlaces] = useState([]);
  // 사용자의 위치를 알아내는 내용
  navigator.geolocation.getCurrentPosition((position) => {
    const sortedPlaces = sortPlacesByDistance(AVAILABLE_PLACES, position.coords.latitude, position.coords.longitude);
    // useState에 위치값 저장
    setAvailablePlaces(sortedPlaces);
  });
  // ... 생략
}
```

## useEffect를 사용한 SideEffect 사용법

아래와 같은 방법을 사용할 경우 무한 loop에서 벗어납니다.

이유는 `useEffect`의 첫번째 함수가 실행되는 시점이 컴포넌트 함수가 실행이 완료된 후 실행됩니다.  
물론 `useEffect`에 있는 `setState`에 의해 다시 `useEffect`가 실행되겠지만 그런 경우 두번째 의존성 배열을  
정의하는 것으로 의존성 값이 변경 되었을 경우에만 재실행 하게됩니다.

```javascript
  const [availablePlaces, setAvailablePlaces] = useState([]);

  useEffect(() => {
    navigator.geolocation.getCurrentPosition((position) => {
      const sortedPlaces = sortPlacesByDistance(AVAILABLE_PLACES, position.coords.latitude, position.coords.longitude);
      setAvailablePlaces(sortedPlaces);
    });
  }, [])

```

## 모든 SideEffect에 useEffect를 사용안하는 이유

무한 루프를 방지하거나 컴포넌트 함수가 한번은 실행된 이후에 작동이 가능한 코드가 있을 때만 사용합니다.

- 첫 번쨰: 상호작용을 이용한 Side Effect가 존재할때는 useEffect를 사용하지 앖습니다.

```javascript
// 이미지 클릭시 발생하는 이벤트
function handleSelectPlace(id) {
  setPickedPlaces((prevPickedPlaces) => {
    if (prevPickedPlaces.some((place) => place.id === id)) {
      return prevPickedPlaces;
    }
    const place = AVAILABLE_PLACES.find((place) => place.id === id);
    return [place, ...prevPickedPlaces];
  });
  // Side Effect 부분
  const storedIds = JSON.parse(localStorage.getItem('selectedPlaces')) || [];
  if (storedIds.indexOf(id) === -1) {
    localStorage.setItem('selectedPlaces', JSON.stringify([id, ...storedIds]))
  }
}
```

- 두 번째: 아래의 `useEffect`같은 경우 안의 내용은 즉시 완료가 되기 때문에 `useEffect`에 넣을 필요가 없습니다.

```javascript
// 저장한 localStorage의 정보를 불러오는 side Effect
useEffect(() => {
  const storedIds = JSON.parse(localStorage.getItem('selectedPlaces')) || [];
  const storedPlaces = storedIds.map((id) => 
  AVAILABLE_PLACES.find((place) => place.id === id)
  )
  setPickedPlaces(storedPlaces);
}, [])

// 수정 후: useEffect를 지우고 setState위에 작성한 코드
const storedIds = JSON.parse(localStorage.getItem('selectedPlaces')) || [];
const storedPlaces = storedIds.map((id) =>  AVAILABLE_PLACES.find((place) => place.id === id));

const [pickedPlaces, setPickedPlaces] = useState(storedPlaces);
```

