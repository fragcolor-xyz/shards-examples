# Appendix B - Handling multiple wires

How to write and control code across multiple wires

- undocumented shards/funcs (?) : Mesh, WireLoader, Wire, Wire@, Wire*, set-global-wire, unset-global-wire, run-empty-forever, eval-wire, wireify, prepare, start, tick, stop, sleep (`SHCore.cpp`, `/tests/loader.clj`, `/tests/compress-strings.edn`)

## Using `(defwire)`
- remove wires explanation from [`(defwire)`](https://docs.fragcolor.xyz/functions/macros/#defwire) and put it here 

- explain wire with loop controls
- explain running multiple wires

## Controlling the wires

*<program control flow with multiple wires :  input/ dispatch/ spawn/ detach/ branch/ WireRunner (with access to variables of spawned/detached wires), wait, branch, repeat, resume, pause, start, stop, step and other shards defined in (`...\code\shards\src\core\shards\wires.cpp`)
https://github.com/fragcolor-xyz/shards/blob/devel/src/tests/subwires.clj
https://docs.fragcolor.xyz/shards/General/Stop/

...
https://github.com/fragcolor-xyz/shards/blob/devel/examples/discord/common.edn
https://github.com/fragcolor-xyz/discord-bot
https://github.com/fragcolor-xyz/shards/blob/devel/src/tests/branch.edn
https://github.com/fragcolor-xyz/shards/blob/devel/src/tests/channels.clj
https://github.com/fragcolor-xyz/shards/blob/devel/src/tests/continuations.clj
https://github.com/fragcolor-xyz/shards/blob/devel/src/tests/demo.clj
https://github.com/fragcolor-xyz/shards/blob/devel/src/tests/subwires.clj
https://github.com/fragcolor-xyz/shards/blob/devel/src/tests/wires_embed_issue.edn

## Wires or iterators
*<when to use wire loops (defloop, multiple wires) and when to use Repeat/Map/ForEach shards (inside same wire); for example in Shards discord bot sample - If the loop logic should end within that iteration of the wire use a Map/Repeat/Foreach to code the loop logic in the same wire; if the loop logic runs independent of the iteration of current wire then put this logic in another wire (defloop) and schedule it to run alongside your main wire>*

--8<-- "includes/license.md"
   