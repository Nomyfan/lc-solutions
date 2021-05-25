## 基础编程能力

[GitHub repo](https://github.com/Nomyfan/lcp1)

## 编写复杂的 TypeScript 类型

[TS Playground 链接](https://www.typescriptlang.org/play?#code/JYOwLgpgTgZghgYwgAgIILMA9iAPAFQD5kBvAKGWQAc4BPAGyzgBMB+ALmXwG4LkxaVCJwDOYKKADmvAL5kyCenBEjkAURgwIGALJZmAV3opylBFgPhkAXmQBGXpQC2EFXEkpbAIgAWEeowAhF68fMz+dAAUoFQGYJwAClBYTsAiELggBk4ARtCEAJSkfJRQEGAGUCDIMXEAdGB+IJHRRdbEkaaU3TQMTMycAAZ+AVjIACQkwDKBgwA0Jd0CQpxe4Uq0XgvdyDIFBY678pTpYDquIu4QkYiYOJzod3gAInCQhcU7ZRVVnzvUdEYLE4t2wIDqvSBzECdQ8Z2AATS2hwzBEkQK23+y2EyC8pwAtC43B4tosZIc5HIyNjkABhHAgbSQZg2P7rKK1eLILK5aAFB4YMG4MQSECSQiHU7nYnXUH3ZCvSD8tCCnCZbJ5KASsjk+Q0+kgRkYCDMVAiWggBDnRr6AhzZAAVWItmiIFiXPwbWIjyFTt4+oZTJNAGULVbyj5bfh7U7WTdVSBOJ6bN6E7g-XrBCgDUbmQA1OD0AwZfAFosQZ1cMvF5AQAAekBAqOQkT4nMSyVS6VwoC0UC4hDIXuQSRSSNwPrVvegjsIg8orDpgeNpvNlutkeYdtnfCT1ZQ9cbzfjTwFTx7ID7A+Hk7w0-7Tr4i5zQeYofXEajMfnyE4jIAbtAoQ0gAYpYCAANIQLQBCVl0ADaEE1NUADW0FYDAXAALpJohWG1g2EBNqoYGWk8yCLkhf4QIBUCyPBaG0Bh2HAVmS6Gky+BsbYCFIaAyCkZB0G4BoWi6PoRgVjh7G5ia+4iZoTJ6IYxh4dqurUmxL4YHGTgScYnCiUp+kQMO2lgFxQihOYIBiMgNm5pw5m6cOnRhBEtCcK67p-hqfIpi2XSUNiqzspsmKUJC-RDCMjDIAATPMfB7JiUoXFcXntgqbymQFbk7CFuIEkSlwkhFAJ9MCyHurC5Q6Ai9BIjZqLopiKU6gc8j1lQWBQGA9k4HZDmvk5y7Mqyw0YJEjIAO7qIp4kqdc+y8EAA)

```ts
interface Action<T> {
  payload?: T;
  type: string;
}

class EffectModule {
  count = 1;
  message = "hello!";

  delay(input: Promise<number>) {
    return input.then((i) => ({
      payload: `hello ${i}!`,
      type: "delay",
    }));
  }

  setMessage(action: Action<Date>) {
    return {
      payload: action.payload!.getMilliseconds(),
      type: "set-message",
    };
  }
}

type Connected = {
  delay(input: number): Action<string>;
  setMessage(action: Date): Action<number>;
};

type ConnectedAsyncMethod<T, U> = (input: T) => Action<U>;
type ConnectedSyncMethod<T, U> = (action: T) => Action<U>;

type ConnectedValue<TValue> = TValue extends (
  input: Promise<infer T>
) => Promise<Action<infer U>>
  ? ConnectedAsyncMethod<T, U>
  : TValue extends (action: Action<infer T>) => Action<infer U>
  ? ConnectedSyncMethod<T, U>
  : never;

type FuncKey<T> = {
  [K in keyof T]: T[K] extends Function ? K : never;
}[keyof T];

type ConnectType = {
  [K in FuncKey<EffectModule>]: ConnectedValue<EffectModule[K]>;
};

type Connect = (module: EffectModule) => ConnectType;

const connect: Connect = (m) => ({
  delay: (input: number) => ({
    type: "delay",
    payload: `hello 2`,
  }),
  setMessage: (input: Date) => ({
    type: "set-message",
    payload: input.getMilliseconds(),
  }),
});

export const connected: Connected = connect(new EffectModule());
```

## 用 Webpack 实现 predictable long term cache

[GitHub repo](https://github.com/Nomyfan/lcp3)
