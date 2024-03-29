# TypeScript 学習メモ

## インストール

```
git checkout -b install-typescript //ブランチきる
npm info typescript // バージョン確認
npm install --save-dev typescript@3.7.5 // インストール
```

```
mkdir src
touch src/install-typescript.ts
```

## コンパイル

```
tsc src/install-typescript.ts // コマンド不明になる
npx tsc src/install-typescript.ts // npxコマンドを使用する
```

## 動作確認

```
node src/install-typescript.js
```

## ts-node インストール

```
npm install --save-dev ts-node@9.1.1
```

### ts-node 実行

- コンパイルから実行まで一気に
- js ファイルは生成されない

```
npx ts-node src/install-typescript.ts
```

## ts-node-dev インストール

```
npm install --save-dev ts-node-dev@1.1.1
```

### ts-node-dev 実行

```
npx ts-node-dev --respawn src/install-typescript.ts
```

### package.json の script に登録

```
"dev" : "ts-node-dev --respawn",
```

### 実行方法

```
npm run dev src/install-typescript.ts

```

## Visual Studio Code について

- 設定 →telemetory の２つをオフにする
- コマンドパレット →code path で登録すると、code で呼べる
- 設定 →tab size→2 に変更
- 拡張機能 prettier インストール
- 設定 →settings json→json 設定へ 1.追加
  "editor.formatOnSave": true,
  "prettier.semi": true,
  "prettier.singleQuote" : true,

## tsconfig.json の生成

```
npx tsc --init
```

## 型

```typescript
// boolean
let isFinished: boolean = true;

// number
let year: number = 1976;

// string
let name: string = 'Ham';

// array
let numbers: number[] = [1, 2, 3];
// 非推奨のarray
let strings2: Array<string> = ['Tokyo', 'Osaka', 'Kyoto'];
// 共用型array
let hairetsu: (string | number | boolean)[] = [1, false, 'japan'];
// tuple 順番まで指定
let profile: [string, number] = ['Ham', 43];

// any(axiosのresponseなど型が不明な場合)
let data: any = response.data;

// void（呼び元には帰ってくる）
function returnNothing(): void {
  console.log("I don't return nothing!");
}

// null and undefined
let absence: null = null;
let data: undefined = undefined;

// never（例外を起こすもの、呼び元に帰cd ..ってこない）
function error(message: string): never {
  throw new Error(message);
}

// object（あいまいobjectなら何でもOK）
// object型を指定するとjsのほぼ全てのオブジェクトを指定することになり、あまり良くない
// object型ではなく、具体的な構造を書く方が良い
let profile1: object = { name: 'Ham' };
// object（型指定が厳しく、こっちの方が良い）
let profiles2: { name: string } = { name: 'Ham' };
```

## 型エイリアス

```typescript
// 型にエイリアスをつける。最初は大文字
type Mojiretsu = string;
const fooString: Mojiretsu = 'Hello';

type Profile1 = {
  name: string;
  age: number;
};

// 今あるものから型を取り出す「typeエイリアス」
const example = {
  name: 'Ham',
  age: 43,
};

type Profile2 = typeof example;
```

## インターフェース

- 型として作ることができる

```typescript
// 型の例
interface ObjectInterface {
  name: string;
  age: number;
}
// typeエイリアスと同じように使用可能
let object: ObjectInterface {
  name: 'Ham-san',
  age: 43,
};
```

- 以下は Java と同様、実装して使用する

```typescript
interface Kenja {
  ionazun(): void; // シグネチャ
}
interface Senshi {
  kougeki(): void;
}
// 実装
class Jiro implements Kenja, Senshi {
  ionazun(): void {
    console.log('ionazun');
  }
  kougeki(): void {
    console.log('kougeki');
  }
}
```

## 型ガード（Type Guard）
- ユニオン型など受け取った際に、typeof, instanceof, inを使って型を判定し、条件式で絞り込む
- 変数がnullやundefinedを扱う場合にユニオン型で定義するため、型ガードは頻繁に使用する

```typescript
const kansu = (): number => 43;

// 暫定的に型を指定したいとき、unknownを使う
let numberUnknown: unknown = kansu();

// 型ガードでnumber型のときのみ、if文の処理をする
if (typeof numberUnknown === 'number') {
  let sumUnknown = numberUnknown + 10;
}

// instanceofの場合
function getYear(d: string | Date): number {
  if (d instanceof Date) {
    return d.getFullYear()
  } else {
    const date = new Date(d)
    return date.getFullYear()
  }
}

// ユーザー定義型ガード
// 判定部分を関数化して使用する
// 変数名 is 型名 という返り値の書き方が必要
fucntion isString(d: string | Date): d is string {
  return typeof d === 'string'
}

```

