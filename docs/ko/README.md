<p align="center">
  <a href="https://xstate.js.org">
  <br />

  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/statelyai/public-assets/main/logos/xstate-logo-white-nobg.svg">
    <img alt="XState logotype" src="https://raw.githubusercontent.com/statelyai/public-assets/main/logos/xstate-logo-black-nobg.svg" width="200">
  </picture>
  <br />
    <sub><strong>JavaScript state machines and statecharts</strong></sub>
  <br />
  <br />
  </a>
</p>

[![npm version](https://badge.fury.io/js/xstate.svg)](https://badge.fury.io/js/xstate)
<img src="https://opencollective.com/xstate/tiers/backer/badge.svg?label=sponsors&color=brightgreen" />

XState 는 모던 웹을 위한 자바스크립트, 타입스크립트 용 [유한 상태 머신](https://en.wikipedia.org/wiki/Finite-state_machine) 및 [상태 차트](https://www.sciencedirect.com/science/article/pii/0167642387900359/pdf) 입니다.

📖 [문서 보기](https://xstate.js.org/docs)

💙 [XState 예제 보기](https://xstate-catalogue.com/)

➡️ [Stately Editor 를 활용해 상태 머신 만들기](https://stately.ai/editor)

🖥 [XState 용 VS Code 익스텐션 다운로드](https://marketplace.visualstudio.com/items?itemName=statelyai.stately-vscode)

📑 [SCXML 스펙](https://www.w3.org/TR/scxml/) 준수사항 확인

💬 [Stately 디스코드 커뮤니티](https://discord.gg/xstate) 참여

## 패키지

- 🤖 `xstate` - 코어 상태머신 및 상태차트 라이브러리 + 인터프리터
- [🔬 `@xstate/fsm`](https://github.com/statelyai/xstate/tree/main/packages/xstate-fsm) - 최소수준의 유한 상태 머신 라이브러리
- [📉 `@xstate/graph`](https://github.com/statelyai/xstate/tree/main/packages/xstate-graph) - XState 를 위한 그래프 탐색 유틸리티
- [⚛️ `@xstate/react`](https://github.com/statelyai/xstate/tree/main/packages/xstate-react) - React 어플리케이션에서 XState 를 활용하기 위한 hooks 및 유틸리티 모음
- [💚 `@xstate/vue`](https://github.com/statelyai/xstate/tree/main/packages/xstate-vue) - Vue 어플리케이션에서 XState 를 활용하기 위한 Vue 컴포지션 함수 및 유틸리티 모음
- [🎷 `@xstate/svelte`](https://github.com/statelyai/xstate/tree/main/packages/xstate-svelte) - Svelte 어플리케이션에서 XState 를 활용하기 위한 유틸리티 모음
- [✅ `@xstate/test`](https://github.com/statelyai/xstate/tree/main/packages/xstate-test) - Model-Based-Testing utilities (using XState) for testing any software
- [🔍 `@xstate/inspect`](https://github.com/statelyai/xstate/tree/main/packages/xstate-inspect) - XState 용 검사도구 라이브러리

## 템플릿

[CodeSandbox](https://codesandbox.io/) 로 작성된 아래 템플릿 중 하나를 통해 프로젝트를 시작해보세요:

- [XState](https://codesandbox.io/s/xstate-example-template-m4ckv) - 프레임워크 없음
- [XState + TypeScript](https://codesandbox.io/s/xstate-typescript-template-s9kz8) - 프레임워크 없음
- [XState + React Template](https://codesandbox.io/s/xstate-react-template-3t2tg)
- [XState + React + TypeScript Template](https://codesandbox.io/s/xstate-react-typescript-template-wjdvn)
- [XState + Vue Template](https://codesandbox.io/s/xstate-vue-template-composition-api-1n23l)
- [XState + Vue 3 Template](https://codesandbox.io/s/xstate-vue-3-template-vrkk9)
- [XState + Svelte Template](https://codesandbox.io/s/xstate-svelte-template-jflv1)

## 빠르게 살펴보기

```bash
npm install xstate
```

```js
import { createMachine, interpret } from 'xstate';

// 머신 정의
const toggleMachine = createMachine({
  id: 'toggle',
  initial: 'inactive',
  states: {
    inactive: { on: { TOGGLE: 'active' } },
    active: { on: { TOGGLE: 'inactive' } }
  }
});

// 내부 상태를 가진 머신 인스턴스
const toggleService = interpret(toggleMachine)
  .onTransition((state) => console.log(state.value))
  .start();
// => 'inactive'

toggleService.send('TOGGLE');
// => 'active'

toggleService.send('TOGGLE');
// => 'inactive'
```

## Promise 예제

[📉 stately.ai/viz 에서 보기](https://stately.ai/viz?gist=bbcb4379b36edea0458f597e5eec2f91)

<details>
<summary>코드 펼치기</summary>

```js
import { createMachine, interpret, assign } from 'xstate';

const fetchMachine = createMachine({
  id: 'Dog API',
  initial: 'idle',
  context: {
    dog: null
  },
  states: {
    idle: {
      on: {
        FETCH: 'loading'
      }
    },
    loading: {
      invoke: {
        id: 'fetchDog',
        src: (context, event) =>
          fetch('https://dog.ceo/api/breeds/image/random').then((data) =>
            data.json()
          ),
        onDone: {
          target: 'resolved',
          actions: assign({
            dog: (_, event) => event.data
          })
        },
        onError: 'rejected'
      },
      on: {
        CANCEL: 'idle'
      }
    },
    resolved: {
      type: 'final'
    },
    rejected: {
      on: {
        FETCH: 'loading'
      }
    }
  }
});

const dogService = interpret(fetchMachine)
  .onTransition((state) => console.log(state.value))
  .start();

dogService.send('FETCH');
```

</details>

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

- [시각화도구](#%EC%8B%9C%EA%B0%81%ED%99%94%EB%8F%84%EA%B5%AC)
- [왜 XState 인가?](#%EC%99%9C-xstate-%EC%9D%B8%EA%B0%80)
- [유한 상태 머신](#%EC%9C%A0%ED%95%9C-%EC%83%81%ED%83%9C-%EB%A8%B8%EC%8B%A0)
- [계층적인 (중첩된) 상태 머신](#%EA%B3%84%EC%B8%B5%EC%A0%81%EC%9D%B8-%EC%A4%91%EC%B2%A9%EB%90%9C-%EC%83%81%ED%83%9C-%EB%A8%B8%EC%8B%A0)
- [병렬 상태 머신](#%EB%B3%91%EB%A0%AC-%EC%83%81%ED%83%9C-%EB%A8%B8%EC%8B%A0)
- [상태 히스토리](#%EC%83%81%ED%83%9C-%ED%9E%88%EC%8A%A4%ED%86%A0%EB%A6%AC)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## 시각화도구

**[XState Viz 를 통해 상태차트를 시각화, 시뮬레이션, 검사, 공유하세요](https://stately.ai/viz)**

<a href="https://stately.ai/viz" title="XState Viz">
  <img src="https://user-images.githubusercontent.com/1093738/131729181-5db835fc-77e7-4740-b03f-46bd0093baa1.png" alt="XState Viz" width="400" />
</a>

## 왜 XState 인가?

상태차트란 상태관리 및 리액티브한 시스템을 모델링하기 위해 만들어진 형식입니다. 이런 형식은 당신의 프로젝트 내 개별적인 컴포넌트에서 프로젝트 전반에 걸치기까지의 _행동들_ 을 선언적으로 설명하는데 유용합니다.

[📽 이 슬라이드](http://slides.com/davidkhourshid/finite-state-machines) 와 ([🎥 영상](https://www.youtube.com/watch?v=VU1NKX6Qkxc)) 을 확인해보거나, 아래 자료들을 통해 당신이 만들 인터페이스에서 유한상태머신 과 상태차트가 얼마나 유용할지 알아보시기 바랍니다.

- [Statecharts - A Visual Formalism for Complex Systems](https://www.sciencedirect.com/science/article/pii/0167642387900359/pdf) by David Harel
- [The World of Statecharts](https://statecharts.github.io/) by Erik Mogensen
- [Pure UI](https://rauchg.com/2015/pure-ui) by Guillermo Rauch
- [Pure UI Control](https://medium.com/@asolove/pure-ui-control-ac8d1be97a8d) by Adam Solove
- [Spectrum - Statecharts Community](https://spectrum.chat/statecharts) (For XState specific questions, please use the [GitHub Discussions](https://github.com/statelyai/xstate/discussions))

## 유한 상태 머신

<a href="https://stately.ai/viz/2ac5915f-789a-493f-86d3-a8ec079773f4" title="Finite states">
  <img src="https://user-images.githubusercontent.com/1093738/131727631-916d28a7-1a40-45ed-8636-c0c0fc1c3889.gif" alt="Finite states" width="400" />
  <br />
  <small>Stately Viz 로 열기</small>
</a>
<br />

```js
import { createMachine } from 'xstate';

const lightMachine = createMachine({
  id: 'light',
  initial: 'green',
  states: {
    green: {
      on: {
        TIMER: 'yellow'
      }
    },
    yellow: {
      on: {
        TIMER: 'red'
      }
    },
    red: {
      on: {
        TIMER: 'green'
      }
    }
  }
});

const currentState = 'green';

const nextState = lightMachine.transition(currentState, 'TIMER').value;

// => 'yellow'
```

## 계층적인 (중첩된) 상태 머신

<a href="https://stately.ai/viz/d3aeda4f-7f8e-44df-bdf9-dd3cdafb3312" title="Hierarchical states">
  <img src="https://user-images.githubusercontent.com/1093738/131727794-86b63c76-5ee0-4d73-b84c-6992a1f0814e.gif" alt="Hierarchical states" width="400" />
  <br />
  <small>Stately Viz 로 열기</small>
</a>
<br />

```js
import { createMachine } from 'xstate';

const pedestrianStates = {
  initial: 'walk',
  states: {
    walk: {
      on: {
        PED_TIMER: 'wait'
      }
    },
    wait: {
      on: {
        PED_TIMER: 'stop'
      }
    },
    stop: {}
  }
};

const lightMachine = createMachine({
  id: 'light',
  initial: 'green',
  states: {
    green: {
      on: {
        TIMER: 'yellow'
      }
    },
    yellow: {
      on: {
        TIMER: 'red'
      }
    },
    red: {
      on: {
        TIMER: 'green'
      },
      ...pedestrianStates
    }
  }
});

const currentState = 'yellow';

const nextState = lightMachine.transition(currentState, 'TIMER').value;
// => {
//   red: 'walk'
// }

lightMachine.transition('red.walk', 'PED_TIMER').value;
// => {
//   red: 'wait'
// }
```

**상태 변경 확인:**

```js
// ...
const waitState = lightMachine.transition({ red: 'walk' }, 'PED_TIMER').value;

// => { red: 'wait' }

lightMachine.transition(waitState, 'PED_TIMER').value;

// => { red: 'stop' }

lightMachine.transition({ red: 'stop' }, 'TIMER').value;

// => 'green'
```

## 병렬 상태 머신

<a href="https://stately.ai/viz/9eb9c189-254d-4c87-827a-fab0c2f71508" title="Parallel states">
  <img src="https://user-images.githubusercontent.com/1093738/131727915-23da4b4b-5e7e-46ea-9c56-5093e37e60e6.gif" alt="Parallel states" width="400" />
  <br />
  <small>Stately Viz 로 열기</small>
</a>
<br />

```js
const wordMachine = createMachine({
  id: 'word',
  type: 'parallel',
  states: {
    bold: {
      initial: 'off',
      states: {
        on: {
          on: { TOGGLE_BOLD: 'off' }
        },
        off: {
          on: { TOGGLE_BOLD: 'on' }
        }
      }
    },
    underline: {
      initial: 'off',
      states: {
        on: {
          on: { TOGGLE_UNDERLINE: 'off' }
        },
        off: {
          on: { TOGGLE_UNDERLINE: 'on' }
        }
      }
    },
    italics: {
      initial: 'off',
      states: {
        on: {
          on: { TOGGLE_ITALICS: 'off' }
        },
        off: {
          on: { TOGGLE_ITALICS: 'on' }
        }
      }
    },
    list: {
      initial: 'none',
      states: {
        none: {
          on: { BULLETS: 'bullets', NUMBERS: 'numbers' }
        },
        bullets: {
          on: { NONE: 'none', NUMBERS: 'numbers' }
        },
        numbers: {
          on: { BULLETS: 'bullets', NONE: 'none' }
        }
      }
    }
  }
});

const boldState = wordMachine.transition('bold.off', 'TOGGLE_BOLD').value;

// {
//   bold: 'on',
//   italics: 'off',
//   underline: 'off',
//   list: 'none'
// }

const nextState = wordMachine.transition(
  {
    bold: 'off',
    italics: 'off',
    underline: 'on',
    list: 'bullets'
  },
  'TOGGLE_ITALICS'
).value;

// {
//   bold: 'off',
//   italics: 'on',
//   underline: 'on',
//   list: 'bullets'
// }
```

## 상태 히스토리

<a href="https://stately.ai/viz/33fd92e1-f9e6-49e6-bdeb-cef9e60195ec" title="History states">
  <img src="https://user-images.githubusercontent.com/1093738/131728111-819cc824-9881-4ecf-948a-00c1162cd9e9.gif" alt="History state" width="400" />
  <br />
  <small>Stately Viz 로 열기</small>
</a>
<br />

```js
const paymentMachine = createMachine({
  id: 'payment',
  initial: 'method',
  states: {
    method: {
      initial: 'cash',
      states: {
        cash: { on: { SWITCH_CHECK: 'check' } },
        check: { on: { SWITCH_CASH: 'cash' } },
        hist: { type: 'history' }
      },
      on: { NEXT: 'review' }
    },
    review: {
      on: { PREVIOUS: 'method.hist' }
    }
  }
});

const checkState = paymentMachine.transition('method.cash', 'SWITCH_CHECK');

// => State {
//   value: { method: 'check' },
//   history: State { ... }
// }

const reviewState = paymentMachine.transition(checkState, 'NEXT');

// => State {
//   value: 'review',
//   history: State { ... }
// }

const previousState = paymentMachine.transition(reviewState, 'PREVIOUS').value;

// => { method: 'check' }
```

## SemVer 정책

우리는 공적인 계약을 이해하고 있으며, 마이너 및 패치 버전 변경시 **런타임 API** 의 변경이 릴리즈 되지는 않을 것입니다.  
XState 라이브러리 수정시 기존 사용자에게 미치는 영향을 최소화하기 위해 노력합니다.

### 주요 변경 사항

XState 는 많은 자체 로직을 수행합니다. 그렇기 때문에 거의 모든 변경점은 주요 변경 사항이 될 수 있습니다. 우리 또한 이런 부분이 잠재적 문제가 될 수 있음을 인지하고 있으나, 그렇다고 모든 변경점을 주요 변경이라고 취급하는 것은 유용하지 않다고 생각합니다.  
우리는 사용자가 더 좋은, 안전한 방향으로 로직을 구현할 수 있도록 신규기능 추가는 세심하게 다룰 것입니다.

어떤 변경이든 머신의 부분적인 설정이라도 사용하는 경우, 기존에 사용하고 있던 XState 머신의 동작에 영향을 줄 수 있습니다. 우린 기존 머신에 영향을 주는 변경사항을 변덕스럽게 도입하지는 않을 것이며, 미치는 영향도 최소화 하는 것을 목표로 합니다. 하지만 우리는 마이너 릴리즈에 대해서는 _일부_ 동작을 변경할 수 있습니다. 상황에 따른 변경사항의 적용법 등은 항상 알려드리겠습니다. 업그레이드 전에 릴리즈 정보를 읽기 바랍니다.

### TypeScript 변경점

우리는 또한 이미 선언된 타입스크립트 정의를 변경하거나 마이너 릴리즈에서 구 타입스크립트 버전의 지원을 중단할 권한이 있습니다. 타입스크립트 언어는 빠르게 변화하며, 종종 마이너 릴리즈에서 주요 변경사항이 나오기도 합니다. 우리 팀 또한 타입스크립트를 더욱 효과적으로 활용하는 방법을 배우고 계속해서 더 나은 타입을 제공하기 위해 노력할 것입니다.

이러한 이유에서, 우리 팀이 타입스크립트의 구 버전이 최신 버전이었을 때 내린 결정이거나, 더 나은 방법의 타입선언을 알지 못했을 때 내린 결정에 매몰어서는 안된다고 생각합니다. 우리는 자주는 아니지만, 런타임의 변경보다는 자주 타입이 변경될 수는 있습니다.

### 패키지

XState 의 패키지 리스트는 XState 의 peer dependency 로 포함되어 있습니다. 우리는 신규 버전의 XState 릴리즈 시 이미 릴리즈된 패키지들간의 호환성을 유지하는 데 주의를 기울일 것입니다. **하지만** XState 에 의존하는 패키지들의 각 릴리즈는 항상 XState 의 최신 버전을 포함하도록 peer dependency 를 조정합니다. 예를 들어, 당신은 `@xstate/react` 없이 항상 `xstate` 를 업데이트 할 수 있어야 합니다. 하지만 `@xstate/react` 를 업데이트 할 때는 `xstate` 또한 업데이트하길 강력히 권장합니다.
