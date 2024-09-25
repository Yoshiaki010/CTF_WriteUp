# Pwnable

- [simpleoverflow](#simpleoverflow-beginner)
- [simpleoverwrite](#simpleoverwrite-easy)
- [BACK](../README.md)

## simpleoverflow (beginner)

バッファオーバーフローで`is_admin`を0以外に上書きし、Flagを表示させた。
if文の判定が False の時に Flag を表示するため、`is_admin`を True （0以外の値）にできるとよい。`buf`と`is_admin`の格納場所が隣り合っているとき、バッファオーバーフローを引き起こすことで、is_adminの格納場所に True（0以外の値）を上書きできる。問題文からbufは10バイト確保し、入力は最大16バイトだとわかります。よって、10バイト以上の入力を行い、Flagを表示する。

問題文：

``` c
    #include <stdio.h>
    #include <stdlib.h>
    #include <unistd.h>

    int main() {
    char buf[10] = {0};
    int is_admin = 0;
    printf("name:");
    read(0, buf, 0x10);
    printf("Hello, %s\n", buf);
    if (!is_admin) {
        puts("You are not admin. bye");
    } else {
        system("/bin/cat ./flag.txt");
    }
    return 0;
    }

    __attribute__((constructor)) void init() {
    setvbuf(stdin, NULL, _IONBF, 0);
    setvbuf(stdout, NULL, _IONBF, 0);
    alarm(120);
    }
```
<!--

-->
## simpleoverwrite (easy)

この問題を解いています。...
方針：win()関数を呼び出す。バッファオーバーフローとリターンアドレス？が関係している。

- `win()関数`はflagを表示する。
- 入力時のbufの最大メモリは10バイト。
- 入力は最大32バイト。←ここにオーバーフローのリスクあり。
- 入力されたものの18バイト目以降を16進数で出力している。そのほかに`void *`でポイント型にし、64bit整数にしている。ポイント型はどの型にも変換できるみたい。

問題文：

```c
#include <stdint.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

void win() {
  char buf[100];
  FILE *f = fopen("./flag.txt", "r");
  fgets(buf, 100, f);
  puts(buf);
}

int main() {
  char buf[10] = {0};
  printf("input:");
  read(0, buf, 0x20);
  printf("Hello, %s\n", buf);
  printf("return to: 0x%lx\n", *(uint64_t *)(((void *)buf) + 18));
  return 0;
}

__attribute__((constructor)) void init() {
  setvbuf(stdin, NULL, _IONBF, 0);
  setvbuf(stdout, NULL, _IONBF, 0);
  alarm(120);
}
```
