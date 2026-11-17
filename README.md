# Custom Unix Shell (C++)

A lightweight Unix-like command-line shell implemented in **C++23**. The project explores how interactive shells parse user input, resolve commands, launch processes, connect pipelines, and redirect standard output and error streams.

> This is an educational shell, not a drop-in replacement for Bash or POSIX `sh`.

## Features implemented

- **Interactive prompt** powered by GNU Readline.
- **Command parsing** with support for whitespace-delimited arguments, single quotes, double quotes, and selected backslash-escape behavior.
- **Built-in commands**:
  - `exit` — exit the shell and write command history.
  - `echo` — print arguments.
  - `pwd` — print the current working directory.
  - `cd` — change the current working directory (including `~` / the home directory).
  - `type` — identify supported built-ins or locate an executable through `PATH`.
  - `history` — display command history and support `-r`, `-w`, and `-a` history-file operations.
- **External command execution** by searching directories in `PATH` and launching programs in child processes.
- **Pipelines** using `|` to connect commands.
- **I/O redirection**:
  - `>` and `1>` — redirect standard output.
  - `>>` and `1>>` — append standard output.
  - `2>` — redirect standard error.
  - `2>>` — append standard error.
- **Command-name completion** for selected built-ins and executable programs found in `PATH`.
- **Persistent command history**, using `HISTFILE` when set and `.my_shell_history` otherwise.

## Tech stack

- C++23
- GNU Readline
- CMake
- Linux / POSIX process and file-descriptor APIs (`fork`, `exec`, `pipe`, `dup2`, `waitpid`, `open`, `chdir`)

## Project structure

```text
.
├── include/
│   ├── builtins.h
│   ├── command.h
│   ├── completion.h
│   ├── parser.h
│   ├── redirection.h
│   └── utils.h
├── src/
│   ├── builtins.cpp
│   ├── completion.cpp
│   ├── main.cpp
│   ├── parser.cpp
│   ├── redirection.cpp
│   └── utils.cpp
├── CMakeLists.txt
└── README.md
```

## Build and run

### Prerequisites

- Linux or another compatible POSIX environment
- A C++ compiler with C++23 support (for example, GCC 13+)
- CMake 3.13+
- GNU Readline development headers and library

On Ubuntu / Debian:

```bash
sudo apt update
sudo apt install -y build-essential cmake libreadline-dev
```

### Compile

From the repository root:

```bash
cmake -S . -B build
cmake --build build
```

### Launch

```bash
./build/shell
```

You should see the shell prompt:

```text
$
```

Enter `exit` or press `Ctrl+D` to leave the shell.

## Usage examples

```text
$ pwd
/home/user/codecrafters-shell-cpp

$ echo Hello from my shell
Hello from my shell

$ type ls
ls is /usr/bin/ls

$ ls
...directory contents...

$ printf "first\nsecond\n" | wc -l
2

$ echo hello > output.txt
$ cat output.txt
hello

$ echo another line >> output.txt
$ cat output.txt
hello
another line
```

The exact output of external commands depends on the host system and installed programs. In this implementation, use spaces around pipeline and redirection operators (for example, `command | command` and `echo hello > file`).

## Command history

The shell reads and writes history using the file named by `HISTFILE`. If `HISTFILE` is not set, it uses `.my_shell_history` in the current working directory.

```bash
HISTFILE="$HOME/.my_shell_history" ./build/shell
```

Inside the shell:

```text
history
history 10
history -r /path/to/history
history -w /path/to/history
history -a /path/to/history
```

## Implementation overview

1. Read interactive input through GNU Readline.
2. Tokenize the input while tracking single-quoted and double-quoted regions and handling selected escapes.
3. Extract supported output/error redirection operators into a `Command` structure.
4. Split pipeline stages and create operating-system pipes when a pipeline is present.
5. Run built-ins in the shell process for ordinary commands; run pipeline stages in child processes.
6. Resolve external programs through `PATH`, create a child process, and wait for it to finish.

## Current scope and limitations

This project intentionally implements a subset of shell behavior. It does **not** aim to provide full Bash/POSIX compatibility. In particular:

- Shell grammar is intentionally simplified; operators should be separated by spaces.
- Features such as variable expansion, globbing, command substitution, job control, and here-documents are not implemented.
- Completion is for command names, not a full shell completion system.
- Quoting and escaping cover a subset of shell rules; edge cases may differ from Bash.
- Redirection and pipeline combinations should be tested independently before relying on them for complex command sequences.

## Future improvements

- Add automated unit and integration tests for parsing, built-ins, pipelines, and redirection.
- Improve shell grammar so operators can be recognized without surrounding spaces.
- Add environment-variable expansion and globbing.
- Add job control and signal handling.
- Improve error reporting and validate file-descriptor handling for combined redirection and pipelines.