## intersection 型（既存の型を組み合わせる）

```typescript
type Pitcher1 = {
  throwingSpeed: number;
};

type Batter1 = {
  battingAverage: number;
};

// 既存の型からintersection型を作る
type twoWayPlayer = Pitcher1 & Batter1;

const OtaniShohei: twoWayPlayer = {
  throwingSpeed: 165,
  battingAverage: 0.286,
};
```

## union（型の複数設定）

```typescript
let value: number | string = 2021;
value = 'apple';
```

## literal（具体的なものの複数設定）

```typescript
// 文字のリテラル型
let dayOfTheWeek: '日' | '月' | '火' | '水' | '水' | '木' | '金' | '土' = '日';
dayOfTheWeek = '月';

// 数値のリテラル型
let month: 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 = 1;
month = 12;

// booleanのリテラル型
let TRUE: true = true;
```

## アンビエント宣言
- declareキーワード を使いアンビエント宣言をしておくことで、TypeScriptでも動作するようになる
```typescript
declare let x: number;

x = 30;
console.log(x);
```

## 関数

### 通常の関数

- 引数と返り値にアノテーションをつける

```typescript
function bmi(
  height: number, // 引数１
  weight: number // 引数2
): number {
  // 返り値
  return weight / (height * height);
}
```

### 無名関数

- 通常＋必要に応じて変数名にアノテーションをつける

```typescript
let bmi: (height: number, weight: number) => number = function (
  // 変数名につける
  height: number, // 引数1
  weight: number // 引数2
): number {
  // 返り値
  return weight / (height * height);
};
```

### アロー関数

```typescript
let bmi: (height: number, weight: number) => number = (
  height: number,
  weight: number
): number => weight / (height * height);
```

### optional 引数

- 引数名の最後に?をつけることで optional 引数になる
- optional 引数はなくてもコンパイルエラーにならない

```typescript
let bmi: (height: number, weight: number, printable?: boolean) => number = (
  height: number,
  weight: number,
  printable?: boolean
): number => {
  const bmi: number = weight / (height * height);

  if (printable) {
    console.log({ bmi });
  }
  return bmi;
};
```

### default 引数

- js と同様にイコールをつけるとデフォルト引数になる

### 残余引数(Rest パラメータ）

- 可変長引数をとるときは...arg と記載する

```typescript
// 総和用のreducer関数を用意
const reducer = (accumulator: number, currentValue: number) => {
  return accumulator + currentValue;
};

const sum = (...values: number[]): number => {
  return values.reduce(reducer);
};
```

### オーバーロード

- 事前に型付きで抽象関数を宣言しておき、その後、typeof で型チェックしながら定義する

```typescript
function double(value: number): number;
function double(value: string): string;

function double(value: any): any {
  if (typeof value === 'number') {
    return value * 2;
  } else {
    return value + value;
  }
}
```

## クラス

### 基本

- プロパティに型指定をする
- コンストラクタの引数にも型指定するが、戻り値型には不要
- メソッドには戻り値型を指定

```typescript
class Person {
  name: string;
  age: number;

  // constructorに戻り値型は記載していけない
  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }

  profile(): string {
    return `name: ${this.name}, age: ${this.age}`;
  }
}
let taro = new Person('Taro', 30);
console.log(taro.profile());
```

### アクセス修飾子

- public (Java と違って宣言なしも public であり、通常記載しない)
- protected 継承先でも使える
- private class 内のみで使える

```typescript
class Person {
  public name: string;
  protected age: number;
  protected nationality: string;
  // constructorに戻り値型は記載していけない
  constructor(name: string, age: number, nationality: string) {
    this.name = name;
    this.age = age;
    this.nationality = nationality;
  }

  profile(): string {
    return `name: ${this.name}, age: ${this.age}`;
  }
}
// 継承クラス
class Android extends Person {
  constructor(name: string, age: number, nationality: string) {
    // super が使える
    super(name, age, nationality);
  }
  profile(): string {
    return `name: ${this.name}, age: ${this.age}, nationality: ${this.nationality}`;
  }
}
```

### readonly 修飾子

- アクセス修飾子のあとに readonly 修飾子をつけることにより読取専用になる

```typescript
class VisaCard {
  constructor(public readonly owner: string) {}
}
```

### コンストラクタの便利な使い方

- プロパティと setter と getter が省略

