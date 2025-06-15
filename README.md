# Crypto Exchange Vue Project

Простий навчальний додаток на **Vue.js 3** для взаємодії з API обміну криптовалют. Дозволяє переглядати курси криптовалют, обирати торгові пари та додавати їх до списку обраних. Проєкт створений для вивчення основ роботи з Vue 3, компонентами, формами, асинхронними запитами та управлінням станом.

---

## ✨ Особливості

- Вибір криптовалютних пар для конвертації
- Введення суми та обчислення результату конвертації
- Додавання та видалення торгових пар до/з обраних
- Відображення помилок і результатів конвертації
- Інтеграція з бібліотекою `crypto-convert` для роботи з API

---

## 🛠 Технології

- **Vue.js 3** (Composition API)
- **JavaScript (ES6+)**
- **HTML5**, **CSS3**
- **[Axios](https://axios-http.com/)** (для HTTP-запитів, якщо використовується)
- **[crypto-convert](https://github.com/coinconvert/crypto-convert)** (для конвертації криптовалют)
- **[ESLint](https://eslint.org/)** (для забезпечення якості коду)

---

## 🚀 Встановлення та запуск

### 1. Клонування репозиторію
```bash
git clone https://your-repository-url/crypto_exchange.git
cd crypto_exchange
```

### 2. Встановлення залежностей
```bash
npm install
```

### 3. Запуск у режимі розробки
```bash
npm run serve
```
Відкрийте `http://localhost:8080` у браузері.

### 4. Збірка для продакшену
```bash
npm run build
```

### 5. Лінтинг та виправлення помилок
```bash
npm run lint
```

---

## 🖼 Інтерфейс

*Додайте скріншот інтерфейсу вашого додатку тут.*

---

## 📂 Структура проєкту

```
crypto_exchange/
├── src/
│   ├── App.vue                   # Кореневий компонент
│   ├── main.js                  # Точка входу
│   ├── components/
│   │   ├── BaseInput.vue        # Компонент для введення суми та дій
│   │   ├── FavouriteComponent.vue # Компонент для відображення обраних пар
│   │   └── SelectorCrypto.vue    # Компонент для вибору криптовалюти
│   └── assets/                  # Статичні ресурси (зображення, стилі тощо)
├── public/
│   └── index.html               # Основний HTML-файл
├── package.json                 # Налаштування проєкту та залежності
├── babel.config.js              # Конфігурація Babel
├── vue.config.js                # Конфігурація Vue CLI
└── README.md                    # Документація
```

---

## 📖 Опис компонентів

### `App.vue`
Кореневий компонент, що відповідає за загальну структуру сторінки, управління станом та взаємодію між дочірніми компонентами.

#### Шаблон (`<template>`)
```html
<template>
  <div>
    <h1>CRYPTO</h1>
    <!-- Компонент для введення суми та кнопок дій -->
    <BaseInput @changeAmount="changeAmount" @convert="convert" @favourite="favourite" />

    <!-- Відображення повідомлень про помилки -->
    <p v-if="error" class="error">{{ error }}</p>
    <!-- Відображення результату конвертації -->
    <p v-if="result !== 0" class="result">Результат: {{ result }}</p>

    <!-- Компонент для відображення списку обраних пар -->
    <FavouriteComponent
      v-if="favs.length > 0"
      :favs="favs"
      @setFromFavs="setFromFavs"
    />

    <!-- Контейнер для вибору криптовалют -->
    <div class="selectors">
      <SelectorCrypto
        :cryptoNow="cryptoFirst"
        @changeCrypto="changeCryptoFirst"
      />
      <SelectorCrypto
        :cryptoNow="cryptoSecond"
        @changeCrypto="changeCryptoSecond"
      />
    </div>
  </div>
</template>
```

**Ключові елементи шаблону:**
- **Заголовок**: `<h1>CRYPTO</h1>` — заголовок сторінки.
- **`BaseInput`**: Компонент для введення суми та виконання дій (конвертація, додавання до обраних).
  - Події: `@changeAmount`, `@convert`, `@favourite`.
- **Повідомлення**:
  - `<p v-if="error" class="error">{{ error }}</p>`: Відображає помилки, якщо `error` не порожній.
  - `<p v-if="result !== 0" class="result">Результат: {{ result }}</p>`: Показує результат конвертації.
- **`FavouriteComponent`**: Відображає список обраних пар, якщо `favs.length > 0`.
  - Пропси: `:favs` — масив обраних пар.
  - Події: `@setFromFavs` — встановлення обраної пари.
- **`SelectorCrypto`**: Два екземпляри для вибору вихідної та цільової криптовалюти.
  - Пропси: `:cryptoNow` — поточна обрана валюта.
  - Події: `@changeCrypto` — оновлення обраної валюти.

#### Логіка (`<script>`)
```javascript
import BaseInput from './components/BaseInput.vue';
import SelectorCrypto from './components/SelectorCrypto.vue';
import FavouriteComponent from './components/FavouriteComponent.vue';
import CryptoConvert from 'crypto-convert';

const convert = new CryptoConvert(); // Ініціалізація бібліотеки

export default {
  name: 'App',
  components: {
    BaseInput,
    SelectorCrypto,
    FavouriteComponent,
  },
  data() {
    return {
      amount: 0,          // Сума для конвертації
      cryptoFirst: '',    // Вихідна криптовалюта
      cryptoSecond: '',   // Цільова криптовалюта
      error: '',          // Повідомлення про помилку
      result: 0,          // Результат конвертації
      favs: [],           // Список обраних пар
    };
  },
  methods: {
    changeAmount(val) {
      this.amount = val;
      this.result = 0; // Скидання результату
    },
    changeCryptoFirst(val) {
      this.cryptoFirst = val;
      this.result = 0; // Скидання результату
    },
    changeCryptoSecond(val) {
      this.cryptoSecond = val;
      this.result = 0; // Скидання результату
    },
    async convert() {
      this.result = 0;
      this.error = '';

      // Валідація
      if (this.amount <= 0) {
        this.error = 'Введіть суму більше 0';
        return;
      }
      if (!this.cryptoFirst || !this.cryptoSecond) {
        this.error = 'Оберіть обидві криптовалюти';
        return;
      }
      if (this.cryptoFirst === this.cryptoSecond) {
        this.error = 'Оберіть різні криптовалюти';
        return;
      }

      await convert.ready(); // Очікування готовності бібліотеки
      this.result = convert[this.cryptoFirst][this.cryptoSecond](this.amount);
    },
    favourite() {
      this.error = '';

      // Валідація
      if (!this.cryptoFirst || !this.cryptoSecond) {
        this.error = 'Оберіть обидві криптовалюти';
        return;
      }
      if (this.cryptoFirst === this.cryptoSecond) {
        this.error = 'Оберіть різні криптовалюти';
        return;
      }

      const pair = { from: this.cryptoFirst, to: this.cryptoSecond };
      const existingPairIndex = this.favs.findIndex(
        (fav) => fav.from === pair.from && fav.to === pair.to
      );

      if (existingPairIndex > -1) {
        this.favs.splice(existingPairIndex, 1);
        this.error = `Пару ${pair.from}/${pair.to} видалено з обраних`;
      } else {
        this.favs.push(pair);
        this.error = `Пару ${pair.from}/${pair.to} додано до обраних`;
      }
    },
    setFromFavs(index) {
      this.cryptoFirst = this.favs[index].from;
      this.cryptoSecond = this.favs[index].to;
      this.result = 0; // Скидання результату
    },
  },
};
```

**Ключові моменти логіки:**
- **Імпорти**: Компоненти та бібліотека `crypto-convert`.
- **Стан (`data`)**: Зберігає суму, обрані криптовалюти, помилки, результати та список обраних пар.
- **Методи**:
  - `changeAmount`: Оновлює суму та скидає результат.
  - `changeCryptoFirst`/`changeCryptoSecond`: Оновлюють обрані валюти.
  - `convert`: Виконує валідацію та конвертацію за допомогою `crypto-convert`.
  - `favourite`: Додає або видаляє пару з обраних.
  - `setFromFavs`: Встановлює обрану пару з списку.

### Структура компонента `SelectorCrypto.vue`


Компонент складається з двох основних секцій: шаблону (`<template>`) та логіки (`<script>`). Секція стилів (`<style>`) не надана в оригінальному коді, тому в цій документації вона не описується.

#### 1. Шаблон (`<template>`)

```html
<template>
    <div>
        <ul>
            <li :class="{ 'active': cryptoNow === 'BTC' }" @click="setCrypto('BTC')">Bitcoin</li>
            <li :class="{ 'active': cryptoNow === 'ETH' }" @click="setCrypto('ETH')">Ethereum</li>
            <li :class="{ 'active': cryptoNow === 'SOL' }" @click="setCrypto('SOL')">Solana</li>
            <li :class="{ 'active': cryptoNow === 'DOGE' }" @click="setCrypto('DOGE')">Dogecoin</li>
            <li :class="{ 'active': cryptoNow === 'ADA' }" @click="setCrypto('ADA')">Cardano</li>
            <li :class="{ 'active': cryptoNow === 'DOT' }" @click="setCrypto('DOT')">Polkadot</li>
            <li :class="{ 'active': cryptoNow === 'USDT' }" @click="setCrypto('USDT')">USDT</li>
        </ul>
    </div>
</template>
```

**Опис шаблону:**

- **Контейнер**: `<div>` — обгортка для списку криптовалют, яка забезпечує базову структуру.
- **Список**: `<ul>` — невпорядкований список, що містить елементи `<li>` для кожної криптовалюти.
- **Елементи списку** (`<li>`):
  - Кожен `<li>` представляє одну криптовалюту (наприклад, Bitcoin, Ethereum тощо).
  - **Динамічний клас**: Атрибут `:class="{ 'active': cryptoNow === 'КОД_ВАЛЮТИ' }"` додає CSS-клас `active` до елемента, якщо значення пропса `cryptoNow` (поточна обрана валюта) збігається з кодом криптовалюти (наприклад, `'BTC'`). Це дозволяє візуально виділити активний елемент.
  - **Обробник подій**: Атрибут `@click="setCrypto('КОД_ВАЛЮТИ')"` викликає метод `setCrypto` з кодом відповідної криптовалюти (наприклад, `'BTC'`) при кліку на елемент.
  - **Текст**: Усередині `<li>` відображається повна назва криптовалюти (наприклад, `Bitcoin` для коду `'BTC'`).
- **Доступні криптовалюти**:
  - Bitcoin (`BTC`)
  - Ethereum (`ETH`)
  - Solana (`SOL`)
  - Dogecoin (`DOGE`)
  - Cardano (`ADA`)
  - Polkadot (`DOT`)
  - USDT (`USDT`)

**Примітка**: Список криптовалют захардкоджено в шаблоні, що обмежує можливість динамічного додавання нових валют без зміни коду.

#### 2. Логіка (`<script>`)

```javascript
<script>
export default {
    name: 'SelectorCrypto',
    props: {
        cryptoNow: {
            type: String,
            required: true,
            default: ''
        }
    },
    methods:{
        setCrypto(val){
            this.$emit('changeCrypto', val);
            // this.current = val; // Цей рядок, ймовірно, зайвий або помилковий,
                                // оскільки 'current' не визначено в data() компонента.
                                // Компонент не має власного стану для 'current'.
        },
    },
}
</script>
```

**Опис логіки:**

- **Назва компонента**:
  - `name: 'SelectorCrypto'`: Ім’я компонента, яке використовується для ідентифікації в Vue Devtools та при відладці.
- **Пропси**:
  - `cryptoNow`: Вхідний параметр, який передається від батьківського компонента (`App.vue`).
    - **Тип**: `String` — очікується рядкове значення, що представляє код криптовалюти (наприклад, `'BTC'`).
    - **Обов’язковість**: `required: true` — пропс є обов’язковим.
    - **Значення за замовчуванням**: `default: ''` — порожній рядок, якщо пропс не передано (хоча через `required: true` це малоймовірно).
    - **Призначення**: Використовується для визначення, яка криптовалюта обрана, щоб підсвітити відповідний `<li>` класом `active`.
- **Методи**:
  - `setCrypto(val)`:
    - **Опис**: Викликається при кліку на елемент списку (`<li>`). Передає код обраної криптовалюти (наприклад, `'BTC'`) батьківському компоненту через подію `changeCrypto`.
    - **Логіка**: Використовує `this.$emit('changeCrypto', val)` для генерації події `changeCrypto` з передачею значення `val` (коду криптовалюти).
    - **Примітка про коментований код**: Рядок `// this.current = val;` є закоментованим і, ймовірно, залишком від попередньої версії. Властивість `current` не визначена в секції `data()`, тому цей рядок не має функціонального значення. Компонент не зберігає власного стану для обраної валюти, оскільки це контролюється через пропс `cryptoNow` і батьківський компонент.
- **Відсутність стану**: Компонент не має секції `data()`, оскільки не зберігає локального стану. Уся інформація про обрану валюту надходить через пропс `cryptoNow`, а зміни передаються через події.

---

#### Використання компонента

Компонент `SelectorCrypto` використовується в `App.vue` для вибору вихідної та цільової криптовалюти. Приклад використання:

```html
<SelectorCrypto
  :cryptoNow="cryptoFirst"
  @changeCrypto="changeCryptoFirst"
/>
<SelectorCrypto
  :cryptoNow="cryptoSecond"
  @changeCrypto="changeCryptoSecond"
/>
```

- **Пропс**:
  - `:cryptoNow="cryptoFirst"`: Передає код першої обраної валюти (наприклад, `'BTC'`) для підсвічування активного елемента.
  - `:cryptoNow="cryptoSecond"`: Передає код другої обраної валюти.
- **Події**:
  - `@changeCrypto="changeCryptoFirst"`: Викликає метод `changeCryptoFirst` у `App.vue` для оновлення стану `cryptoFirst`.
  - `@changeCrypto="changeCryptoSecond"`: Викликає метод `changeCryptoSecond` для оновлення стану `cryptoSecond`.

---

#### Функціональність

1. **Вибір криптовалюти**:
   - Користувач клікає на один із елементів списку (`<li>`), наприклад, "Bitcoin".
   - Метод `setCrypto` викликається з кодом валюти (наприклад, `'BTC'`).
   - Компонент випромінює подію `changeCrypto` з кодом (`'BTC'`), яку обробляє батьківський компонент.

2. **Візуальне підсвічування**:
   - Якщо значення пропса `cryptoNow` збігається з кодом валюти (наприклад, `cryptoNow === 'BTC'`), до відповідного `<li>` додається клас `active`.
   - Клас `active` використовується для стилізації (наприклад, зміна фону чи кольору тексту), але стилі не визначені в наданому коді.

3. **Комунікація з батьківським компонентом**:
   - Компонент не зберігає власного стану, а лише передає вибір користувача через подію `changeCrypto`.
   - Батьківський компонент (`App.vue`) відповідає за оновлення стану та передачу оновленого `cryptoNow` назад у компонент.

### Структура компонента `BaseInput.vue`

Компонент `BaseInput.vue` призначений для введення користувачем суми та ініціювання дій конвертації або додавання до обраних.

#### 1. Шаблон (`<template>`)

```html
<template>
    <div>
        <input type="number" placeholder="Введіть число" @input="handleInputChange($event.target.value)" min="0" />
        <button @click="convert()">Конвертувати</button>
        <button @click="favourite()">Вибрані</button>
    </div>
</template>
```

**Опис шаблону:**

- **Контейнер**: `<div>` — обгортка для елементів вводу та кнопок.
- **Поле вводу**: `<input type="number" ... />`
  - `type="number"`: Вказує, що поле призначене для введення числових значень.
  - `placeholder="Введіть число"`: Текст-підказка, що відображається, коли поле порожнє.
  - `@input="handleInputChange($event.target.value)"`: При кожній зміні значення в полі викликається метод `handleInputChange`, якому передається поточне значення поля (`$event.target.value`).
  - `min="0"`: Встановлює мінімальне допустиме значення для введення (0).
- **Кнопка "Конвертувати"**: `<button @click="convert()">Конвертувати</button>`
  - При кліку на цю кнопку викликається метод `convert()`.
- **Кнопка "Вибрані"**: `<button @click="favourite()">Вибрані</button>`
  - При кліку на цю кнопку викликається метод `favourite()`.

#### 2. Логіка (`<script>`)

```javascript
<script>
export default {
    name: 'BaseInput',
    methods: {
        handleInputChange(value) {
            this.$emit('changeAmount', value);
        },
        convert() {
            this.$emit('convert');
        },
        favourite() {
            this.$emit('favourite');
        }
    }
}
</script>
```

**Опис логіки:**

- **Назва компонента**:
  - `name: 'BaseInput'`: Ім'я компонента, корисне для налагодження.
- **Методи**:
  - `handleInputChange(value)`:
    - **Опис**: Викликається при зміні значення в полі вводу.
    - **Логіка**: Генерує подію `changeAmount` та передає `value` (введене користувачем значення) батьківському компоненту. Батьківський компонент (`App.vue`) прослуховує цю подію для оновлення стану суми.
  - `convert()`:
    - **Опис**: Викликається при натисканні кнопки "Конвертувати".
    - **Логіка**: Генерує подію `convert`. Батьківський компонент прослуховує цю подію для запуску процесу конвертації.
  - `favourite()`:
    - **Опис**: Викликається при натисканні кнопки "Вибрані".
    - **Логіка**: Генерує подію `favourite`. Батьківський компонент прослуховує цю подію для додавання поточної пари криптовалют до списку обраних або видалення з нього.
- **Відсутність стану та пропсів**: Компонент не має власної секції `data()` та не приймає пропси. Він повністю керується подіями, які генерує, та передає відповідальність за обробку даних батьківському компоненту.

#### 3. Стилі (`<style scoped>`)

```css
<style scoped>
input {
    display: block;
    margin: 0 auto;
    border-radius: 3px;
    border:transparent;
    padding: 10px 15px;
    background-color: #fafafa;
    color:#333;
    outline: none;
    width: 500px;
    position: relative;
    top:-50px;
    font-size: 1.2em;
}

button {
    position: relative;
    margin: 0 10px;
    top:-20px;
    padding: 15px 20px;
    color:#fff;
    text-transform: uppercase;
    cursor: pointer;
    background-color: #1a032d;
    border: 0;
    border-radius: 3px;
    font-size: 1.2em;
}
</style>
```

**Опис стилів:**

- **Атрибут `scoped`**: Гарантує, що ці стилі будуть застосовані тільки до елементів цього компонента.
- **Стилі для `input`**: Визначають зовнішній вигляд поля вводу, включаючи розміри, відступи, колір, фон, заокруглення кутів та позиціонування.
- **Стилі для `button`**: Визначають зовнішній вигляд кнопок, включаючи відступи, колір тексту та фону, розмір шрифту, заокруглення кутів та позиціонування.

#### Використання компонента

Компонент `BaseInput` використовується в `App.vue` для отримання суми від користувача та ініціювання дій:

```html
<BaseInput @changeAmount="changeAmount" @convert="convert" @favourite="favourite" />
```

**Події:**
- `@changeAmount="changeAmount"`: При зміні суми в `BaseInput` викликається метод `changeAmount` в `App.vue`.
- `@convert="convert"`: При натисканні кнопки "Конвертувати" викликається метод `convert` в `App.vue`.
- `@favourite="favourite"`: При натисканні кнопки "Вибрані" викликається метод `favourite` в `App.vue`.

#### Функціональність

- **Введення суми**: Користувач вводить числове значення у поле. При кожній зміні генерується подія `changeAmount`.
- **Ініціювання конвертації**: При натисканні кнопки "Конвертувати" генерується подія `convert`.
- **Додавання до обраних**: При натисканні кнопки "Вибрані" генерується подія `favourite`.
- Компонент `BaseInput` слугує простим інтерфейсом для користувача, делегуючи всю логіку обробки даних та виконання дій батьківському компоненту `App.vue`.

---

### Структура компонента `FavouriteComponent.vue`

Компонент `FavouriteComponent.vue` відповідає за відображення списку обраних користувачем пар криптовалют та дозволяє вибрати одну з них для подальших дій.

#### 1. Шаблон (`<template>`)

```html
<template>
    <div>
        <h2>Вибрані пари криптовалют:</h2>
        <div v-if="favs && favs.length > 0">
            <div @click="setfromFavs(index)" v-for="(el, index) in favs" :key="index" class="fav-item">
                <span>{{ el.from }}</span>
                <span>{{ el.to }}</span>
            </div>
        </div>
        <div v-else>
            <p class="empty-message">Список вибраних порожній.</p>
        </div>
    </div>
</template>
```

**Опис шаблону:**

- **Заголовок**: `<h2>Вибрані пари криптовалют:</h2>` — відображає назву секції.
- **Умовний рендеринг**:
  - `v-if="favs && favs.length > 0"`: Блок з обраними парами відображається тільки якщо масив `favs` існує і не порожній.
  - `v-else`: Якщо масив `favs` порожній або не існує, відображається повідомлення `<p class="empty-message">Список вибраних порожній.</p>`.
- **Список обраних пар**:
  - `<div @click="setfromFavs(index)" v-for="(el, index) in favs" :key="index" class="fav-item">`:
    - `v-for="(el, index) in favs"`: Ітерує по масиву `favs`, де `el` — це об'єкт обраної пари (наприклад, `{ from: 'BTC', to: 'USD' }`), а `index` — її індекс у масиві.
    - `:key="index"`: Надає унікальний ключ для кожного елемента списку, що важливо для ефективного оновлення DOM у Vue.
    - `@click="setfromFavs(index)"`: При кліку на елемент списку викликається метод `setfromFavs` з передачею індексу цього елемента.
    - `class="fav-item"`: CSS-клас для стилізації елемента списку.
  - `<span>{{ el.from }}</span>`: Відображає код вихідної криптовалюти пари.
  - `<span>{{ el.to }}</span>`: Відображає код цільової криптовалюти пари.

#### 2. Логіка (`<script>`)

```javascript
<script>
export default {
    name: 'FavouriteComponent',
    props: {
        favs: {
            type: Array,
            required: true,
            default: () => []
        },
    },
    methods: {
        setfromFavs(index){
            this.$emit('setfromFavs', index);
        }
    }
}
</script>
```

**Опис логіки:**

- **Назва компонента**:
  - `name: 'FavouriteComponent'`: Ім'я компонента.
- **Пропси**:
  - `favs`: Вхідний параметр, що передається від батьківського компонента (`App.vue`).
    - `type: Array`: Очікується масив.
    - `required: true`: Пропс є обов'язковим.
    - `default: () => []`: Значення за замовчуванням — порожній масив. Це стандартна практика для пропсів типу `Array` або `Object`.
    - **Призначення**: Містить список об'єктів, де кожен об'єкт представляє обрану пару криптовалют (наприклад, `{ from: 'BTC', to: 'USD' }`).
- **Методи**:
  - `setfromFavs(index)`:
    - **Опис**: Викликається при кліку на елемент зі списку обраних пар.
    - **Логіка**: Генерує подію `setfromFavs` та передає `index` (індекс обраної пари в масиві `favs`) батьківському компоненту. Батьківський компонент (`App.vue`) прослуховує цю подію, щоб встановити відповідні криптовалюти у свої поля вибору.

#### Використання компонента

Компонент `FavouriteComponent` використовується в `App.vue` для відображення списку обраних пар та взаємодії з ним:

```html
<FavuoriteComponent :favs="favs" @setfromFavs="setfromFavs" v-if="favs.length > 0"/>
```

- **Пропс**:
  - `:favs="favs"`: Передає масив `favs` зі стану `App.vue` до компонента.
- **Подія**:
  - `@setfromFavs="setfromFavs"`: При виборі пари зі списку в `FavouriteComponent` викликається метод `setfromFavs` в `App.vue` з індексом обраної пари.
- **Умова**:
  - `v-if="favs.length > 0"`: Компонент відображається тільки якщо масив `favs` не порожній.

#### Функціональність

- **Відображення списку**: Компонент отримує масив `favs` та відображає кожну пару як окремий клікабельний елемент.
- **Обробка порожнього списку**: Якщо масив `favs` порожній, виводиться відповідне повідомлення.
- **Вибір пари**: При кліку на елемент списку генерується подія `setfromFavs` з індексом обраної пари, що дозволяє батьківському компоненту оновити свій стан (наприклад, встановити обрані криптовалюти для конвертації).

---

## 📦 Залежності

Встановіть бібліотеку `crypto-convert` для роботи з конвертацією:
```bash
npm install crypto-convert
```

Документація: [crypto-convert](https://github.com/coinconvert/crypto-convert).

---

## 📄 Ліцензія

Цей проєкт ліцензовано за [GNU General Public License](./LICENSE).

---

## 🙌 Внесок

Цей проєкт створений для навчальних цілей. Якщо ви хочете внести зміни:
1. Створіть форк репозиторію.
2. Внесіть зміни та створіть pull request.
3. Опишіть зміни у pull request.

