# Minishell İş Bölümü Stratejisi

## 👤 KİŞİ 1: Frontend & Parsing (Ön Uç & Ayrıştırma)

### Ana Sorumluluklar
- **Lexical Analysis (Tokenization)**
  - Input string'i token'lara ayırma
  - Quote handling (' ve ")
  - Whitespace ve özel karakter yönetimi

- **Syntax Parsing**
  - Token'ları command structure'a dönüştürme
  - Pipe chain'leri parse etme
  - Redirection'ları belirleme

- **Environment Variable Expansion (Ortam Değişkeni Genişletme)**
  - `$VAR` expansion
  - `$?` exit status handling
  - Quote içindeki expansion kuralları

- **Signal Handling (Sinyal Yönetimi)**
  - Ctrl-C, Ctrl-D, Ctrl-\ handling
  - Global signal variable yönetimi
  - Interactive mode davranışları

---

## 👤 KİŞİ 2: Backend & Execution (Arka Uç & Yürütme)

### Ana Sorumluluklar
- **Command Execution (Komut Yürütme)**
  - Process creation (fork/execve)
  - PATH variable ile executable bulma
  - Wait/waitpid ile process management

- **Built-in Commands (Dahili Komutlar)**
  - echo, cd, pwd, export, unset, env, exit
  - Her built-in için error handling
  - Environment variable yönetimi

- **Pipe Implementation**
  - Multi-command pipeline execution
  - Process chain management
  - Pipe creation ve cleanup

- **Redirections (Yönlendirmeler)**
  - Input/Output redirections (<, >)
  - Append mode (>>)
  - Here document (<<)

---

## 🔄 Ortak Sorumluluklar

### Her İki Kişi
- **Memory Management:** Kendi modüllerindeki memory leak'leri
- **Error Handling:** Robust error checking
- **Testing:** Kendi modülleri için test case'ler

### Integration Görevleri
- Main loop implementasyonu
- Data structure'ların finalize edilmesi
- Cross-module testing
- Norm kontrolü

---

## 📁 Dosya Yapısı

```
minishell/
├── includes/
│   ├── minishell.h          // Ana header: tüm tanımlar, prototipler, system includes
│   ├── parsing.h            // Parser modülü: tokenizer, expander, signal prototipler
│   └── execution.h          // Executor modülü: builtin, pipe, redirect prototipler
├── src/
│   ├── main.c               // Ana program: shell loop, readline, history yönetimi
│   ├── parsing/             
│   │   ├── tokenizer.c      // Input'u token'lara ayırma, quote handling
│   │   ├── parser.c         // Token'ları command structure'a dönüştürme
│   │   ├── expander.c       // $VAR, $? expansion, variable substitution
│   │   └── signals.c        // Ctrl-C, Ctrl-D, Ctrl-\ signal handling
│   ├── execution/           
│   │   ├── executor.c       // Fork/execve, PATH search, process management
│   │   ├── pipes.c          // Pipe creation, inter-process communication
│   │   ├── redirections.c   // <, >, <<, >> file redirection handling
│   │   └── builtins/
│   │       ├── builtin_echo.c    // echo komutu, -n option handling
│   │       ├── builtin_cd.c      // cd komutu, PWD/OLDPWD güncelleme
│   │       ├── builtin_pwd.c     // pwd komutu, getcwd ile current dir
│   │       ├── builtin_export.c  // export komutu, env var creation/modification
│   │       ├── builtin_unset.c   // unset komutu, env var removal
│   │       ├── builtin_env.c     // env komutu, environment variable listing
│   │       └── builtin_exit.c    // exit komutu, shell termination, cleanup
│   └── utils/               
│       ├── utils_string.c   // String manipulation: split, join, compare
│       ├── utils_memory.c   // Memory management: safe malloc/free, cleanup
│       ├── utils_env.c      // Environment utilities: search, copy, modify
│       └── utils_error.c    // Error handling: messages, codes, debug
└── Makefile                 // Compilation rules, linking, clean targets
```

---

## 🧪 Test Strategy

### Unit Testing
Her kişi kendi modülü için:
```bash
# Parser tests
echo "ls | grep test" | ./test_parser
echo "'hello world'" | ./test_parser

# Executor tests
echo "echo hello" | ./test_executor
echo "ls -la | wc -l" | ./test_executor
```

### Integration Testing
```bash
# Real minishell testing
./minishell
minishell$ echo "Hello World"
minishell$ ls -la | grep .c
minishell$ export TEST=value && echo $TEST
```
