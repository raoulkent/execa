<picture>
       <source media="(prefers-color-scheme: dark)" srcset="../media/logo_dark.svg">
       <img alt="execa logo" src="../media/logo.svg" width="400">
</picture>
<br>

# 📔 API reference

This lists all available [methods](#methods) and their [options](#options). This also describes the properties of the [subprocess](#subprocess), [result](#result) and [error](#execaerror) they return.

## Methods

### execa(file, arguments?, options?)

`file`: `string | URL`\
`arguments`: `string[]`\
`options`: [`Options`](#options)\
_Returns_: [`Subprocess`](#subprocess)

Executes a command using `file ...arguments`.

More info on the [syntax](execution.md#array-syntax) and [escaping](escaping.md#array-syntax).

### execa\`command\`
### execa(options)\`command\`

`command`: `string`\
`options`: [`Options`](#options)\
_Returns_: [`Subprocess`](#subprocess)

Executes a command. `command` is a [template string](execution.md#template-string-syntax) that includes both the `file` and its `arguments`.

More info on the [syntax](execution.md#template-string-syntax) and [escaping](escaping.md#template-string-syntax).

### execa(options)

`options`: [`Options`](#options)\
_Returns_: [`execa`](#execafile-arguments-options)

Returns a new instance of Execa but with different default [`options`](#options). Consecutive calls are merged to previous ones.

[More info.](execution.md#globalshared-options)

### execaSync(file, arguments?, options?)
### execaSync\`command\`

Same as [`execa()`](#execafile-arguments-options) but synchronous.

Returns or throws a subprocess [`result`](#result). The [`subprocess`](#subprocess) is not returned: its methods and properties are not available.

[More info.](execution.md#synchronous-execution)

### $(file, arguments?, options?)

`file`: `string | URL`\
`arguments`: `string[]`\
`options`: [`Options`](#options)\
_Returns_: [`Subprocess`](#subprocess)

Same as [`execa()`](#execafile-arguments-options) but using [script-friendly default options](scripts.md#script-files).

Just like `execa()`, this can use the [template string syntax](execution.md#template-string-syntax) or [bind options](execution.md#globalshared-options). It can also be [run synchronously](#execasyncfile-arguments-options) using `$.sync()` or `$.s()`.

This is the preferred method when executing multiple commands in a script file.

[More info.](scripts.md)

### execaNode(scriptPath, arguments?, options?)

`scriptPath`: `string | URL`\
`arguments`: `string[]`\
`options`: [`Options`](#options)\
_Returns_: [`Subprocess`](#subprocess)

Same as [`execa()`](#execafile-arguments-options) but using the [`node: true`](#optionsnode) option.
Executes a Node.js file using `node scriptPath ...arguments`.

Just like `execa()`, this can use the [template string syntax](execution.md#template-string-syntax) or [bind options](execution.md#globalshared-options).

This is the preferred method when executing Node.js files.

[More info.](node.md)

### execaCommand(command, options?)

`command`: `string`\
`options`: [`Options`](#options)\
_Returns_: [`Subprocess`](#subprocess)

Executes a command. `command` is a string that includes both the `file` and its `arguments`.

This is only intended for very specific cases, such as a [REPL](https://en.wikipedia.org/wiki/Read%E2%80%93eval%E2%80%93print_loop). This should be avoided otherwise.

Just like `execa()`, this can [bind options](execution.md#globalshared-options). It can also be [run synchronously](#execasyncfile-arguments-options) using `execaCommandSync()`.

[More info.](escaping.md#user-defined-input)

## Subprocess

The return value of all [asynchronous methods](#methods) is both:
- a `Promise` resolving or rejecting with a subprocess [`result`](#result).
- a [`child_process` instance](https://nodejs.org/api/child_process.html#child_process_class_childprocess) with the following methods and properties.

[More info.](execution.md#subprocess)

### subprocess\[Symbol.asyncIterator\]()

_Returns_: [`AsyncIterable`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols#the_async_iterator_and_async_iterable_protocols)

Subprocesses are [async iterables](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/asyncIterator). They iterate over each output line.

[More info.](lines.md#progressive-splitting)

### subprocess.iterable(readableOptions?)

`readableOptions`: [`ReadableOptions`](#readableoptions)\
_Returns_: [`AsyncIterable`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols#the_async_iterator_and_async_iterable_protocols)

Same as [`subprocess[Symbol.asyncIterator]`](#subprocesssymbolasynciterator) except [options](#readableoptions) can be provided.

[More info.](lines.md#progressive-splitting)

### subprocess.pipe(file, arguments?, options?)

`file`: `string | URL`\
`arguments`: `string[]`\
`options`: [`Options`](#options) and [`PipeOptions`](#pipeoptions)\
_Returns_: [`Promise<Result>`](#result)

[Pipe](https://nodejs.org/api/stream.html#readablepipedestination-options) the subprocess' [`stdout`](#subprocessstdout) to a second Execa subprocess' [`stdin`](#subprocessstdin). This resolves with that second subprocess' [result](#result). If either subprocess is rejected, this is rejected with that subprocess' [error](#execaerror) instead.

This follows the same syntax as [`execa(file, arguments?, options?)`](#execafile-arguments-options) except both [regular options](#options) and [pipe-specific options](#pipeoptions) can be specified.

[More info.](pipe.md#array-syntax)

### subprocess.pipe\`command\`
### subprocess.pipe(options)\`command\`

`command`: `string`\
`options`: [`Options`](#options) and [`PipeOptions`](#pipeoptions)\
_Returns_: [`Promise<Result>`](#result)

Like [`subprocess.pipe(file, arguments?, options?)`](#subprocesspipefile-arguments-options) but using a [`command` template string](scripts.md#piping-stdout-to-another-command) instead. This follows the same syntax as `execa` [template strings](execution.md#template-string-syntax).

[More info.](pipe.md#template-string-syntax)

### subprocess.pipe(secondSubprocess, pipeOptions?)

`secondSubprocess`: [`execa()` return value](#subprocess)\
`pipeOptions`: [`PipeOptions`](#pipeoptions)\
_Returns_: [`Promise<Result>`](#result)

Like [`subprocess.pipe(file, arguments?, options?)`](#subprocesspipefile-arguments-options) but using the [return value](#subprocess) of another [`execa()`](#execafile-arguments-options) call instead.

[More info.](pipe.md#advanced-syntax)

#### pipeOptions

Type: `object`

#### pipeOptions.from

Type: `"stdout" | "stderr" | "all" | "fd3" | "fd4" | ...`\
Default: `"stdout"`

Which stream to pipe from the source subprocess. A [file descriptor](https://en.wikipedia.org/wiki/File_descriptor) like `"fd3"` can also be passed.

`"all"` pipes both [`stdout`](#subprocessstdout) and [`stderr`](#subprocessstderr). This requires the [`all`](#optionsall) option to be `true`.

[More info.](pipe.md#source-file-descriptor)

#### pipeOptions.to

Type: `"stdin" | "fd3" | "fd4" | ...`\
Default: `"stdin"`

Which [stream](#subprocessstdin) to pipe to the destination subprocess. A [file descriptor](https://en.wikipedia.org/wiki/File_descriptor) like `"fd3"` can also be passed.

[More info.](pipe.md#destination-file-descriptor)

#### pipeOptions.unpipeSignal

Type: [`AbortSignal`](https://developer.mozilla.org/en-US/docs/Web/API/AbortSignal)

Unpipe the subprocess when the signal aborts.

[More info.](pipe.md#unpipe)

### subprocess.kill(signal, error?)
### subprocess.kill(error?)

`signal`: `string | number`\
`error`: `Error`\
_Returns_: `boolean`

Sends a [signal](https://nodejs.org/api/os.html#signal-constants) to the subprocess. The default signal is the [`killSignal`](#optionskillsignal) option. `killSignal` defaults to `SIGTERM`, which [terminates](#erroristerminated) the subprocess.

This returns `false` when the signal could not be sent, for example when the subprocess has already exited.

When an error is passed as argument, it is set to the subprocess' [`error.cause`](#errorcause). The subprocess is then terminated with the default signal. This does not emit the [`error` event](https://nodejs.org/api/child_process.html#event-error).

[More info.](termination.md)

### subprocess.pid

_Type_: `number | undefined`

Process identifier ([PID](https://en.wikipedia.org/wiki/Process_identifier)).

This is `undefined` if the subprocess failed to spawn.

[More info.](termination.md#inter-process-termination)

### subprocess.send(message)

`message`: `unknown`\
_Returns_: `boolean`

Send a `message` to the subprocess. The type of `message` depends on the [`serialization`](#optionsserialization) option.
The subprocess receives it as a [`message` event](https://nodejs.org/api/process.html#event-message).

This returns `true` on success.

This requires the [`ipc`](#optionsipc) option to be `true`.

[More info.](ipc.md#exchanging-messages)

### subprocess.on('message', (message) => void)

`message`: `unknown`

Receives a `message` from the subprocess. The type of `message` depends on the [`serialization`](#optionsserialization) option.
The subprocess sends it using [`process.send(message)`](https://nodejs.org/api/process.html#processsendmessage-sendhandle-options-callback).

This requires the [`ipc`](#optionsipc) option to be `true`.

[More info.](ipc.md#exchanging-messages)

### subprocess.stdin

Type: [`Writable | null`](https://nodejs.org/api/stream.html#class-streamwritable)

The subprocess [`stdin`](https://en.wikipedia.org/wiki/Standard_streams#Standard_input_(stdin)) as a stream.

This is `null` if the [`stdin`](#optionsstdin) option is set to [`'inherit'`](input.md#terminal-input), [`'ignore'`](input.md#ignore-input), [`Readable`](streams.md#input) or [`integer`](input.md#terminal-input).

[More info.](streams.md#manual-streaming)

### subprocess.stdout

Type: [`Readable | null`](https://nodejs.org/api/stream.html#class-streamreadable)

The subprocess [`stdout`](https://en.wikipedia.org/wiki/Standard_streams#Standard_output_(stdout)) as a stream.

This is `null` if the [`stdout`](#optionsstdout) option is set to [`'inherit'`](output.md#terminal-output), [`'ignore'`](output.md#ignore-output), [`Writable`](streams.md#output) or [`integer`](output.md#terminal-output), or if the [`buffer`](#optionsbuffer) option is `false`.

[More info.](streams.md#manual-streaming)

### subprocess.stderr

Type: [`Readable | null`](https://nodejs.org/api/stream.html#class-streamreadable)

The subprocess [`stderr`](https://en.wikipedia.org/wiki/Standard_streams#Standard_error_(stderr)) as a stream.

This is `null` if the [`stderr`](#optionsstdout) option is set to [`'inherit'`](output.md#terminal-output), [`'ignore'`](output.md#ignore-output), [`Writable`](streams.md#output) or [`integer`](output.md#terminal-output), or if the [`buffer`](#optionsbuffer) option is `false`.

[More info.](streams.md#manual-streaming)

### subprocess.all

Type: [`Readable | undefined`](https://nodejs.org/api/stream.html#class-streamreadable)

Stream combining/interleaving [`subprocess.stdout`](#subprocessstdout) and [`subprocess.stderr`](#subprocessstderr).

This requires the [`all`](#optionsall) option to be `true`.

This is `undefined` if [`stdout`](#optionsstdout) and [`stderr`](#optionsstderr) options are set to [`'inherit'`](output.md#terminal-output), [`'ignore'`](output.md#ignore-output), [`Writable`](streams.md#output) or [`integer`](output.md#terminal-output), or if the [`buffer`](#optionsbuffer) option is `false`.

More info on [interleaving](output.md#interleaved-output) and [streaming](streams.md#manual-streaming).

### subprocess.stdio

Type: [`[Writable | null, Readable | null, Readable | null, ...Array<Writable | Readable | null>]`](https://nodejs.org/api/stream.html#class-streamreadable)

The subprocess [`stdin`](#subprocessstdin), [`stdout`](#subprocessstdout), [`stderr`](#subprocessstderr) and [other files descriptors](#optionsstdio) as an array of streams.

Each array item is `null` if the corresponding [`stdin`](#optionsstdin), [`stdout`](#optionsstdout), [`stderr`](#optionsstderr) or [`stdio`](#optionsstdio) option is set to [`'inherit'`](output.md#terminal-output), [`'ignore'`](output.md#ignore-output), [`Stream`](streams.md#output) or [`integer`](output.md#terminal-output), or if the [`buffer`](#optionsbuffer) option is `false`.

[More info.](streams.md#manual-streaming)

### subprocess.readable(readableOptions?)

`readableOptions`: [`ReadableOptions`](#readableoptions)\
_Returns_: [`Readable`](https://nodejs.org/api/stream.html#class-streamreadable) Node.js stream

Converts the subprocess to a readable stream.

[More info.](streams.md#converting-a-subprocess-to-a-stream)

#### readableOptions

Type: `object`

#### readableOptions.from

Type: `"stdout" | "stderr" | "all" | "fd3" | "fd4" | ...`\
Default: `"stdout"`

Which stream to read from the subprocess. A [file descriptor](https://en.wikipedia.org/wiki/File_descriptor) like `"fd3"` can also be passed.

`"all"` reads both [`stdout`](#subprocessstdout) and [`stderr`](#subprocessstderr). This requires the [`all`](#optionsall) option to be `true`.

[More info.](streams.md#different-file-descriptor)

#### readableOptions.binary

Type: `boolean`\
Default: `false` with [`subprocess.iterable()`](#subprocessiterablereadableoptions), `true` with [`subprocess.readable()`](#subprocessreadablereadableoptions)/[`subprocess.duplex()`](#subprocessduplexduplexoptions)

If `false`, iterates over lines. Each line is a string.

If `true`, iterates over arbitrary chunks of data. Each line is an [`Uint8Array`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array) (with [`subprocess.iterable()`](#subprocessiterablereadableoptions)) or a [`Buffer`](https://nodejs.org/api/buffer.html#class-buffer) (with [`subprocess.readable()`](#subprocessreadablereadableoptions)/[`subprocess.duplex()`](#subprocessduplexduplexoptions)).

This is always `true` when the [`encoding`](#optionsencoding) option is binary.

More info for [iterables](binary.md#iterable) and [streams](binary.md#streams).

#### readableOptions.preserveNewlines

Type: `boolean`\
Default: `false` with [`subprocess.iterable()`](#subprocessiterablereadableoptions), `true` with [`subprocess.readable()`](#subprocessreadablereadableoptions)/[`subprocess.duplex()`](#subprocessduplexduplexoptions)

If both this option and the [`binary`](#readableoptionsbinary) option is `false`, [newlines](https://en.wikipedia.org/wiki/Newline) are stripped from each line.

[More info.](lines.md#iterable)

### subprocess.writable(writableOptions?)

`writableOptions`: [`WritableOptions`](#writableoptions)\
_Returns_: [`Writable`](https://nodejs.org/api/stream.html#class-streamwritable) Node.js stream

Converts the subprocess to a writable stream.

[More info.](streams.md#converting-a-subprocess-to-a-stream)

#### writableOptions

Type: `object`

#### writableOptions.to

Type: `"stdin" | "fd3" | "fd4" | ...`\
Default: `"stdin"`

Which [stream](#subprocessstdin) to write to the subprocess. A [file descriptor](https://en.wikipedia.org/wiki/File_descriptor) like `"fd3"` can also be passed.

[More info.](streams.md#different-file-descriptor)

### subprocess.duplex(duplexOptions?)

`duplexOptions`: [`ReadableOptions | WritableOptions`](#readableoptions)\
_Returns_: [`Duplex`](https://nodejs.org/api/stream.html#class-streamduplex) Node.js stream

Converts the subprocess to a duplex stream.

[More info.](streams.md#converting-a-subprocess-to-a-stream)

## Result

Type: `object`

[Result](execution.md#result) of a subprocess successful execution.

When the subprocess [fails](errors.md#subprocess-failure), it is rejected with an [`ExecaError`](#execaerror) instead.

### result.stdout

Type: `string | Uint8Array | string[] | Uint8Array[] | unknown[] | undefined`

The output of the subprocess on [`stdout`](https://en.wikipedia.org/wiki/Standard_streams#Standard_output_(stdout)).

This is `undefined` if the [`stdout`](#optionsstdout) option is set to only [`'inherit'`](output.md#terminal-output), [`'ignore'`](output.md#ignore-output), [`Writable`](streams.md#output) or [`integer`](output.md#terminal-output), or if the [`buffer`](#optionsbuffer) option is `false`.

This is an array if the [`lines`](#optionslines) option is `true`, or if the `stdout` option is a [transform in object mode](transform.md#object-mode).

[More info.](output.md#stdout-and-stderr)

### result.stderr

Type: `string | Uint8Array | string[] | Uint8Array[] | unknown[] | undefined`

The output of the subprocess on [`stderr`](https://en.wikipedia.org/wiki/Standard_streams#Standard_error_(stderr)).

This is `undefined` if the [`stderr`](#optionsstderr) option is set to only [`'inherit'`](output.md#terminal-output), [`'ignore'`](output.md#ignore-output), [`Writable`](streams.md#output) or [`integer`](output.md#terminal-output), or if the [`buffer`](#optionsbuffer) option is `false`.

This is an array if the [`lines`](#optionslines) option is `true`, or if the `stderr` option is a [transform in object mode](transform.md#object-mode).

[More info.](output.md#stdout-and-stderr)

### result.all

Type: `string | Uint8Array | string[] | Uint8Array[] | unknown[] | undefined`

The output of the subprocess with [`result.stdout`](#resultstdout) and [`result.stderr`](#resultstderr) interleaved.

This requires the [`all`](#optionsall) option to be `true`.

This is `undefined` if both [`stdout`](#optionsstdout) and [`stderr`](#optionsstderr) options are set to only [`'inherit'`](output.md#terminal-output), [`'ignore'`](output.md#ignore-output), [`Writable`](streams.md#output) or [`integer`](output.md#terminal-output), or if the [`buffer`](#optionsbuffer) option is `false`.

This is an array if the [`lines`](#optionslines) option is `true`, or if either the `stdout` or `stderr` option is a [transform in object mode](transform.md#object-mode).

[More info.](output.md#interleaved-output)

### result.stdio

Type: `Array<string | Uint8Array | string[] | Uint8Array[] | unknown[] | undefined>`

The output of the subprocess on [`stdin`](#optionsstdin), [`stdout`](#optionsstdout), [`stderr`](#optionsstderr) and [other file descriptors](#optionsstdio).

Items are `undefined` when their corresponding [`stdio`](#optionsstdio) option is set to [`'inherit'`](output.md#terminal-output), [`'ignore'`](output.md#ignore-output), [`Writable`](streams.md#output) or [`integer`](output.md#terminal-output), or if the [`buffer`](#optionsbuffer) option is `false`.

Items are arrays when their corresponding `stdio` option is a [transform in object mode](transform.md#object-mode).

[More info.](output.md#additional-file-descriptors)

### result.pipedFrom

Type: [`Array<Result | ExecaError>`](#result)

[Results](#result) of the other subprocesses that were piped into this subprocess.

This array is initially empty and is populated each time the [`subprocess.pipe()`](#subprocesspipefile-arguments-options) method resolves.

[More info.](pipe.md#result)

### result.command

Type: `string`

The file and [arguments](input.md#command-arguments) that were run.

[More info.](debugging.md#command)

### result.escapedCommand

Type: `string`

Same as [`command`](#resultcommand) but escaped.

[More info.](debugging.md#command)

### result.cwd

Type: `string`

The [current directory](#optionscwd) in which the command was run.

[More info.](environment.md#current-directory)

### result.durationMs

Type: `number`

Duration of the subprocess, in milliseconds.

[More info.](debugging.md#duration)

### result.failed

Type: `boolean`

Whether the subprocess failed to run.

When this is `true`, the result is an [`ExecaError`](#execaerror) instance with additional error-related properties.

[More info.](errors.md#subprocess-failure)

## ExecaError
## ExecaSyncError

Type: `Error`

Result of a subprocess [failed execution](errors.md#subprocess-failure).

This error is thrown as an exception. If the [`reject`](#optionsreject) option is false, it is returned instead.

This has the same shape as [successful results](#result), with the following additional properties.

[More info.](errors.md)

### error.message

Type: `string`

Error message when the subprocess [failed](errors.md#subprocess-failure) to run.

[More info.](errors.md#error-message)

### error.shortMessage

Type: `string`

This is the same as [`error.message`](#errormessage) except it does not include the subprocess [output](output.md).

[More info.](errors.md#error-message)

### error.originalMessage

Type: `string | undefined`

Original error message. This is the same as [`error.message`](#errormessage) excluding the subprocess [output](output.md) and some additional information added by Execa.

[More info.](errors.md#error-message)

### error.cause

Type: `unknown | undefined`

Underlying error, if there is one. For example, this is set by [`subprocess.kill(error)`](#subprocesskillerror).

This is usually an `Error` instance.

[More info.](termination.md#error-message-and-stack-trace)

### error.code

Type: `string | undefined`

Node.js-specific [error code](https://nodejs.org/api/errors.html#errorcode), when available.

### error.timedOut

Type: `boolean`

Whether the subprocess timed out due to the [`timeout`](#optionstimeout) option.

[More info.](termination.md#timeout)

### error.isCanceled

Type: `boolean`

Whether the subprocess was canceled using the [`cancelSignal`](#optionscancelsignal) option.

[More info.](termination.md#canceling)

### error.isMaxBuffer

Type: `boolean`

Whether the subprocess failed because its output was larger than the [`maxBuffer`](#optionsmaxbuffer) option.

[More info.](output.md#big-output)

### error.isTerminated

Type: `boolean`

Whether the subprocess was terminated by a [signal](termination.md#signal-termination) (like [`SIGTERM`](termination.md#sigterm)) sent by either:
- The current process.
- [Another process](termination.md#inter-process-termination). This case is [not supported on Windows](https://nodejs.org/api/process.html#signal-events).

[More info.](termination.md#signal-name-and-description)

### error.exitCode

Type: `number | undefined`

The numeric [exit code](https://en.wikipedia.org/wiki/Exit_status) of the subprocess that was run.

This is `undefined` when the subprocess could not be spawned or was terminated by a [signal](#errorsignal).

[More info.](errors.md#exit-code)

### error.signal

Type: `string | undefined`

The name of the [signal](termination.md#signal-termination) (like [`SIGTERM`](termination.md#sigterm)) that terminated the subprocess, sent by either:
- The current process.
- [Another process](termination.md#inter-process-termination). This case is [not supported on Windows](https://nodejs.org/api/process.html#signal-events).

If a signal terminated the subprocess, this property is defined and included in the [error message](#errormessage). Otherwise it is `undefined`.

[More info.](termination.md#signal-name-and-description)

### error.signalDescription

Type: `string | undefined`

A human-friendly description of the [signal](termination.md#signal-termination) that was used to terminate the subprocess.

If a signal terminated the subprocess, this property is defined and included in the error message. Otherwise it is `undefined`. It is also `undefined` when the signal is very uncommon which should seldomly happen.

[More info.](termination.md#signal-name-and-description)

## Options

Type: `object`

This lists all options for [`execa()`](#execafile-arguments-options) and the [other methods](#methods).

The following options [can specify different values](output.md#stdoutstderr-specific-options) for [`stdout`](#optionsstdout) and [`stderr`](#optionsstderr): [`verbose`](#optionsverbose), [`lines`](#optionslines), [`stripFinalNewline`](#optionsstripfinalnewline), [`buffer`](#optionsbuffer), [`maxBuffer`](#optionsmaxbuffer).

### options.preferLocal

Type: `boolean`\
Default: `true` with [`$`](#file-arguments-options), `false` otherwise

Prefer locally installed binaries when looking for a binary to execute.

[More info.](environment.md#local-binaries)

### options.localDir

Type: `string | URL`\
Default: [`cwd`](#optionscwd) option

Preferred path to find locally installed binaries, when using the [`preferLocal`](#optionspreferlocal) option.

[More info.](environment.md#local-binaries)

### options.node

Type: `boolean`\
Default: `true` with [`execaNode()`](#execanodescriptpath-arguments-options), `false` otherwise

If `true`, runs with Node.js. The first argument must be a Node.js file.

[More info.](node.md)

### options.nodeOptions

Type: `string[]`\
Default: [`process.execArgv`](https://nodejs.org/api/process.html#process_process_execargv) (current Node.js CLI options)

List of [CLI flags](https://nodejs.org/api/cli.html#cli_options) passed to the [Node.js executable](#optionsnodepath).

Requires the [`node`](#optionsnode) option to be `true`.

[More info.](node.md#nodejs-cli-flags)

### options.nodePath

Type: `string | URL`\
Default: [`process.execPath`](https://nodejs.org/api/process.html#process_process_execpath) (current Node.js executable)

Path to the Node.js executable.

Requires the [`node`](#optionsnode) option to be `true`.

[More info.](node.md#nodejs-version)

### options.shell

Type: `boolean | string | URL`\
Default: `false`

If `true`, runs the command inside of a [shell](https://en.wikipedia.org/wiki/Shell_(computing)).

Uses [`/bin/sh`](https://en.wikipedia.org/wiki/Unix_shell) on UNIX and [`cmd.exe`](https://en.wikipedia.org/wiki/Cmd.exe) on Windows. A different shell can be specified as a string. The shell should understand the `-c` switch on UNIX or `/d /s /c` on Windows.

We [recommend against](shell.md#avoiding-shells) using this option.

[More info.](shell.md)

### options.cwd

Type: `string | URL`\
Default: `process.cwd()`

Current [working directory](https://en.wikipedia.org/wiki/Working_directory) of the subprocess.

This is also used to resolve the [`nodePath`](#optionsnodepath) option when it is a relative path.

[More info.](environment.md#current-directory)

### options.env

Type: `object`\
Default: [`process.env`](https://nodejs.org/api/process.html#processenv)

[Environment variables](https://en.wikipedia.org/wiki/Environment_variable).

Unless the [`extendEnv`](#optionsextendenv) option is `false`, the subprocess also uses the current process' environment variables ([`process.env`](https://nodejs.org/api/process.html#processenv)).

[More info.](input.md#environment-variables)

### options.extendEnv

Type: `boolean`\
Default: `true`

If `true`, the subprocess uses both the [`env`](#optionsenv) option and the current process' environment variables ([`process.env`](https://nodejs.org/api/process.html#processenv)).
If `false`, only the `env` option is used, not `process.env`.

[More info.](input.md#environment-variables)

### options.input

Type: `string | Uint8Array | stream.Readable`

Write some input to the subprocess' [`stdin`](https://en.wikipedia.org/wiki/Standard_streams#Standard_input_(stdin)).

See also the [`inputFile`](#optionsinputfile) and [`stdin`](#optionsstdin) options.

[More info.](input.md#string-input)

### options.inputFile

Type: `string | URL`

Use a file as input to the subprocess' [`stdin`](https://en.wikipedia.org/wiki/Standard_streams#Standard_input_(stdin)).

See also the [`input`](#optionsinput) and [`stdin`](#optionsstdin) options.

[More info.](input.md#file-input)

### options.stdin

Type: `string | number | stream.Readable | ReadableStream | TransformStream | URL | {file: string} | Uint8Array | Iterable<string | Uint8Array | unknown> | AsyncIterable<string | Uint8Array | unknown> | GeneratorFunction<string | Uint8Array | unknown> | AsyncGeneratorFunction<string | Uint8Array | unknown> | {transform: GeneratorFunction | AsyncGeneratorFunction | Duplex | TransformStream}` (or a tuple of those types)\
Default: `'inherit'` with [`$`](#file-arguments-options), `'pipe'` otherwise

How to setup the subprocess' [standard input](https://en.wikipedia.org/wiki/Standard_streams#Standard_input_(stdin)). This can be [`'pipe'`](streams.md#manual-streaming), [`'overlapped'`](windows.md#asynchronous-io), [`'ignore`](input.md#ignore-input), [`'inherit'`](input.md#terminal-input), a [file descriptor integer](input.md#terminal-input), a [Node.js `Readable` stream](streams.md#input), a web [`ReadableStream`](streams.md#web-streams), a [`{ file: 'path' }` object](input.md#file-input), a [file URL](input.md#file-input), an [`Iterable`](streams.md#iterables-as-input) (including an [array of strings](input.md#string-input)), an [`AsyncIterable`](streams.md#iterables-as-input), an [`Uint8Array`](binary.md#binary-input), a [generator function](transform.md), a [`Duplex`](transform.md#duplextransform-streams) or a web [`TransformStream`](transform.md#duplextransform-streams).

This can be an [array of values](output.md#multiple-targets) such as `['inherit', 'pipe']` or `[fileUrl, 'pipe']`.

More info on [available values](input.md), [streaming](streams.md) and [transforms](transform.md).

### options.stdout

Type: `string | number | stream.Writable | WritableStream | TransformStream | URL | {file: string} | GeneratorFunction<string | Uint8Array | unknown> | AsyncGeneratorFunction<string | Uint8Array | unknown>  | {transform: GeneratorFunction | AsyncGeneratorFunction | Duplex | TransformStream}` (or a tuple of those types)\
Default: `pipe`

How to setup the subprocess' [standard output](https://en.wikipedia.org/wiki/Standard_streams#Standard_input_(stdin)). This can be [`'pipe'`](output.md#stdout-and-stderr), [`'overlapped'`](windows.md#asynchronous-io), [`'ignore`](output.md#ignore-output), [`'inherit'`](output.md#terminal-output), a [file descriptor integer](output.md#terminal-output), a [Node.js `Writable` stream](streams.md#output), a web [`WritableStream`](streams.md#web-streams), a [`{ file: 'path' }` object](output.md#file-output), a [file URL](output.md#file-output), a [generator function](transform.md), a [`Duplex`](transform.md#duplextransform-streams) or a web [`TransformStream`](transform.md#duplextransform-streams).

This can be an [array of values](output.md#multiple-targets) such as `['inherit', 'pipe']` or `[fileUrl, 'pipe']`.

More info on [available values](output.md), [streaming](streams.md) and [transforms](transform.md).

### options.stderr

Type: `string | number | stream.Writable | WritableStream | TransformStream | URL | {file: string} | GeneratorFunction<string | Uint8Array | unknown> | AsyncGeneratorFunction<string | Uint8Array | unknown> | {transform: GeneratorFunction | AsyncGeneratorFunction | Duplex | TransformStream}` (or a tuple of those types)\
Default: `pipe`

How to setup the subprocess' [standard error](https://en.wikipedia.org/wiki/Standard_streams#Standard_input_(stdin)). This can be [`'pipe'`](output.md#stdout-and-stderr), [`'overlapped'`](windows.md#asynchronous-io), [`'ignore`](output.md#ignore-output), [`'inherit'`](output.md#terminal-output), a [file descriptor integer](output.md#terminal-output), a [Node.js `Writable` stream](streams.md#output), a web [`WritableStream`](streams.md#web-streams), a [`{ file: 'path' }` object](output.md#file-output), a [file URL](output.md#file-output), a [generator function](transform.md), a [`Duplex`](transform.md#duplextransform-streams) or a web [`TransformStream`](transform.md#duplextransform-streams).

This can be an [array of values](output.md#multiple-targets) such as `['inherit', 'pipe']` or `[fileUrl, 'pipe']`.

More info on [available values](output.md), [streaming](streams.md) and [transforms](transform.md).

### options.stdio

Type: `string | Array<string | number | stream.Readable | stream.Writable | ReadableStream | WritableStream | TransformStream | URL | {file: string} | Uint8Array | Iterable<string> | Iterable<Uint8Array> | Iterable<unknown> | AsyncIterable<string | Uint8Array | unknown> | GeneratorFunction<string | Uint8Array | unknown> | AsyncGeneratorFunction<string | Uint8Array | unknown> | {transform: GeneratorFunction | AsyncGeneratorFunction | Duplex | TransformStream}>` (or a tuple of those types)\
Default: `pipe`

Like the [`stdin`](#optionsstdin), [`stdout`](#optionsstdout) and [`stderr`](#optionsstderr) options but for all [file descriptors](https://en.wikipedia.org/wiki/File_descriptor) at once. For example, `{stdio: ['ignore', 'pipe', 'pipe']}` is the same as `{stdin: 'ignore', stdout: 'pipe', stderr: 'pipe'}`.

A single string can be used [as a shortcut](output.md#shortcut).

The array can have more than 3 items, to create [additional file descriptors](output.md#additional-file-descriptors) beyond [`stdin`](#optionsstdin)/[`stdout`](#optionsstdout)/[`stderr`](#optionsstderr).

More info on [available values](output.md), [streaming](streams.md) and [transforms](transform.md).

### options.all

Type: `boolean`\
Default: `false`

Add a [`subprocess.all`](#subprocessall) stream and a [`result.all`](#resultall) property.

[More info.](output.md#interleaved-output)

### options.encoding

Type: `'utf8' | 'utf16le' | 'buffer' | 'hex' | 'base64' | 'base64url' | 'latin1' | 'ascii'`\
Default: `'utf8'`

If the subprocess outputs text, specifies its character encoding, either [`'utf8'`](https://en.wikipedia.org/wiki/UTF-8) or [`'utf16le'`](https://en.wikipedia.org/wiki/UTF-16).

If it outputs binary data instead, this should be either:
- `'buffer'`: returns the binary output as an [`Uint8Array`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array).
- [`'hex'`](https://en.wikipedia.org/wiki/Hexadecimal), [`'base64'`](https://en.wikipedia.org/wiki/Base64), [`'base64url'`](https://en.wikipedia.org/wiki/Base64#URL_applications), [`'latin1'`](https://nodejs.org/api/buffer.html#buffers-and-character-encodings) or [`'ascii'`](https://nodejs.org/api/buffer.html#buffers-and-character-encodings): encodes the binary output as a string.

The output is available with [`result.stdout`](#resultstdout), [`result.stderr`](#resultstderr) and [`result.stdio`](#resultstdio).

[More info.](binary.md)

### options.lines

Type: `boolean`\
Default: `false`

Set [`result.stdout`](#resultstdout), [`result.stderr`](#resultstdout), [`result.all`](#resultall) and [`result.stdio`](#resultstdio) as arrays of strings, splitting the subprocess' output into lines.

This cannot be used if the [`encoding`](#optionsencoding) option is [binary](binary.md#binary-output).

By default, this applies to both `stdout` and `stderr`, but [different values can also be passed](output.md#stdoutstderr-specific-options).

[More info.](lines.md#simple-splitting)

### options.stripFinalNewline

Type: `boolean`\
Default: `true`

Strip the final [newline character](https://en.wikipedia.org/wiki/Newline) from the output.

If the [`lines`](#optionslines) option is true, this applies to each output line instead.

By default, this applies to both `stdout` and `stderr`, but [different values can also be passed](output.md#stdoutstderr-specific-options).

[More info.](lines.md#newlines)

### options.maxBuffer

Type: `number`\
Default: `100_000_000`

Largest amount of data allowed on [`stdout`](#resultstdout), [`stderr`](#resultstderr) and [`stdio`](#resultstdio).

By default, this applies to both `stdout` and `stderr`, but [different values can also be passed](output.md#stdoutstderr-specific-options).

[More info.](output.md#big-output)

### options.buffer

Type: `boolean`\
Default: `true`

When `buffer` is `false`, the [`result.stdout`](#resultstdout), [`result.stderr`](#resultstderr), [`result.all`](#resultall) and [`result.stdio`](#resultstdio) properties are not set.

By default, this applies to both `stdout` and `stderr`, but [different values can also be passed](output.md#stdoutstderr-specific-options).

[More info.](output.md#low-memory)

### options.ipc

Type: `boolean`\
Default: `true` if the [`node`](#optionsnode) option is enabled, `false` otherwise

Enables exchanging messages with the subprocess using [`subprocess.send(message)`](#subprocesssendmessage) and [`subprocess.on('message', (message) => {})`](#subprocessonmessage-message--void).

[More info.](ipc.md)

### options.serialization

Type: `'json' | 'advanced'`\
Default: `'advanced'`

Specify the kind of serialization used for sending messages between subprocesses when using the [`ipc`](#optionsipc) option.

[More info.](ipc.md#message-type)

### options.verbose

Type: `'none' | 'short' | 'full'`\
Default: `'none'`

If `verbose` is `'short'`, prints the command on [`stderr`](https://en.wikipedia.org/wiki/Standard_streams#Standard_error_(stderr)): its file, arguments, duration and (if it failed) error message.

If `verbose` is `'full'`, the command's [`stdout`](https://en.wikipedia.org/wiki/Standard_streams#Standard_output_(stdout)) and `stderr` are also printed.

By default, this applies to both `stdout` and `stderr`, but [different values can also be passed](output.md#stdoutstderr-specific-options).

[More info.](debugging.md#verbose-mode)

### options.reject

Type: `boolean`\
Default: `true`

Setting this to `false` resolves the [result's promise](#subprocess) with the [error](#execaerror) instead of rejecting it.

[More info.](errors.md#preventing-exceptions)

### options.timeout

Type: `number`\
Default: `0`

If `timeout` is greater than `0`, the subprocess will be [terminated](#optionskillsignal) if it runs for longer than that amount of milliseconds.

On timeout, [`result.timedOut`](#errortimedout) becomes `true`.

[More info.](termination.md#timeout)

### options.cancelSignal

Type: [`AbortSignal`](https://developer.mozilla.org/en-US/docs/Web/API/AbortSignal)

You can abort the subprocess using [`AbortController`](https://developer.mozilla.org/en-US/docs/Web/API/AbortController).

When `AbortController.abort()` is called, [`result.isCanceled`](#erroriscanceled) becomes `true`.

[More info.](termination.md#canceling)

### options.forceKillAfterDelay

Type: `number | false`\
Default: `5000`

If the subprocess is terminated but does not exit, forcefully exit it by sending [`SIGKILL`](https://en.wikipedia.org/wiki/Signal_(IPC)#SIGKILL).

[More info.](termination.md#forceful-termination)

### options.killSignal

Type: `string | number`\
Default: `'SIGTERM'`

Default [signal](https://en.wikipedia.org/wiki/Signal_(IPC)) used to terminate the subprocess.

This can be either a name (like [`'SIGTERM'`](termination.md#sigterm)) or a number (like `9`).

[More info.](termination.md#default-signal)

### options.detached

Type: `boolean`\
Default: `false`

Run the subprocess independently from the current process.

[More info.](environment.md#background-subprocess)

### options.cleanup

Type: `boolean`\
Default: `true`

Kill the subprocess when the current process exits.

[More info.](termination.md#current-process-exit)

### options.uid

Type: `number`\
Default: current user identifier

Sets the [user identifier](https://en.wikipedia.org/wiki/User_identifier) of the subprocess.

[More info.](windows.md#uid-and-gid)

### options.gid

Type: `number`\
Default: current group identifier

Sets the [group identifier](https://en.wikipedia.org/wiki/Group_identifier) of the subprocess.

[More info.](windows.md#uid-and-gid)

### options.argv0

Type: `string`\
Default: file being executed

Value of [`argv[0]`](https://nodejs.org/api/process.html#processargv0) sent to the subprocess.

### options.windowsHide

Type: `boolean`\
Default: `true`

On Windows, do not create a new console window.

[More info.](windows.md#console-window)

### options.windowsVerbatimArguments

Type: `boolean`\
Default: `true` if the [`shell`](#optionsshell) option is `true`, `false` otherwise

If `false`, escapes the command arguments on Windows.

[More info.](windows.md#cmdexe-escaping)

## Transform options

A transform or an [array of transforms](transform.md#combining) can be passed to the [`stdin`](#optionsstdin), [`stdout`](#optionsstdout), [`stderr`](#optionsstderr) or [`stdio`](#optionsstdio) option.

A transform is either a [generator function](#transformoptionstransform) or a plain object with the following members.

[More info.](transform.md)

### transformOptions.transform

Type: `GeneratorFunction<string | Uint8Array | unknown>` | `AsyncGeneratorFunction<string | Uint8Array | unknown>`

Map or [filter](transform.md#filtering) the [input](input.md) or [output](output.md) of the subprocess.

More info [here](transform.md#summary) and [there](transform.md#sharing-state).

### transformOptions.final

Type: `GeneratorFunction<string | Uint8Array | unknown>` | `AsyncGeneratorFunction<string | Uint8Array | unknown>`

Create additional lines after the last one.

[More info.](transform.md#finalizing)

### transformOptions.binary

Type: `boolean`\
Default: `false`

If `true`, iterate over arbitrary chunks of `Uint8Array`s instead of line `string`s.

[More info.](binary.md#transforms)

### transformOptions.preserveNewlines

Type: `boolean`\
Default: `false`

If `true`, keep newlines in each `line` argument. Also, this allows multiple `yield`s to produces a single line.

[More info.](lines.md#transforms)

### transformOptions.objectMode

Type: `boolean`\
Default: `false`

If `true`, allow [`transformOptions.transform`](#transformoptionstransform) and [`transformOptions.final`](#transformoptionsfinal) to return any type, not just `string` or `Uint8Array`.

[More info.](transform.md#object-mode)

<hr>

[**Previous**: 🔍 Differences with Bash and zx](bash.md)\
[**Top**: Table of contents](../readme.md#documentation)