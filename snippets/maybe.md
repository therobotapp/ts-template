### Install dependencies

`npm i --save fp-ts io-ts`

### Make a module

```typescript
// utils/maybe.ts

import { TypeC, Props, ExactC, exact } from 'io-ts'
import { pipe } from 'fp-ts/lib/pipeable'
import { fold } from 'fp-ts/lib/Either'

export const maybe = <P extends Props>(
  input: unknown,
  type: ExactC<TypeC<P>> | TypeC<P>,
  noExtraFields: boolean = false
) =>
  pipe(
    noExtraFields && type._tag !== 'ExactType'
      ? exact(type).decode(input)
      : type.decode(input),
    fold(
      () => null,
      right => right
    )
  )
export default maybe
```

### Usage

```typescript
import maybe from './utils/maybe'
import * as t from 'io-ts'
const User = t.type({
  userId: t.number,
  name: t.string,
})
const maybeUser = maybe(
  JSON.parse('{ "userId": 1, "name": "Giulio", "something": "field" }'),
  User
)
console.log(maybeUser, maybeUser?.userId)
console.log(
  maybe(
    JSON.parse('{ "userId": 1, "name": "Giulio", "something": "field" }'),
    User,
    true
  )
)
```
