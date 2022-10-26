# CLJS API

Created: June 4, 2022 11:54 AM
Finished: No
Source: https://cljs.github.io/api/
Tags: #tool

Welcome! This is a comprehensive reference for ClojureScript's syntax, standard library, and compiler API. **[See the Cheatsheet for quick reference](http://cljs.info/cheatsheet)**

Documentation is versioned and supplemented by curated descriptions,examples, and cross-refs. Community contributions welcome.

**Current Version:** 1.11.54 | [Version Table](https://cljs.github.io/versions)

Get these docs for [Dash](https://kapeli.com/dash) under User Contributed downloads.

Syntax forms, literals, conventions and patterns.

core library

[Untitled](CLJS%20API%20c1efb9d53854456b95d4d5fde68a660b/Untitled%20Database%2012618a4e54cf438ea192ec75087ba326.csv)

compile/analyze ClojureScript code at runtime.

nodejs support functions

a pretty-printer for printing data structures

- print-base*
- print-miser-width*
- print-pprint-dispatch*
- print-pretty*
- print-radix*
- print-right-margin*
- print-suppress-namespaces*

[char-code](https://cljs.github.io/api/cljs.pprint#char-code)

[cl-format](https://cljs.github.io/api/cljs.pprint#cl-format)

[code-dispatch](https://cljs.github.io/api/cljs.pprint#code-dispatch)

[deftype](https://cljs.github.io/api/cljs.pprint#deftype)

[float?](https://cljs.github.io/api/cljs.pprint#floatQMARK)

[formatter](https://cljs.github.io/api/cljs.pprint#formatter)

[formatter-out](https://cljs.github.io/api/cljs.pprint#formatter-out)

[fresh-line](https://cljs.github.io/api/cljs.pprint#fresh-line)

[get-pretty-writer](https://cljs.github.io/api/cljs.pprint#get-pretty-writer)

[getf](https://cljs.github.io/api/cljs.pprint#getf)

[pp](https://cljs.github.io/api/cljs.pprint#pp)

[pprint](https://cljs.github.io/api/cljs.pprint#pprint)

[pprint-indent](https://cljs.github.io/api/cljs.pprint#pprint-indent)

[pprint-logical-block](https://cljs.github.io/api/cljs.pprint#pprint-logical-block)

[pprint-newline](https://cljs.github.io/api/cljs.pprint#pprint-newline)

[pprint-set](https://cljs.github.io/api/cljs.pprint#pprint-set)

[pprint-tab](https://cljs.github.io/api/cljs.pprint#pprint-tab)

[print-length-loop](https://cljs.github.io/api/cljs.pprint#print-length-loop)

[print-table](https://cljs.github.io/api/cljs.pprint#print-table)

[set-pprint-dispatch](https://cljs.github.io/api/cljs.pprint#set-pprint-dispatch)

[setf](https://cljs.github.io/api/cljs.pprint#setf)

[simple-dispatch](https://cljs.github.io/api/cljs.pprint#simple-dispatch)

[with-pprint-dispatch](https://cljs.github.io/api/cljs.pprint#with-pprint-dispatch)

[with-pretty-writer](https://cljs.github.io/api/cljs.pprint#with-pretty-writer)

[write](https://cljs.github.io/api/cljs.pprint#write)

[write-out](https://cljs.github.io/api/cljs.pprint#write-out)

a reader to parse text and produce data structures

macros auto-imported into a ClojureScript REPL

[&](https://cljs.github.io/api/cljs.spec.alpha#&)

- 
- coll-check-limit*
- coll-error-limit*
- compile-asserts*
- explain-out*
- fspec-iterations*
- recursion-limit*

[+](https://cljs.github.io/api/cljs.spec.alpha#PLUS)

[?](https://cljs.github.io/api/cljs.spec.alpha#QMARK)

[MAX_INT](https://cljs.github.io/api/cljs.spec.alpha#MAX_INT)

[abbrev](https://cljs.github.io/api/cljs.spec.alpha#abbrev)

[alt](https://cljs.github.io/api/cljs.spec.alpha#alt)

[and](https://cljs.github.io/api/cljs.spec.alpha#and)

[assert](https://cljs.github.io/api/cljs.spec.alpha#assert)

[assert*](https://cljs.github.io/api/cljs.spec.alpha#assertSTAR)

[cat](https://cljs.github.io/api/cljs.spec.alpha#cat)

[check-asserts](https://cljs.github.io/api/cljs.spec.alpha#check-asserts)

[check-asserts?](https://cljs.github.io/api/cljs.spec.alpha#check-assertsQMARK)

[coll-of](https://cljs.github.io/api/cljs.spec.alpha#coll-of)

[conform](https://cljs.github.io/api/cljs.spec.alpha#conform)

[conformer](https://cljs.github.io/api/cljs.spec.alpha#conformer)

[def](https://cljs.github.io/api/cljs.spec.alpha#def)

[describe](https://cljs.github.io/api/cljs.spec.alpha#describe)

[double-in](https://cljs.github.io/api/cljs.spec.alpha#double-in)

[every](https://cljs.github.io/api/cljs.spec.alpha#every)

[every-kv](https://cljs.github.io/api/cljs.spec.alpha#every-kv)

[exercise](https://cljs.github.io/api/cljs.spec.alpha#exercise)

[exercise-fn](https://cljs.github.io/api/cljs.spec.alpha#exercise-fn)

[explain](https://cljs.github.io/api/cljs.spec.alpha#explain)

[explain-data](https://cljs.github.io/api/cljs.spec.alpha#explain-data)

[explain-data*](https://cljs.github.io/api/cljs.spec.alpha#explain-dataSTAR)

[explain-out](https://cljs.github.io/api/cljs.spec.alpha#explain-out)

[explain-printer](https://cljs.github.io/api/cljs.spec.alpha#explain-printer)

[explain-str](https://cljs.github.io/api/cljs.spec.alpha#explain-str)

[fdef](https://cljs.github.io/api/cljs.spec.alpha#fdef)

[form](https://cljs.github.io/api/cljs.spec.alpha#form)

[fspec](https://cljs.github.io/api/cljs.spec.alpha#fspec)

[gen](https://cljs.github.io/api/cljs.spec.alpha#gen)

[get-spec](https://cljs.github.io/api/cljs.spec.alpha#get-spec)

[inst-in](https://cljs.github.io/api/cljs.spec.alpha#inst-in)

[inst-in-range?](https://cljs.github.io/api/cljs.spec.alpha#inst-in-rangeQMARK)

[int-in](https://cljs.github.io/api/cljs.spec.alpha#int-in)

[int-in-range?](https://cljs.github.io/api/cljs.spec.alpha#int-in-rangeQMARK)

[invalid?](https://cljs.github.io/api/cljs.spec.alpha#invalidQMARK)

[keys](https://cljs.github.io/api/cljs.spec.alpha#keys)

[keys*](https://cljs.github.io/api/cljs.spec.alpha#keysSTAR)

[map-of](https://cljs.github.io/api/cljs.spec.alpha#map-of)

[merge](https://cljs.github.io/api/cljs.spec.alpha#merge)

[multi-spec](https://cljs.github.io/api/cljs.spec.alpha#multi-spec)

[nilable](https://cljs.github.io/api/cljs.spec.alpha#nilable)

[nonconforming](https://cljs.github.io/api/cljs.spec.alpha#nonconforming)

[or](https://cljs.github.io/api/cljs.spec.alpha#or)

[regex?](https://cljs.github.io/api/cljs.spec.alpha#regexQMARK)

[registry](https://cljs.github.io/api/cljs.spec.alpha#registry)

[registry-ref](https://cljs.github.io/api/cljs.spec.alpha#registry-ref)

[spec](https://cljs.github.io/api/cljs.spec.alpha#spec)

[spec?](https://cljs.github.io/api/cljs.spec.alpha#specQMARK)

[speced-vars](https://cljs.github.io/api/cljs.spec.alpha#speced-vars)

[tuple](https://cljs.github.io/api/cljs.spec.alpha#tuple)

[unform](https://cljs.github.io/api/cljs.spec.alpha#unform)

[valid?](https://cljs.github.io/api/cljs.spec.alpha#validQMARK)

[with-gen](https://cljs.github.io/api/cljs.spec.alpha#with-gen)

[any](https://cljs.github.io/api/cljs.spec.gen.alpha#any)

[any-printable](https://cljs.github.io/api/cljs.spec.gen.alpha#any-printable)

[bind](https://cljs.github.io/api/cljs.spec.gen.alpha#bind)

[boolean](https://cljs.github.io/api/cljs.spec.gen.alpha#boolean)

[cat](https://cljs.github.io/api/cljs.spec.gen.alpha#cat)

[char](https://cljs.github.io/api/cljs.spec.gen.alpha#char)

[char-alpha](https://cljs.github.io/api/cljs.spec.gen.alpha#char-alpha)

[char-alphanumeric](https://cljs.github.io/api/cljs.spec.gen.alpha#char-alphanumeric)

[char-ascii](https://cljs.github.io/api/cljs.spec.gen.alpha#char-ascii)

[choose](https://cljs.github.io/api/cljs.spec.gen.alpha#choose)

[delay](https://cljs.github.io/api/cljs.spec.gen.alpha#delay)

[double](https://cljs.github.io/api/cljs.spec.gen.alpha#double)

[double*](https://cljs.github.io/api/cljs.spec.gen.alpha#doubleSTAR)

[dynaload](https://cljs.github.io/api/cljs.spec.gen.alpha#dynaload)

[elements](https://cljs.github.io/api/cljs.spec.gen.alpha#elements)

[fmap](https://cljs.github.io/api/cljs.spec.gen.alpha#fmap)

[for-all*](https://cljs.github.io/api/cljs.spec.gen.alpha#for-allSTAR)

[frequency](https://cljs.github.io/api/cljs.spec.gen.alpha#frequency)

[gen-for-pred](https://cljs.github.io/api/cljs.spec.gen.alpha#gen-for-pred)

[generate](https://cljs.github.io/api/cljs.spec.gen.alpha#generate)

[hash-map](https://cljs.github.io/api/cljs.spec.gen.alpha#hash-map)

[int](https://cljs.github.io/api/cljs.spec.gen.alpha#int)

[keyword](https://cljs.github.io/api/cljs.spec.gen.alpha#keyword)

[keyword-ns](https://cljs.github.io/api/cljs.spec.gen.alpha#keyword-ns)

[large-integer](https://cljs.github.io/api/cljs.spec.gen.alpha#large-integer)

[large-integer*](https://cljs.github.io/api/cljs.spec.gen.alpha#large-integerSTAR)

[list](https://cljs.github.io/api/cljs.spec.gen.alpha#list)

[map](https://cljs.github.io/api/cljs.spec.gen.alpha#map)

[not-empty](https://cljs.github.io/api/cljs.spec.gen.alpha#not-empty)

[one-of](https://cljs.github.io/api/cljs.spec.gen.alpha#one-of)

[quick-check](https://cljs.github.io/api/cljs.spec.gen.alpha#quick-check)

[ratio](https://cljs.github.io/api/cljs.spec.gen.alpha#ratio)

[return](https://cljs.github.io/api/cljs.spec.gen.alpha#return)

[sample](https://cljs.github.io/api/cljs.spec.gen.alpha#sample)

[set](https://cljs.github.io/api/cljs.spec.gen.alpha#set)

[shuffle](https://cljs.github.io/api/cljs.spec.gen.alpha#shuffle)

[simple-type](https://cljs.github.io/api/cljs.spec.gen.alpha#simple-type)

[simple-type-printable](https://cljs.github.io/api/cljs.spec.gen.alpha#simple-type-printable)

[string](https://cljs.github.io/api/cljs.spec.gen.alpha#string)

[string-alphanumeric](https://cljs.github.io/api/cljs.spec.gen.alpha#string-alphanumeric)

[string-ascii](https://cljs.github.io/api/cljs.spec.gen.alpha#string-ascii)

[such-that](https://cljs.github.io/api/cljs.spec.gen.alpha#such-that)

[symbol](https://cljs.github.io/api/cljs.spec.gen.alpha#symbol)

[symbol-ns](https://cljs.github.io/api/cljs.spec.gen.alpha#symbol-ns)

[tuple](https://cljs.github.io/api/cljs.spec.gen.alpha#tuple)

[uuid](https://cljs.github.io/api/cljs.spec.gen.alpha#uuid)

[vector](https://cljs.github.io/api/cljs.spec.gen.alpha#vector)

[vector-distinct](https://cljs.github.io/api/cljs.spec.gen.alpha#vector-distinct)

a unit-testing framework

- current-env*

[are](https://cljs.github.io/api/cljs.test#are)

[assert-any](https://cljs.github.io/api/cljs.test#assert-any)

[assert-expr](https://cljs.github.io/api/cljs.test#assert-expr)

[assert-predicate](https://cljs.github.io/api/cljs.test#assert-predicate)

[async](https://cljs.github.io/api/cljs.test#async)

[async?](https://cljs.github.io/api/cljs.test#asyncQMARK)

[block](https://cljs.github.io/api/cljs.test#block)

[clear-env!](https://cljs.github.io/api/cljs.test#clear-envBANG)

[compose-fixtures](https://cljs.github.io/api/cljs.test#compose-fixtures)

[deftest](https://cljs.github.io/api/cljs.test#deftest)

[do-report](https://cljs.github.io/api/cljs.test#do-report)

[empty-env](https://cljs.github.io/api/cljs.test#empty-env)

[file-and-line](https://cljs.github.io/api/cljs.test#file-and-line)

[function?](https://cljs.github.io/api/cljs.test#functionQMARK)

[get-and-clear-env!](https://cljs.github.io/api/cljs.test#get-and-clear-envBANG)

[get-current-env](https://cljs.github.io/api/cljs.test#get-current-env)

[inc-report-counter!](https://cljs.github.io/api/cljs.test#inc-report-counterBANG)

[is](https://cljs.github.io/api/cljs.test#is)

[join-fixtures](https://cljs.github.io/api/cljs.test#join-fixtures)

[js-filename](https://cljs.github.io/api/cljs.test#js-filename)

[js-line-and-column](https://cljs.github.io/api/cljs.test#js-line-and-column)

[mapped-line-and-column](https://cljs.github.io/api/cljs.test#mapped-line-and-column)

[ns?](https://cljs.github.io/api/cljs.test#nsQMARK)

[report](https://cljs.github.io/api/cljs.test#report)

[run-all-tests](https://cljs.github.io/api/cljs.test#run-all-tests)

[run-block](https://cljs.github.io/api/cljs.test#run-block)

[run-tests](https://cljs.github.io/api/cljs.test#run-tests)

[run-tests-block](https://cljs.github.io/api/cljs.test#run-tests-block)

[set-env!](https://cljs.github.io/api/cljs.test#set-envBANG)

[successful?](https://cljs.github.io/api/cljs.test#successfulQMARK)

[test-all-vars](https://cljs.github.io/api/cljs.test#test-all-vars)

[test-all-vars-block](https://cljs.github.io/api/cljs.test#test-all-vars-block)

[test-ns](https://cljs.github.io/api/cljs.test#test-ns)

[test-ns-block](https://cljs.github.io/api/cljs.test#test-ns-block)

[test-var](https://cljs.github.io/api/cljs.test#test-var)

[test-var-block](https://cljs.github.io/api/cljs.test#test-var-block)

[test-vars](https://cljs.github.io/api/cljs.test#test-vars)

[test-vars-block](https://cljs.github.io/api/cljs.test#test-vars-block)

[testing](https://cljs.github.io/api/cljs.test#testing)

[testing-contexts-str](https://cljs.github.io/api/cljs.test#testing-contexts-str)

[testing-vars-str](https://cljs.github.io/api/cljs.test#testing-vars-str)

[try-expr](https://cljs.github.io/api/cljs.test#try-expr)

[update-current-env!](https://cljs.github.io/api/cljs.test#update-current-envBANG)

[use-fixtures](https://cljs.github.io/api/cljs.test#use-fixtures)

a library for reduction and parallel folding (parallelism not supported)

non-core data functions

deprecated

set operations such as union/intersection

string operations

a generic tree walker for Clojure data structures

functional hierarchical zipper, w/ navigation/editing/enumeration

## Compiler

programmatic access to the analyzer (producing AST)

programmatic access to project-building facilities

[add-dependencies](https://cljs.github.io/api/compiler/cljs.build.api#add-dependencies)

[add-dependency-sources](https://cljs.github.io/api/compiler/cljs.build.api#add-dependency-sources)

[add-implicit-options](https://cljs.github.io/api/compiler/cljs.build.api#add-implicit-options)

[build](https://cljs.github.io/api/compiler/cljs.build.api#build)

[cljs-dependents-for-macro-namespaces](https://cljs.github.io/api/compiler/cljs.build.api#cljs-dependents-for-macro-namespaces)

[compilable->ijs](https://cljs.github.io/api/compiler/cljs.build.api#compilable-GTijs)

[compile](https://cljs.github.io/api/compiler/cljs.build.api#compile)

[compiler-opts?](https://cljs.github.io/api/compiler/cljs.build.api#compiler-optsQMARK)

[dependency-order](https://cljs.github.io/api/compiler/cljs.build.api#dependency-order)

[get-node-deps](https://cljs.github.io/api/compiler/cljs.build.api#get-node-deps)

[goog-dep-string](https://cljs.github.io/api/compiler/cljs.build.api#goog-dep-string)

[handle-js-modules](https://cljs.github.io/api/compiler/cljs.build.api#handle-js-modules)

[index-ijs](https://cljs.github.io/api/compiler/cljs.build.api#index-ijs)

[inputs](https://cljs.github.io/api/compiler/cljs.build.api#inputs)

[install-node-deps!](https://cljs.github.io/api/compiler/cljs.build.api#install-node-depsBANG)

[mark-cljs-ns-for-recompile!](https://cljs.github.io/api/compiler/cljs.build.api#mark-cljs-ns-for-recompileBANG)

[node-inputs](https://cljs.github.io/api/compiler/cljs.build.api#node-inputs)

[node-modules](https://cljs.github.io/api/compiler/cljs.build.api#node-modules)

[ns->location](https://cljs.github.io/api/compiler/cljs.build.api#ns-GTlocation)

[ns->source](https://cljs.github.io/api/compiler/cljs.build.api#ns-GTsource)

[output-unoptimized](https://cljs.github.io/api/compiler/cljs.build.api#output-unoptimized)

[parse-js-ns](https://cljs.github.io/api/compiler/cljs.build.api#parse-js-ns)

[source-on-disk](https://cljs.github.io/api/compiler/cljs.build.api#source-on-disk)

[src-file->goog-require](https://cljs.github.io/api/compiler/cljs.build.api#src-file-GTgoog-require)

[src-file->target-file](https://cljs.github.io/api/compiler/cljs.build.api#src-file-GTtarget-file)

[target-file-for-cljs-ns](https://cljs.github.io/api/compiler/cljs.build.api#target-file-for-cljs-ns)

[watch](https://cljs.github.io/api/compiler/cljs.build.api#watch)

programmatic access to the compiler (emitting JS)

macros auto-imported into a ClojureScript REPL

- cljs-verbose*
- repl-env*
- repl-opts*

[add-url](https://cljs.github.io/api/compiler/cljs.repl#add-url)

[analyze-source](https://cljs.github.io/api/compiler/cljs.repl#analyze-source)

[apropos](https://cljs.github.io/api/compiler/cljs.repl#apropos)

[compilable?](https://cljs.github.io/api/compiler/cljs.repl#compilableQMARK)

[decorate-specs](https://cljs.github.io/api/compiler/cljs.repl#decorate-specs)

[default-special-fns](https://cljs.github.io/api/compiler/cljs.repl#default-special-fns)

[demunge](https://cljs.github.io/api/compiler/cljs.repl#demunge)

[dir](https://cljs.github.io/api/compiler/cljs.repl#dir)

[doc](https://cljs.github.io/api/compiler/cljs.repl#doc)

[eval-cljs](https://cljs.github.io/api/compiler/cljs.repl#eval-cljs)

[evaluate](https://cljs.github.io/api/compiler/cljs.repl#evaluate)

[evaluate-form](https://cljs.github.io/api/compiler/cljs.repl#evaluate-form)

[ex-str](https://cljs.github.io/api/compiler/cljs.repl#ex-str)

[ex-triage](https://cljs.github.io/api/compiler/cljs.repl#ex-triage)

[file-display](https://cljs.github.io/api/compiler/cljs.repl#file-display)

[find-doc](https://cljs.github.io/api/compiler/cljs.repl#find-doc)

[initial-prompt](https://cljs.github.io/api/compiler/cljs.repl#initial-prompt)

[js-src->cljs-src](https://cljs.github.io/api/compiler/cljs.repl#js-src-GTcljs-src)

[known-repl-opts](https://cljs.github.io/api/compiler/cljs.repl#known-repl-opts)

[load](https://cljs.github.io/api/compiler/cljs.repl#load)

[load-file](https://cljs.github.io/api/compiler/cljs.repl#load-file)

[load-namespace](https://cljs.github.io/api/compiler/cljs.repl#load-namespace)

[load-stream](https://cljs.github.io/api/compiler/cljs.repl#load-stream)

[mapped-stacktrace](https://cljs.github.io/api/compiler/cljs.repl#mapped-stacktrace)

[maybe-install-npm-deps](https://cljs.github.io/api/compiler/cljs.repl#maybe-install-npm-deps)

[ns->input](https://cljs.github.io/api/compiler/cljs.repl#ns-GTinput)

[ns-info](https://cljs.github.io/api/compiler/cljs.repl#ns-info)

[print-mapped-stacktrace](https://cljs.github.io/api/compiler/cljs.repl#print-mapped-stacktrace)

[pst](https://cljs.github.io/api/compiler/cljs.repl#pst)

[read-source-map](https://cljs.github.io/api/compiler/cljs.repl#read-source-map)

[repl](https://cljs.github.io/api/compiler/cljs.repl#repl)

[repl*](https://cljs.github.io/api/compiler/cljs.repl#replSTAR)

[repl-caught](https://cljs.github.io/api/compiler/cljs.repl#repl-caught)

[repl-nil?](https://cljs.github.io/api/compiler/cljs.repl#repl-nilQMARK)

[repl-options](https://cljs.github.io/api/compiler/cljs.repl#repl-options)

[repl-prompt](https://cljs.github.io/api/compiler/cljs.repl#repl-prompt)

[repl-quit-prompt](https://cljs.github.io/api/compiler/cljs.repl#repl-quit-prompt)

[repl-read](https://cljs.github.io/api/compiler/cljs.repl#repl-read)

[repl-special-doc-map](https://cljs.github.io/api/compiler/cljs.repl#repl-special-doc-map)

[repl-title](https://cljs.github.io/api/compiler/cljs.repl#repl-title)

[run-inits](https://cljs.github.io/api/compiler/cljs.repl#run-inits)

[setup](https://cljs.github.io/api/compiler/cljs.repl#setup)

[skip-if-eol](https://cljs.github.io/api/compiler/cljs.repl#skip-if-eol)

[skip-whitespace](https://cljs.github.io/api/compiler/cljs.repl#skip-whitespace)

[source](https://cljs.github.io/api/compiler/cljs.repl#source)

[source-fn](https://cljs.github.io/api/compiler/cljs.repl#source-fn)

[special-doc-map](https://cljs.github.io/api/compiler/cljs.repl#special-doc-map)

[tear-down](https://cljs.github.io/api/compiler/cljs.repl#tear-down)

browser-connected REPL

Nashorn REPL (JS on Java 8)

Node.js REPL

Rhino REPL (JS on Java 6+)