# Pwnable

- [simpleoverflow](#simpleoverflow-beginner)
- [simpleoverwrite](#simpleoverwrite-beginner)
- [BACK](../README.md)

## simpleoverflow (beginner)

Flagを表示するには、if文の判定が False でなければならない。つまり、`is_admin`が True （0以外の値）であればif文の判定が False になる。
`buf`と`is_admin`の保存場所が隣り合っていれば、Overflowを引き起こすことで、is_adminの値を0以外の値に上書きできる。問題文からbufは10文字まで保存していることがわかるので、入力時に10文字以上の入力をしてFlagを表示させる。

問題文

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

## simpleoverwrite (easy)

a

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
