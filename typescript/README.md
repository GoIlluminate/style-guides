# TypeScript

## Formatting
* Make sure [Prettier](https://prettier.io/) is installed and configured to 'format on save' in your editor of choice. Same with [ts-lint](https://palantir.github.io/tslint/). Here is a sample `.prettierrc`, which should be placed in the root dir of your project.
   ```json
   {
   "printWidth": 150,
   "tabWidth": 3,
   "useTabs": false,
   "semi": false,
   "singleQuote": true,
   "trailingComma": "es5",
   "bracketSpacing": true,
   "arrowParens": "avoid",
   "parser": "typescript"
   }
   ```

* Don't use semicolons. Prettier should handle this for you.

* 3 spaces are the standard tab length.

* Use `'single quotes'` for strings and template strings where appropriate.
   ```typescript
   `The cow jumped over ${planet}`
   ```

## Mutation
* Avoid mutation when possible. Try to write your code in a functional style.
   ```typescript
      // Don't mutate
      const numbers = [2, 4, 6, 21]
      let even_squares: number[] = []

      for (let i = 0; i < numbers.length; i++) {
         const num = numbers[i]

         if (num % 2 === 0) {
            squares.push(num)
         }
      }

      // Use functions like map, reduce, filter etc.
      const even_squares: number[] = numbers.filter(num => num % 2 === 0).map(num => num * num)
   ```
* The spread operator is your friend.
   ```typescript
   // Don't do this
   let user: M.User = {
      username: 'alex',
      email: 'alex.munda@goilluminate.com',
   }
   user.email = 'alex@munda.com'

   // Use the spread, Luke
   const user: M.User = {
      username: 'alex',
      email: 'alex.munda@goilluminate.com',
   }

   const updated_user: M.User = {
      ...user,
      email: 'alex@munda.com,
   }
   ```

## Variables
* Prefer `const` to `let` and `var`. A detailed explanation between each can be found [here](https://tylermcginnis.com/var-let-const/)
   ```typescript
   const existing_user = await this.fetch(account.account_model_key)
   ```

* Variables should be snake_cased
   ```typescript
   const twenty_one = 21
   ```

* Variable names should convey meaning
   ```typescript
   // BAD DON'T DO THIS
   const stuff: M.DogCreate = {
      name: 'Moose',
      breed: 'Golden'
   }

   const value1 = await this.createDog(stuff)

   // GOOD
   const dog_create: M.DogCreate = {
      name: 'Moose',
      breed: 'Golden'
   }

   const dog = await this.createDog(options)
   ```
* When creating object literals, explicitly name the object properties.
   ```typescript
   // DON'T USE IMPLICIT NAMING
   const foo = 'x'
   const bar = 'y'
   const baz = {
      foo,
      bar,
   }
   ```
   ```typescript
   // BE EXPLICIT
   const foo = 'x'
   const bar = 'y'
   const baz = {
      foo: foo,
      bar: bar,
   }
   ```

## Imports
* Imports should follow [es-module](https://hacks.mozilla.org/2018/03/es-modules-a-cartoon-deep-dive/) imports/exports.
   ```typescript
   import { UserService } from '../services/user_service'
   ```
* When importing a few members from a module, specify them explicitly.
   ```typescript
   import { UserService, EmailService } from '../services'
   ```
   When you end up using many members from a single module, `import *` and alias them. This is most commonly used with models.
   ```typescript
   import * as M from '../models'

   const dog: M.Dog = {
      breed: 'Golden',
      name: 'Moose',
   }

   const cat: M.Cat = {
      breed: 'Siamese',
      name: 'Whiskers',
   }
   ```
* Index files should be used to make importing many modules easier. Say we have the following folder structure:
   ```typescript
   models
      |__ dog.ts
      |__ cat.ts
      |__ cow.ts
      |__ pig.ts
   ```
   If we add an `index.ts` to the models folder, we can export all of these models so they can be imported by `import * as M from '../models'`
   ```typescript
   // models/index.ts
   export * from './dog'
   export * from './cat'
   export * from './cow'
   export * from './pig'
   ```

## Functions
* 'Class methods' and exported functions should be camelCased
   ```typescript
   // In the file helpers.ts
   export function createProposal(options: M.ProposalCreate): Promise<M.Proposal> {
      ...
   }
   ```
   ```typescript
   // In the file user_service.ts
   export class UserService extends BaseService {
      async createUser(user_create: M.UserCreate): Promise<M.User> {
         ...
      }
   }
   ```
* Function signatures should be explicit
   ```typescript
   // Don't do this
   function addOne(num) {
      return num + 1
   }

   // Do this
   function addOne(num: number): number {
      return num + 1
   }
   ```
* Single line lambdas need not use braces
   ```typescript
   // Unnecessary
   const addOne = (num: number): number => {
      return num + 1
   }

   // Keith Stone smooth
   const addOne = (num: number): number => num + 1
   ```
* Functions which return a `Promise` should be marked as `async`
   ```typescript
   async function clearDb(): Promise<void> {
      ...
   }
   ```

## Promises
* Promises should be used in the `async/await` style. Know that `await` isn't necessary when returning a promise _UNLESS_ its inside of a `try/catch` block. See [this article](https://jakearchibald.com/2017/await-vs-return-vs-return-await/) for more details.
   ```typescript
   // Old news
   return user_service.create(user_create)
   .then(user => {
      return messaging_service.sendWelcomeMessage(user)
   })
   .catch(error => {
      Logger.error('Something went wrong.')
   })

   // async/await, new meta
   try {
      const user = await user_service.create(user_create)

      return await messaging_service.sendWelcomeMessage(user)
   }
   catch (error) {
      Logger.error('Something went wrong.')
   }
   ```
* Use `Promise.all` when possible
   ```typescript
   // Serial
   const user_1 = await user_service.createUser(option_1)
   const user_2 = await user_service.createUser(option_2)

   // When results are independent of each other, schedule at the same time
   const [user_1, user_2] = await Promise.all([option_1, option_2].map(x => user_service.createUser(x)))
   ```
