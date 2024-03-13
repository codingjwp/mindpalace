# CSS 애니메이션

## Framer moction

- **animate:** 애니메이션 관련
  - **x:** x축 이동
  - **y:** y축 이동
  - **rotate:** 회전
- **transition:** 바운드, 이동 시간 등 관련
  - **duration:** 완료까지 걸리는 시간
  - **bounce:** 튕김 정도
  - **type:** 애니메이션 동안 사용될 타입
    - spring: 기본적인 튕김 적용
    - tween: 튕김 없애기

```javascript
import { useState } from 'react';
import {motion} from 'framer-motion;

function App() {

  const [x, setX] = useState(0);
  const [y, setY] = useState(0);
  const [rotate, setRotate] = useState(0);

  return (
    <motion.div id="box" animate={{
      x: x,
      y: y,
      rotate: rotate
    }}
    transition={{
      duration: 0.3,
      bounce: 1, \
      type: spring
    }}
    >
    </motion.div>
  )
}
```

## 조건 값에서 애니메이션 넣기

props에 따른 조건 값 추가

```javascript
<motion.span
	animate={{ rotate: isExpanded ? 180 : 0 }}
	className='challenge-item-details-icon'>
	&#9650;
</motion.span>
```

## Dom에 추가되자 마자 곧바로 재생되는 애니메이션

- **initial:**: Dom에 추가되는 순간 실행되는 애니메이션

```javascript
export default function Modal({ title, children, onClose }) {
	return createPortal(
		<>
			<div className='backdrop' onClick={onClose} />
			<motion.dialog
				initial={{ opacity: 0, y: 30 }}
				animate={{ opacity: 1, y: 0 }}
				open
				className='modal'>
				<h2>{title}</h2>
				{children}
			</motion.dialog>
		</>,
		document.getElementById('modal')
	);
}
```

## 요소가 사라지는 애니메이션 넣기

- **exit:** 는 애니메이션이 종료될 떄 진행되는 애니메이션

위에 애니메이션을 사용해도 jsx를 조건으로 나타나게 했다가 삭제하는 기능이 있으면 적용이 안되는 문제가 있습니다.

```javascript
<motion.dialog exit={{ opacity: 0, y: 30 }} open className='modal'>
	<h2>{title}</h2>
	{children}
</motion.dialog>
```

- **AnimatePresence:** 조건으로 나타나는 jsx경우 해당 태크로 적용시 `exit`옵션을 먼저 실행 시키고 태그를 비활성화 합니다.

```javascript
import { AnimatePresence } from 'framer-motion';

export default function Header() {
	return (
		<AnimatePresence>
			{isCreatingNewChallenge && <NewChallenge onDone={handleDone} />}
		</AnimatePresence>
	);
}
```

## 마우스 호버시 애니메이션 적용

- **whileHover:** 마우스가 호버되었을때 적용
- **transition**
  - **siffness:** 강성 튀는 효과 크기

```javascript
<motion.button
	whileHover={{ scale: 1.1 }}
	transition={{ type: 'spring', stiffness: 500 }}
	onClick={handleStartAddNewChallenge}
	className='button'>
	Add Challenge
</motion.button>
```

## 애니메이션 재사용하기

```javascript
<motion.dialog
	variants={{
		hidden: { opacity: 0, y: 30 },
		visible: { opacity: 1, y: 0 },
	}}
	initial='hidden'
	animate='visible'
	exit='hidden'
	open
	className='modal'>
	<h2>{title}</h2>
	{children}
</motion.dialog>
```

## 중첩 애니메이션 벨리언트

벨리언트는 래퍼나 부모에서 벨리언트를 선언하여 자식한테 전달할 수 있습니다.

## staggering

스태거링 : 하나씩 차례대로 애니메이션화되는 기법

`transition`은 다른 animate나 exit에도 사용이 가능합니다.

```javascript
<motion.ul
	id='new-challenge-images'
	variants={{
		visible: { transition: { staggerChildren: 0.05 } },
	}}>
	{/* ... 스태거링 쓰고 싶은 리스트들 */}
</motion.ul>
```

## 명령적 접근법으로 애니메이션 구현

motion으로 직접적으로 애니메이션을 넣어주는 것이 아니라 특정 상황에 애니메이션을 넣어주는 방식입니다.

- **useAnimate:** 명략적 접근법으로 애니메이션 구현하기 위한 부분입니다.
	- **scope:** Ref와 같은 역활 animate에 지정한 element들이 어떠한 특정 구역만 가능하게 만들기 위해 지정합니다.
	- **animate:** 애니메이션이 적용되는 구역과 어떠한 애니메이션을 할 건지 작성하는 곳 입니다.

```javascript
import { stagger, useAnimate } from 'framer-motion';

export default function NewChalleng() {
	const [scope, animate] = useAnimate();
}

function handleSubmit(event) {
	// .. 생략
	if (
		!challenge.title.trim() ||
		!challenge.description.trim() ||
		!challenge.deadline.trim() ||
		!challenge.image
	) {
		animate(
			'input, textarea',
			{ x: [-10, 0, 10, 0] },
			{ type: 'spring', duration: 0.2, delay: stagger(0.05) }
		);
		return;
	}
	// ... 생략
}

return (
	<form id='new-challenge' onSubmit={handleSubmit} ref={scope}>
		<p>
			<label htmlFor='title'>Title</label>
			<input ref={title} type='text' name='title' id='title' />
		</p>

		<p>
			<label htmlFor='description'>Description</label>
			<textarea ref={description} name='description' id='description' />
		</p>

		<p>
			<label htmlFor='deadline'>Deadline</label>
			<input ref={deadline} type='date' name='deadline' id='deadline' />
		</p>
		// .. 생략
	</form>
);
```

## 레이아웃에 변화 주기

- **layout:** 레이아웃에 자동으로 변화가 오면 애니메이션의 효과를 주는 속성업니다.

```javascript

return (
	<motion.li layout>
		{/* ... 생략*/}
	</motion.li>
)

```