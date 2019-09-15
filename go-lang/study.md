### nil
> len 0, cap 0 배열을 의미

### make
```go
make([]int, 5, 5) # int 형 배열, len 5, cap 5
```

### len vs cap
> len - 배열 크기 의미, cap - 배열 최대 용량 (slice 개념 생각)

### 배열 loop

```go

...
var pow = [] int {1,2,4,8,16,32,64,128,256,512}
...
for i, v := range, pow {
  fmt.Printf("2**%d = %d\n",i,v)  
}
```


### How to use 2-dimension array

```go
package main

import "code.google.com/p/go-tour/pic"

func Pic(dx, dy int) (a [][]uint8) {
    a = make([][]uint8, dy)

    for i := range a {
        a[i] = make([]uint8, dx)
    }

    for i := range a {
        for j := range a[i] {
          a[i][j] = (uint8) (i + j)
        }
    }

    return
}

func main() {
    pic.Show(Pic)
}
```

### How to use map

```go
package main

import "fmt"

type Vertex struct {
    Lat, Long float64
}

var m map[string]Vertex

func main() {
    m = make(map[string]Vertex)
    m["Bell Labs"] = Vertex{
        40.68433, -74.39967,
    }
    m["kangnam"] = Vertex{
        1230.23123, -98.23123,
    }
    fmt.Println(m["Bell Labs"])
    fmt.Println(m["kangnam"])
}

```

### How to use map iteration
```go
package main

import (
    "code.google.com/p/go-tour/wc"
    "strings"
)

func WordCount(s string) map[string]int {
    
    m := make(map[string]int)
    
    keys := strings.Fields(s)
    
    for val := range keys {
        m[keys[val]]++
    }
    
    return m
}

func main() {
    wc.Test(WordCount)
}

```
