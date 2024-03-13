# 데이터베이스 연결 및 HTTP 요청

리액트에서는 직접적으로 데이터베이스를 연결하지 않습니다.

왜냐하면 방문자들이 직접적으로 코드를 볼 수 있으며 그 코드에 접속할 수 있기 때문입니다.

리액트에서 데이터베이스를 사용할 순 없지만 벡엔드를 통해 API를 사용하여 HTTP통신을 할 수 있습니다.

> API는 "application programming interface(애플리케이션 프로그래밍 인터페이스)"의 약자입니다.  
> "REST API"는 HTTP 요청을 전송할 수 있는 사전 정의된 특정 경로를 노출하는 웹 서버입니다.  

## HTTP 요청을 보낼때는 ..

3가지 `isFetch`, `data`, `error` 부분이 같이 합니다.

```javascript
  const [isFetching, setIsFetching] = useState(false);
  const [availablePlaces, setAvailablePlaces] = useState([]);
  const [error, setError] = useState();

  useEffect(() => {
    async function featchPlaces() {
      setIsFetching(true);
      try {
        const places = await featchAvailablePlaces();
        navigator.geolocation.getCurrentPosition((position) => {
          const sortedPlaces = sortPlacesByDistance(places, position.coords.latitude, position.coords.longitude);
          setAvailablePlaces(sortedPlaces)
        })
      } catch (error) {
        setError({ message: error.message || 'Could not fetch places, please try again later.' });
      } finally {
        setIsFetching(false);
      }
    }
    featchPlaces();
  });
```

## 낙관적 업데이트

로딩 스피너나 로딩 메세지를 띄우는 게 아닌 데이터를 불러오기 전에 UI에대 해안 업데이트를 먼저 하고 에러 발생시 원본 UI로 돌리는 형태입니다.

```javascript
async function handleSelectPlace(selectedPlace) {
  setUserPlaces((prevPickedPlaces) => {
    if (!prevPickedPlaces) {
      prevPickedPlaces = [];
    }
    if (prevPickedPlaces.some((place) => place.id === selectedPlace.id)) {
      return prevPickedPlaces;
    }
    return [selectedPlace, ...prevPickedPlaces];
  });
  try {
    await updateUserPlaces([selectedPlace, ...userPlaces]);
  }catch(error) {
    setUserPlaces(userPlaces);
    setErrorUpdatingPlaces({
      message: error.message || 'Fail to update places.'
    })
  }
}
```