```typescript
class Person {
  constructor(public name: string, private age: number) {}
}
```

### getter と setter

- メソッドは get XXX として作る
- プロパティは\_XXX にする慣習
- setter は引数にセットするわけではなく、＝でセットする

```typescript
class MyNumberCard{

  private _owner: string; // アンダースコア入れる
  private _secretNumber: number; // アンダースコア入れる

  constructor(owner: string, secretNumber: number ) {
    this._owner = owner;
    this._secretNumber = secretNumber;
  }

  // getter
  get owner():string {
    return this._owner;
  }

  // setter
  set secretNumber(secretNumber:number) {
    this._secretNumber = secretNumber;
  }
}
// 使い方
et card = new MyNumberCard('ハムさん', 1234567890);
console.log(card.owner);
card.secretNumber = 1111111111; //setterは引数に入れない

```

### static メンバ

- プロパティ名に static をつける
- プライベートなフィールドを#で定義できるようになった(ES2022)
- TypeScriptはprivate修飾子とClass Fieldsの両方が使用可能
  - private修飾子：コンパイル後はパブリックなフィールドになるので、ソフトプライベート
  - Class Fields：コンパイル後もプライベートなままなのでハードプライベート

```typescript
class Me {
  static isProgrammer: boolean = true;
  static firstName: string = 'Atushi';
  static lastName: string = 'Ishida';

  static work() {
    return `Hey, guys! This is ${this.firstName} Are you interested in TypeScript? Let's dive into TypeScript!`;
  }
}
```

```typescript
class Point {
	#x: number // privateなメンバは#をつける
	#y: number
	z: number // 何もつけないとpublic

	constructor(x: number, y: number) {
		this.#x = x
		this.#y = y
	}
	
	// getで定義可能
	get getX() {
		return this.#x
	}
	
	get getY() {
		return this.#y
	}

	public add(p: Point) {
		this.#x += p.x
		this.#y += p.y
	} 
}
```

### 名前空間

- namespace 空間名 で名前空間を区切る
- namespace 内のクラスには export をつける
- namespace 内で namespace をネストできるが、export をつける

```typescript
namespace English {
  // 名前空間
  export class Person {
    // クラス名にexport
    constructor(
      public firstName: string,
      public middleName: string,
      public lastName: string
    ) {}
  }
}
namespace Japanese {
  // 名前空間
  export namespace Tokyo {
    // 名前空間のネスト(export付)
    export class Person {
      constructor(public name: string) {}
    }
  }
  export namespace Osaka {
    // 名前空間のネスト(export付)
    export class Person {
      constructor(public name: string) {}
    }
  }
}
```

### 継承

- Java 同様でコンストラクタ内で呼ぶ、super.メソッド名()、super.プロパティ名が使える

```typescript
// 親クラス
class Animal {
  constructor(public name: string) {}
  run(): string {
    return 'I can run';
  }
}
// 子クラス
class Lion extends Animal {
  public speed: number;

  constructor(name: string, speed: number) {
    // superで親コンストラクタを呼ぶ
    super(name);
    this.speed = speed;
  }
  run(): string {
    // super.メソッド名、super.プロパティ名で呼び出す
    return `I'm ${this.name}. ${super.run()} ${this.speed}km/h.`;
  }
}
```

### 抽象クラス

- Java 同様に abstract が使用できる

```typescript
abstract class Animal {
  abstract cry(): string;
}

class Lion extends Animal {
  cry() {
    return 'roar';
  }
}

class Tiger extends Animal {
  cry() {
    return 'grrrr';
  }
}
```

## 高度な型

### 型の互換性

- 文字列リテラル型などのリテラル型は上位型と互換性あり
- 構造的部分型であり、メンバが一致すれば互換性あり
- Java や PHP は公称型であり、継承しているかで互換性を判断

```typescript
// リテラル型
let fooStringLiteral: 'fooStringLiteral' = 'fooStringLiteral';
fooString = fooStringLiteral; // 文字列リテラル型は文字列型の一部

// 構造的部分型
interface Animal {
  // ageとnameを持っている
  age: number;
  name: string;
}
class Person {
  // 同じくageとnameを持っている
  constructor(public age: number, public name: string) {}
}
// メンバが一致していれば互換性はある
let me: Animal;
me = new Person(43, 'はむさん');
```

### ジェネリクス

- 関数は関数名の直後に<T>をつける
- クラスはクラス名の直後に<T>をつける
- 両者とも型は全て T とする

```typescript
// 関数
const echo = <T>(arg: T): T => {
  return arg;
};
function Func<T>(arg: T): T {
  return arg;
}
console.log(echo<number>(100));

