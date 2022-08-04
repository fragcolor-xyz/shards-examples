# Appendix A - Developer toolbox

## Code Editor
Code editors allow you to write, debug, and execute code in the programming language of your choice. They also have a host of features and plugins that make life easier for developers.

[VS Code](https://code.visualstudio.com/) is a good option to start with.
Learn how to set it up and use it [here](https://code.visualstudio.com/learn).

## Loading scripts
*slurp - read file content into a string*
https://docs.fragcolor.xyz/functions/strings/#slurp

*undocumented functions - path, override-root-path, import (`SHCore.cpp`)*
*importing code/files via shard `load-file` and `load-file-once` (`...\src\mal\lib\load-file-once.mal`)*
https://github.com/fragcolor-xyz/shards/blob/devel/src/tests/loadme.clj
https://github.com/fragcolor-xyz/shards/blob/devel/src/tests/loadme-extra.clj

*file-system reads, file-system writes*
https://github.com/fragcolor-xyz/shards/blob/devel/src/tests/edn.edn

*running external processes*
https://docs.fragcolor.xyz/shards/Process/
https://github.com/fragcolor-xyz/shards/blob/devel/src/tests/shell.clj

## Hot reload
*<explain `WireLoader`/ `WireRunner` - WireLoader/ WireRunner watches and runs a script file if it changes>*
https://github.com/fragcolor-xyz/shards/blob/devel/src/tests/loader.clj

;; main entry point (loop)
(defloop main-wire
  (WireLoader (Wire* "main.edn"))) ;; WireLoader watches main.edn, and if the watched file changes it will run this edn file

;; schedule and run the main loop
(defmesh root)
(schedule root main-wire)
(run root)

## Code debugging
*<using `Log`>*
*<explain how to fix>* 
    - 'is not a malSequence' or 'is not a `malNumber` errors - i.e.
    - where to use functions (like `prn`) and where to used shards (like `Msg`)

standard output
https://docs.fragcolor.xyz/shards/General/Log/
https://docs.fragcolor.xyz/shards/General/Msg/
https://docs.fragcolor.xyz/functions/standard-output/

## Error handling
section on try-catch error handler for shards:
  - `Maybe` shard (illustrate usage with no-match-found error in `Match` shard)
  - function `throw`
https://github.com/fragcolor-xyz/shards/blob/devel/src/tests/general.edn
 
*< handling failures using Expect*, ExpectLike, ExpectWire, ExpectString... >*
https://github.com/fragcolor-xyz/shards/blob/devel/src/tests/failures.clj
   

## Unit testing
*< using Assert.Is >*

--8<-- "includes/license.md"
