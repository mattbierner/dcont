# DCont
<a href="https://github.com/fantasyland/fantasy-land">
    <img src="https://raw.github.com/fantasyland/fantasy-land/master/logo.png" align="right" width="82px" height="82px" alt="Fantasy Land logo" />
</a>

[Delimited continuation][dcont] [Akh][akh] monad transformer that layers delimited
control over a monad (`dcont::trans::dcont`).

The base type, `dcont::dcont`, provides delimited control on its own.

## Structures
The delimited continuation transformer is a monad, functor, and applicative functor.

## Running

#### `DCont::runDCont(m, k)`
Perform a delimited continuation computation `m` and complete with outer continuation `k`.

```
with
    import 'akh::base' {liftM2},
    import 'dcont::dcont' {runDCont of shift reset}
in {

var list = liftM2 @ \x y -> [x, y];

var c = reset \ p ->
    liftM2((+),
        shift @ p \k ->
            list(k (of 1), k (of 2)),
        shift @ p \k ->
            list(k (of 10), k (of 20)));

runDCont(c, console.log); // logs: [[11, 21], [12, 22]]
}
```

#### `DContT::runDContT(m, k)`
Same as `DContT::runDCont` but for transformed types


## Delimited Control Interface
All DCont operations and methods are defined on both the type and its instances.

#### `M.newPrompt`
Create a new unique prompt that can be used to delimit a continuation.

#### `M.pushPrompt(prompt, c)`
Push `prompt` on to the control stack, delimiting the continuation, and evaluate computation `c`.

#### `M.withSubCont(prompt, f)`
Capture the continuation delimited by `prompt` and call `f` with it. `f` maps the delimited continuation to a computation. The delimited control structure passed to `f` should be considered opaque.

#### `M.pushSubCont(subk, c)`
Push an entire sub continuation `sunk` onto the stack and evaluate computation `c`.

#### `M.reset(f)`
Delimit a continuation, calling `f` with delimiting prompt. `f` maps the prompt to a computation that is performed inside the delimited context.

#### `M.shift(p, f)`
Capture the continuation delimited by `p`, reify the continuation, and pass it to `f`. `f` can invoke the reified continuation with a computation to evaluate the rest of the delimited continuation, or return a computation directly to break out.

[dcont]: http://en.wikipedia.org/wiki/Delimited_continuation

[akh]: https://github.com/mattbierner/akh