// クラス
class Mirror<T> {
  constructor(public value: T) {}
  echo(): T {
    return this.value;
  }
}
console.log(new Mirror<number>(123).echo());
```

### 型アサーション

- キャストではない。any 型だったりを型指定するもの
- 互換性がないとアサーションできない
- as と<>の２種類あるが、<>は JSX 構文と似ているため非推奨

```typescript
let name: any = 'Ham';
// asを使ったアサーション
let length = (name as string).length;
// jsxで使われるものに似ているため非推奨
let length2 = (<string>name).length;
```

### const アサーション

- 型アサーションと別物
- 構文の最後に as const をつける
- let nickname = 'Ham' as const;とすると Ham 型になる
- オブジェクトにつけると、内容の書き換え不可(readonly 化)

```typescript
// as constをつけて、Ham型にする
let nickname = 'Ham' as const;
nickname = 'Ham';

// as constをつけるとreadonly属性が付与
let profile = {
  name: 'Atsushi',
  height: 180,
} as const;
```

### nullable な型

- 型指定時に union 型として| null を設定する

```typescript
// 変数ageにはnumber型かnullが入る設定
let profile: { name: string; age: number | null } = {
  name: 'Ham',
  age: null,
};
```

### インデックスシグネチャ

- 汎用性のある型インデックスを定義するもの
- インデックス部分に[index: string]などと記載すると自由なインデックスを設定できる

```typescript
// How to write index signatures
// { [ index: typeForIndex]: typeForValue }
interface Profile {
  name: string;
  underTwenty: boolean;
  [index: string]: string | number | boolean;
}
// nameとunderTwentyは必須
let profile: Profile = { name: 'Ham', underTwenty: false };

// 未定義のageやnationalityを設定できる
profile.name = 'Ham';
profile.age = 43;
profile.nationality = 'Japan';
```

## Utility Types

- コード内で型変換を容易にする為に TypeScript が提供する(便利な関数のような)型達

### Partial<T>

- T に型を入れることで、その型のプロパティは全て?がついたオプションとなった型に変換できる

### Required<T>

- T に型を入れることで、その型のプロパティは全て?がない必須項目となった型に変換できる

```typescript
// ベースの型
type Profile = {
  name: string;
  age?: number;
  zipCode: number;
};

// 型PartialTypeのプロパティは全て?がついたオプションとなる
type PartialType = Partial<Profile>;
// 型RequiredTypeのプロパティは全て?がない必須項目
type RequiredType = Required<Profile>;
```

### Mapped types

- Partial の定義で使っている in keyof を使って、新しい型を作るもの

```typescript
// Partialの定義
// in keyof Tで型Tからkeyを取り出して、新しい型を?付きで作る
type Optional<T> = { [P in keyof T]?: T[P] | null };
```

### Readonly types

- 読取専用の型に変換。全てのプロパティが readonly になる

```typescript
type Profile = {
  name: string;
  age: number;
};
// Readonly<T>で変換される
type PersonalDataType = Readonly<Profile>;
const friend: PersonalDataType = {
  name: 'Shigeru',
  age: 40,
};
```

### Record types

- 組み込み型？のオブジェクトの型定義をシンプルに記述
- Record<K, T>と記述（K はユニオン型）

```typescript
// ユニオン型
type Prefectures = 'Tokyo' | 'Chiba' | 'Tottori' | 'Shiga';
type Covid19InfectionInfo = {
  kanji_name: string; // 名前
  confirmed_cases: number; // 感染者数
};

// Record<K, T>を使う。要素を増やすときは上記prefecturesに追加
const covid19Japan: Record<Prefectures, Covid19InfectionInfo> = {
  Tokyo: { kanji_name: '東京', confirmed_cases: 1960 },
  Chiba: { kanji_name: '千葉', confirmed_cases: 249 },
  Tottori: { kanji_name: '鳥取', confirmed_cases: 2 },
};
```

### Exclude types

- Exclude 除外
- Exclude<T, U> ユニオン型の型 T から U を取り除いた型を設定

```typescript
type DebugType = () => void;
type SomeTypes = string | number | DebugType;

