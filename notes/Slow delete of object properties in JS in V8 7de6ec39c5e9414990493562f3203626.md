# Slow delete of object properties in JS in V8

Created: July 5, 2022 10:19 AM
Finished: No
Source: https://exchangetuts.com/slow-delete-of-object-properties-in-js-in-v8-1640123944068044

![image.png](Slow%20delete%20of%20object%20properties%20in%20JS%20in%20V8%207de6ec39c5e9414990493562f3203626/image.png)

Just to train myself a bit of Typescript I wrote a simple ES6 Map+Set-like implementation based on plain JS Object. It works only for primitive keys, so no buckets, no hash-codes, etc. The problem I encountered is implementing delete method. Using plain `delete` is just unacceptably slow. For large maps it's about 300-400x slower than ES6 Map delete. I noticed the huge performance degradation if size of the object is large. On Node JS 7.9.0 (and Chrome 57 for example) if object has 50855 properties `delete` performance is the same as ES6 Map. But for 50856 properties the ES6 Map is faster on 2 orders of magnitude. Here is the simple code to reproduce:

```
// for node 6: 76300// for node 7: 50855const N0 = 50855;function fast() {
	const N = N0

	const o = {}
	for ( let i = 0; i < N; i++ ) {
		o[i] = i
	}

	const t1 = Date.now()
	for ( let i = 0; i < N; i++ ) {
		delete o[i]
	}
	const t2 = Date.now()

	console.log( N / (t2 - t1) + ' KOP/S' )}function slow() {
	const N = N0 + 1 // adding just 1

	const o = {}
	for ( let i = 0; i < N; i++ ) {
		o[i] = i
	}

	const t1 = Date.now()
	for ( let i = 0; i < N; i++ ) {
		delete o[i]
	}
	const t2 = Date.now()

	console.log( N / (t2 - t1) + ' KOP/S' )}

fast()

slow()
```

I guess I could instead of `delete` properties just set them to `undefined` or some guard object, but this will mess the code, because `hasOwnProperty` will not work correctly, `for...in` loops will need additional check and so on. Are there more nice solutions?

P.S. I'm using node 7.9.0 on OSX Sierra

**Edited** Thanks for comments guys, I fixed OP/S => KOP/S. I think I asked rather badly specified question, so I changed the title. After some investigation I found out that for example in Firefox there is no such problems -- deleting cost grows linearly. So it's problem of super smart V8. And I think it's just a bug:(

(V8 developer here.) Yes, this is a known issue. The underlying problem is that objects should switch their elements backing store from a flat array to a dictionary when they become too sparse, and the way this has historically been implemented was for every `delete` operation to check if enough elements were still present for that transition *not* to happen yet. The bigger the array, the more time this check took. Under certain conditions (recently created objects below a certain size), the check was skipped -- the resulting impressive speedup is what you're observing in the `fast()` case.

I've taken this opportunity to fix the (frankly quite silly) behavior of the regular/slow path. It should be sufficient to check every now and then, not on every single `delete`. The fix will be in V8 6.0, which should be picked up by Node in a few months (I believe Node 8 is supposed to get it at some point).

That said, using `delete` causes various forms and magnitudes of slowdown in many situations, because it tends to make things more complicated, forcing the engine (*any* engine) to perform more checks and/or fall off various fast paths. It is generally recommended to avoid using `delete` whenever possible. Since you have ES6 maps/sets, use them! :-)