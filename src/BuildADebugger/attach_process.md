# Attaching To a Process

- in linux, DWARF is the debugging information format 
- without it, it is IMPOSSIBLE to retrieve the source file from an executable
- ptrace: gives a process be able to control another
```C
    extern long int ptrace (enum __ptrace_request __request, ...);
    - PTRACE_ATTACH: attach to a process
    - PTRACE_TRACEME: makes the process tracable
    - PTRACE_DETACH: detach from a process
```

```cpp
    if(ptrace(PTRACE_ATTACH, pid, nullptr, nullptr) < 0){
        std::perror("could not attach");
        return -1;
    }
```

- fork(): clones the process and returns a pid_t
    - pid == 0: current process is the child
    - pid != 0: current process is the parent
- execX(): runs a new process (there are a lot of types)

```c
    /* Execute PATH with arguments ARGV and environment from `environ'.  */
extern int execv (const char *__path, char *const __argv[])
     __THROW __nonnull ((1, 2));

/* Execute PATH with all arguments after PATH until a NULL pointer,
   and the argument after that for environment.  */
extern int execle (const char *__path, const char *__arg, ...)
     __THROW __nonnull ((1, 2));

/* Execute PATH with all arguments after PATH until
   a NULL pointer and environment from `environ'.  */
extern int execl (const char *__path, const char *__arg, ...)
     __THROW __nonnull ((1, 2));

/* Execute FILE, searching in the `PATH' environment variable if it contains
   no slashes, with arguments ARGV and environment from `environ'.  */
extern int execvp (const char *__file, char *const __argv[])
     __THROW __nonnull ((1, 2));

/* Execute FILE, searching in the `PATH' environment variable if
   it contains no slashes, with all arguments after FILE until a
   NULL pointer and environment from `environ'.  */
extern int execlp (const char *__file, const char *__arg, ...)
     __THROW __nonnull ((1, 2));
```

- editline/readline.h: CLI library
- needs to be installed locally
```C
line = readline("std> ")                            /*read a line :)*/
add_history(line);                                  /*adds line to history*/
line == history_list()[history_length - 1]->line;   /*accesses the history list*/

```