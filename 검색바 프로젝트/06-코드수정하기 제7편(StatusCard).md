# StatusCard의 차트 부분 수정

차트 부분이 존재해서 파일 코드가 보기 힘들어서 따로 Hook으로 빼놓을려고 합니다.  
아래의 코드는 원본으로 `useEffect`에 적용해여 `canvas`에 적용해 놓았습니다.

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