// stringとnumberが除外
type FunctionType = Exclude<SomeTypes, string | number>;
// DebugTypeが除外
type NonFUnctionType = Exclude<SomeTypes, DebugType>;
// 関数全て除外
type TypeExcludeFunction = Exclude<SomeTypes, Function>;
```

### Extract types

- Extract 引き抜く
- Extract<T, U> ユニオン型の型 T から U を引き抜いた型を指定

```typescript
// DebugType型が設定
type FunctionTypeByExtract = Extract<SomeTypes, DebugType>;
// stringとnumberが設定
type NonFunctionTypeByExtract = Extract<SomeTypes, string | number>;
// 関数全て抜き出して設定
type FunctionTypeExtractingFunction = Extract<SomeTypes, Function>;
```

### NonNullable types

- NonNullable<T> ユニオン型の T から null など値が入っていないものを除外

```typescript
type NullableTypes = string | number | null | undefined;
// nullとundefinedは除外される
type NonNullableTypes = NonNullable<NullableTypes>;
```

### Pick types

- Pick<T, K> 型 T の中から K の型だけ取り出して設定

### Omit types

- Omit<T, K> 型 T の中から K の型を除外して設定

```typescript
type DetailedProfile = {
  name: string;
  height: number;
  weight: number;
};

// Pick<T, K> 型Tの中からKの型だけ取り出して設定
type SimpleProfile = Pick<DetailedProfile, 'name' | 'height'>;
// nameとheightが設定

// Omit<T, K> 型Tの中からKの型を除外して設定
type SmallProfile = Omit<DetailedProfile, 'weight'>;
// nameとheightが設定
```

### Return types

- ReturnType<typeof 関数> 返り値の型を設定する

```typescript
function add(a: number, b: number) {
  return a + b;
}
// add関数の返り値の型numberを設定する
type ReturnTypeFromAdd = ReturnType<typeof add>;
```

### Parameters types

- Prameters<typeof 関数> 引数の型を設定する

```typescript
const debugProfile = (name: string, age: number) => {
  console.log({ name, age });
};
debugProfile('Ham', 43);

// Parameters<typeof F>で関数Fの引数の型を取得する
// 他の人が作った関数など容易に変更できないものから取得することが多い
type Profile = Parameters<typeof debugProfile>;
// 定数profileに設定
const profile: Profile = ['Gloria', 76];
// 関数debugProfileの引数に入れることができる
debugProfile(...profile);
```

### ConstructorParameters

- ConstructorParameters<クラスタイプ> コンストラクタの引数の型を取り出して型を作る
- typeof でクラスからクラスの型を作り、引数に入れる

```typescript
// Personクラスを定義
class Person {
  name: string;
  age: number;
  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }
}
// PersonTypeを作る
type PersonType = typeof Person;
// PersonTypeからConstructorParameters<>を使う
type Profile = ConstructorParameters<PersonType>;

const profile: Profile = ['Ham', 43];
const ham = new Person(...profile);
```

## React と TypeScript

### FunctionComponent

- 関数の変数に型アノテーションをつける
- React.FC と省略可能

```typescript
const App: React.FunctionComponent<AppProps> = ({ message }: AppProps) => {
  return <div>{message}</div>;
};
```

### useState

- 16.8 から登場した Hooks の中のフック
- 関数コンポーネントの中でローカルな state を使うために呼び出す。
- const [x, setX] = useState(初期値)

```typescript
import React, { useState } from 'react'; // reactとuseStateの読み込み

// React.FunctionComponentはReact.FCと省略可能
const Counter: React.FC<{}> = () => {
  const initialValue: any = 0;
  // useStateでvalueとsetValueを定義
  const [value, setValue] = useState<number>(initialValue);

  const increment = () => {
    setValue((prevState) => prevState + 1);
  };

  const decrement = () => {
    setValue((prevState) => prevState - 1);
  };

  return (
    <div>
      <div> value: {value}</div>
      <button onClick={increment}>+1</button>
      <button onClick={decrement}>-1</button>
    </div>
  );
};

export default Counter; // Counterの出力
```

### useRef と useEffect

- useRef で初期値をもって、useEffect で render 時に処理をする？
- import React, {useEffect, useRef, useState} from 'react';する

```typescript
// useRefで初期値を設定
const renderTimes = useRef<number>(0);

// renderされるたびに実行
useEffect(() => {
  renderTimes.current += 1;
});

return (
  <div>
    <div>This component was re-rendered {renderTimes.current} times!</div>
  </div>
);
```

- focus の方法

```typescript
// nonNullアサーションオペレータ!で、nullじゃないことを伝える
const ref = useRef<HTMLInputElement>(null!);
const focusInput = () => {
  // オプショナルチェインを使った場合
  // ref.current?.focus();

  ref.current.focus();
};
return (
  <div>
    <input ref={ref} type="text" />
    <button onClick={focusInput}>Click Me!</button>
  </div>
);
```
