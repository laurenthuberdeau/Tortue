# Tortue

> [!NOTE]
> This project has since been merged in the Ribbit repo. See https://github.com/udem-dlteam/ribbit/pull/9.

This repo contains an implementation of the Ribbit Virtual Machine in Haskell
named `Tortue`.
It was done for the `IFT-6232: Compilation` class given by Marc Feeley in Winter 2022.

The VM with a basic `HELLO!` program fits in 4KB (of Haskell code) once minimized.

## How to compile

The virtual machine is split in multiple modules for ease of development and can
be compiled using `stack build`.

Since the implementation only depends on `base`, the modules can be combined in
1 single executable file with `make bundle` that can be compiled with `ghc`
using `ghc dist/rvm.hs -o runrvm`.

## How to run

Programs can be run in 2 ways.
1. Via the Ribbit compiler which creates a single executable Haskell file.
2. If you already have RVM code, a file containing the code can be passed
to `runrvm`. If no file is passed to `runrvm`, it uses the program that was
used when the VM was made (hardcoded in `inputStr`).

## Why so slow

To make it fast, make sure you pass the following options to ghc:
1. `-O2`: Enables optimizations.
1. `-threaded -with-rtsopts=-N1`: Disables the parallel runtime, which reduces productivity by more than half for some reason.

## Possible optimizations

Performance can be measured with `stack bench`.

### IORef and garbage collection

Using many IORefs in Haskell is problematic because the generational garbage
collector has to keep track of IORef write operations by adding the mutated
IORefs to the remembered set. This remembered set has to be entirely traversed
every GC, which reduces productivity when a lot of memory is allocated.

A temporary solution can be to increase nursery size (`-A16M`) so that minor
GCs happen less often.

Some solutions:
1. Manage heap manually like in C.
1. Use a different garbage collector that isn't generational. See [rts-options-gc](https://downloads.haskell.org/~ghc/latest/docs/html/users_guide/runtime_control.html#rts-options-gc).

### Primitive calls

When calling a primitive, a lookup in the list of primitives is performed.
This is not ideal and can be easily improved with an array.
A function that pattern matches on the index may be a faster and a simpler
option, depending on how GHC compiles functions with many cases on `Int`.

## Bugs

The test suite of the ribbit project passes.

## More on Ribbit

The Ribbit VM is described in the paper
`A Small Scheme VM, Compiler, and REPL in 4K` written by Samuel Yvon and Marc
Feeley, from the Université de Montréal.

The goal of the Ribbit project is to have a compact Scheme implementation that
can be used in constrained environments and that has all the expected features
of a normal Scheme system.

The Scheme programming language was chosen for it's short specification,
minimalistic design and for the expressive power of the constructs it offers.

[Link to the repo accompanying the paper](https://github.com/udem-dlteam/ribbit/)

[Link to the paper](https://www.iro.umontreal.ca/~feeley/papers/YvonFeeleyVMIL21.pdf)
