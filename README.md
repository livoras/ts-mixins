# ts-mixins
Mixins In TypeScript with Code Suggestions.

```typescript
type Constructor<T> = new(...args: any[]) => T

const isAbstract = Symbol("isAbstract")

export const mixin = <T1 = {}, T2 = {}, T3 = {}, T4 = {}, T5 = {}, T6 = {}, T7 = {}>(
  C1?: Constructor<T1>,
  C2?: Constructor<T2>,
  C3?: Constructor<T3>,
  C4?: Constructor<T4>,
  C5?: Constructor<T5>,
  C6?: Constructor<T6>,
  C7?: Constructor<T7>,
): {
  new(): T1 & T2 & T3 & T4 & T5 & T6 & T7,
} => {
  const baseCtors = [C1, C2, C3, C4, C5, C6, C7]
  class Super {
    constructor() {
      const subClassName = Object.getPrototypeOf(this).constructor.name
      baseCtors.forEach((ctor) => {
        if (!ctor) { return }
        const abstractMethodList = []
        Object.keys(ctor.prototype).forEach((method) => {
          if (ctor.prototype[method][isAbstract]) {
            abstractMethodList.push(method)
          }
        })
        abstractMethodList.forEach((method) => {
          console.assert(!this[method][isAbstract],  `${subClassName} 必须要实现父类 ${ctor.name} 的抽象方法: ${method}`)
        })
      })
    }
  }
  applyMixin(baseCtors, Super)
  return Super as any
}

function applyMixin(baseCtors, Super) {
  baseCtors.forEach((baseCtor) => {
    Object.assign(Super.prototype, baseCtor.prototype)
    const instance = new baseCtor()
    Object.assign(Super.prototype, instance)
  })
}

export const Abstract = () => {
  return (target: any, propertyKey: string, descriptor: PropertyDescriptor) => {
    target[propertyKey][isAbstract] = true
  }
}

```
