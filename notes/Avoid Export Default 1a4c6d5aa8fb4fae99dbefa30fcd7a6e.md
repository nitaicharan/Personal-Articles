# Avoid Export Default

Created: June 4, 2022 8:41 PM
Finished: No
Source: https://basarat.gitbook.io/typescript/main-1/defaultisbad
Subjects: typescript
Tags: #typescript

Auto import quickfix works better. You use `Foo` and auto import will write down `import { Foo } from "./foo";` cause its a well defined name exported from a module. Some tools out there will try to magic read and *infer* a name for a default export but magic is flaky.