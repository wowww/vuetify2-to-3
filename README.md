# Vuetify 2 to 3

`#vue` `#vuetify`

## Vuetify?

Vue 생태계에서 가장 활동적이며 다양한 기능을 갖춘 UI toolkit이라고 할 수 있다.

### Vue3과 Vuetify3

Vuetify2와 Vue3가 연동이 되지 않아 이에 대한 이슈가 많았다. 

2022년 6월을 기준으로 [Vuetify3 베타 버전](https://next.vuetifyjs.com/en/getting-started/installation/#vuetify-3-beta)이 나왔는데, Vue 3 용으로 재구축되어 출시되었다고 한다.

- **업데이트 예정 항목**
    - [Vue 3](https://vuejs.org/) 용으로 재구축
    - 앱을 크게 변경할 수 있는 전역 속성
    - **[Built-In Modules](https://sass-lang.com/documentation/modules)** 속성으로 SASS 변수 사용자 정의 및 확장성 향상
    - Vite와 빠르게 사용 가능
    - 대폭 개선된 TypeScript 지원 범위
    
    Vuetify github: [Releases · vuetifyjs/vuetify](https://github.com/vuetifyjs/vuetify/releases)
    

## 프로젝트에 적용

### 1. 버전 확인

```
❗ Vuetify 3에는 vue-cli 5.0이 필요
```


- vue --version 명령어로 vue-cli의 버전을 확인한다.
 재 작업중인 프로젝트의 vue-cli version은 `5.0.4` 이다.
- Vue의 버전도 확인해준다.
현재 프로젝트의 Vue version은 `2.6.14` 이다. Vue를 `3.x.x` 으로 업그레이드해준다.

### 2. 업그레이드 설정

**Vuetify** 클래스 제거, **createVuetify** 기능 사용

```jsx
// 2.x.x
Vue.use(Vuetify)

const vuetify = new Vuetify({ ... })

const app = new Vue({
	vuetify,
	...
})
```

```jsx
// 3.0 
const app = createVue()

const vuetify = createVuetify({ ... })

app.use(vuetify)
```

### 3. 변경된 사항 및 적용 방법

[Guide to upgrading Vuetify - Vuetify](https://next.vuetifyjs.com/en/getting-started/upgrade-guide/#v-app)

#### Components 변경 점

- `light`, `dark` prop이 사라짐 → 대신 `theme` 속성을 사용
프로젝트를 확인해보니 따로 light, dark props가 사용되지 않고 theme 속성이 사용된 것 같음
    
    ```jsx
    const req = {
      address: this.address,
      hashData: hashData,
      type: type,
      theme: theme,
    };
    ```
    

#### 일반적인 변경 사항

- value prop은 model-value로 대체됨
`v-model`을 대신 사용
    
    ```
    📌 v-model 속성
    Vue.js로 Form 요소를 개발할 때 사용하는 v-model
    사용자의 입력을 받는 UI 요소들에 `v-model`이라는 속성을 사용하면 입력 값이 자동으로 뷰 데이터 속성에 연결됨
    
    - `v-model` 더 알아보기
        
        **v-model은 어떻게 동작할까?** 
        
        `v-model` 속성은 `v-bind`와 `v-on` 의 기능의 조합으로 동작
        
        매번 사용자가 일일이 `v-bind`와 `v-on` 속성을 다 지정해주지 않아도 좀 더 편하게 개발할 수 있게 고안된 문법
        
        - `v-bind` 속성은 뷰 인스턴스의 데이터 속성을 해당 HTML 요소에 연결할 때 사용
        - `v-on` 속성은 해당 HTML 요소의 이벤트를 뷰 인스턴스의 로직과 연결할 때 사용
        - 사용자 이벤트에 의해 실행된 뷰 메서드(methods) 함수의 첫 번째 인자에는 해당 이벤트(`event`)가 들어옴
        - `v-model` = `v-bind:value` + `v-on`
    
    
  ```

참고: [v-model의 동작 원리와 활용 방법](https://joshua1988.github.io/web-development/vuejs/v-model-usage/)   

```jsx
// components/nft/common/NftListTabBar.vue
// vuetify 2.x.x
<template>
  <div class="list-bar">
    <v-tabs **:value**="value" class="tab-traded" background-color="transparent" @change="onChange">
      <v-tab>{{ $t('t.exd.top.traded') }}</v-tab>
      <v-tab>{{ $t('t.exd.recent.traded') }}</v-tab>
    </v-tabs>
    <router-link to="/nft/history" class="list-bar__more">{{ $t('w.rounds.view.more') }}</router-link>
  </div>
</template>
```
    
```jsx
// components/nft/common/NftListTabBar.vue
// vuetify 3.0
<template>
  <div class="list-bar">
    <v-tabs **v-model**="value" class="tab-traded" background-color="transparent" @change="onChange">
      <v-tab>{{ $t('t.exd.top.traded') }}</v-tab>
      <v-tab>{{ $t('t.exd.recent.traded') }}</v-tab>
    </v-tabs>
    <router-link to="/nft/history" class="list-bar__more">{{ $t('w.rounds.view.more') }}</router-link>
  </div>
</template>
```
    

- @input 처럼 값에 대한 value들만 변경되는 event(예: Form에 사용되는 것 들)는 `v-model`을 사용
- @click 은 예외일 수 있다.

```html
// components/nft/desktop/NtfListFilter.vue
// vuetify 2.x.x
<v-range-slider
  class="filter-range-slider"
  **:value="level"**
  :min="defaultLevelRange[0]"
  :max="defaultLevelRange[1]"
  track-color="#ffffff33"
  track-fill-color="#fff"
  thumb-color="#aa00ff"
  **@input="onChange.level"**
>
```

```html
// components/nft/desktop/NtfListFilter.vue
// vuetify 3.0
<v-range-slider
  class="filter-range-slider"
  **v-model="level"**
  :min="defaultLevelRange[0]"
  :max="defaultLevelRange[1]"
  track-color="#ffffff33"
  track-fill-color="#fff"
  thumb-color="#aa00ff"
>
```


### **v-app**

**id** prop 삭제됨

```html
// layout/default.vue
// vuetify 3.0 이상 부터는 id 값 삭제
<template>
  <v-app **id="app"**>
    <v-main>
      <div v-if="!isHiddenHeader">
        <mobile-header v-if="isMobile" />
        <desktop-header v-else />
      </div>
      <v-container id="container" fluid>
        <Nuxt />
        <modal-account />
        <modal-wemix-login />
        <modal-common />
      </v-container>
    </v-main>
  </v-app>
</template>
```

### v-alert

v-alert은 진행중인 프로젝트에서 사용하지 않아 따로 예제는 없으나 아래 항목이 변경됐다고 합니다.

- **border** props value가 `start` → `left` , `end` → `right`로 대체됨
- **colored-border** prop이 **border-color**로 대체됨
- **dismissable** prop이 **closeable**로 대체됨
- **outlined** and **text** 두가지 props가 **variant** 하나의 prop으로 대체됨
- **text** 는 기본 슬롯이 사용되지 않는 경우 경고 텍스트 내용이 나타나는 것으로 변경됨

[컴포넌트 보러가기](https://next.vuetifyjs.com/en/components/all/)