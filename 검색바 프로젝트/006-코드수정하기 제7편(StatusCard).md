# StatusCard의 차트

## Chart.js사용 부분

~~차트 부분이 존재해서 파일 코드가 보기 힘들어서 따로 Hook으로 빼놓을려고 합니다.  
아래의 코드는 원본으로 `useEffect`에 적용해여 `canvas`에 적용해 놓았습니다.~~  

아래 코드는 화면 상 보기별로 좋지 않아 bar형태의 차트로 변경했으며 사용했던 chat.js 라이브러리는 삭제하여 없애고 progress로 꾸몄습니다.

```typescript
  useEffect(() => {
    if (canvasRef) {
      const canvas = canvasRef.current as ChartItem;
      const status = [hp, attack, defense, spattack, spdefense, speed];
      const data: ChartConfiguration = {
        type: 'doughnut',
        data: {
          labels: ['Hp', 'Attack', 'Defense', 'Spattack', 'Spdefense', 'Speed'],
          datasets: [
            {
              label: 'status',
              data: status.map(Number),
              backgroundColor: [
                '#d2381d',
                '#2b7fd3',
                '#d1a72a',
                '#5da042',
                '#8b457d',
                '#d2477f',
              ],
              hoverOffset: 30,
            },
          ],
        },
        options: {
          responsive: true,
          layout: {
            padding: {
              left: 20,
              right: 20,
              top: 20,
              bottom: 20,
            },
          },
          plugins: {
            legend: {
              display: false,
            },
            tooltip: {
              enabled: true,
            },
          },
        },
      };
      const chart = new Chart(canvas, data);
      return () => chart.destroy();
    }
  }, []);
```

`useChart`를 생성하여 ref값이랑 배열을 매개변수로 전달하여 적용하였고 ref가 빈 값일 경우 리턴하도록 하였습니다.

```typescript
import { useEffect } from 'react';
import { Chart, ChartConfiguration, ChartItem } from 'chart.js';

export const useChart = (
  type: string[],
  ref: React.RefObject<HTMLCanvasElement>,
) => {
  useEffect(() => {
    if (!ref) return;
    const canvas = ref.current as ChartItem;
    const data: ChartConfiguration = {
    /**... 생략 */
      data: {
        labels: ['Hp', 'Attack', 'Defense', 'Spattack', 'Spdefense', 'Speed'],
        datasets: [{
          label: 'status',
          data: type.map(Number),
    /**... 생략 */
        }]
      }
    }
  }, []);
};
```

## Progress 태그를 사용한 코드

status 이름과 값을 가져오는 progress를 만들기 때문에 코드와 같이 타입을 지정하였습니다.

```typescript
interface StatusProps {
  label: string;
  value: string;
}
```

**특정 수치마다 색상이 다른** style을 주기 위해 코드와 같이 수치에 따른 설정을 하였습니다.

```typescript
const StatusProgress = ({label, value}: StatusProps) => {
  const test = cn(styles.progress_status, {
    [styles.fifty_under]: Number(value) < 50,
    [styles.eighty_under]: Number(value) >= 50 && Number(value) < 80,
    [styles.hundred_under]: Number(value) >= 80 && Number(value) < 100,
    [styles.hundred_more]: Number(value) >= 100,
  });
  // ... 생략
}
```

**250으로 최대 값을 주었으며** label과 값을 표시하였습니다.

```typescript
  return (
    <div className={styles.progress_cover}>
      <em className={styles.progress_title}>{label} : </em>
      <div className={styles.progress_main}>
        <progress
          aria-label={label}
          className={test}
          value={value}
          max={'250'}></progress>
        <span className={styles.progress_value}>{value}</span>
      </div>
    </div>
  );
```

## progress 스타일

아래와 같이 기본적으로 네모만 progress를 둥글게 만들기 위한 부분과 기본 색상값을 주기위해 설정 하였습니다. 기본적으로 Chrome, Safari를 기준으로 하고 파이어폭스도 넣었습니다.

```css
.progress_status[value]::-webkit-progress-bar {
  background-color: var(--base-ball-bottom);
}

.progress_status[value]::-webkit-progress-bar,
.progress_status[value]::-webkit-progress-value {
  border-radius: 10rem;
}
/* 파이어 폭스*/
.progress_status[value],
.progress_status[value]::-moz-progress-bar {
  border-radius: 10rem;
}
```

해당 코드는 각 수치마다 표실되는 색상 css 코드 입니다.

```css
.fifty_under::-moz-progress-bar {
  background-color: #ff7f0f;
}
.fifty_under::-webkit-progress-value {
  background-color: #ff7f0f;
}
.eighty_under::-webkit-progress-value {
  background-color: #ca15e1;
}
.eighty_under::-moz-progress-bar {
  background-color: #ca15e1;
}
.hundred_under::-webkit-progress-value {
  background-color: #422ec4;
}
.hundred_under::-moz-progress-bar {
  background-color: #422ec4;
}
.hundred_more::-webkit-progress-value {
  background-color: #23cd5e;
}
.hundred_more::-moz-progress-bar {
  background-color: #23cd5e;
}
```
