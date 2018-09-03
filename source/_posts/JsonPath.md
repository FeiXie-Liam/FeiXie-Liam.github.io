## 导入依赖

在build.gradle中添加依赖`compile('com.jayway.jsonpath:json-path:2.1.0')`。

## 语法

讲解示例：

```json
{
    "store": {
        "book": [
            {
                "category": "reference",
                "author": "Nigel Rees",
                "title": "Sayings of the Century",
                "price": 8.95
            },
            {
                "category": "fiction",
                "author": "Evelyn Waugh",
                "title": "Sword of Honour",
                "price": 12.99
            },
            {
                "category": "fiction",
                "author": "Herman Melville",
                "title": "Moby Dick",
                "isbn": "0-553-21311-3",
                "price": 8.99
            },
            {
                "category": "fiction",
                "author": "J. R. R. Tolkien",
                "title": "The Lord of the Rings",
                "isbn": "0-395-19395-8",
                "price": 22.99
            }
        ],
        "bicycle": {
            "color": "red",
            "price": 19.95
        }
    },
    "expensive": 10
}
```

### 表示方法

jsonpath有两种方式表示路径：

dot notation:

```java
$.store.book[2]
```

backet  notation

```java
$['store']['book'][2]
```

两种方式指向同样一个json子串。

### 运算符

在jsonpath中有多个有效的运算符（Operator）

| Operator                  | Description                                                  |
| ------------------------- | ------------------------------------------------------------ |
| `$`                       | The root element to query. This starts all path expressions. |
| `@`                       | The current node being processed by a filter predicate.      |
| `*`                       | Wildcard. Available anywhere a name or numeric are required. |
| `..`                      | Deep scan. Available anywhere a name is required.            |
| `.<name>`                 | Dot-notated child                                            |
| `['<name>' (, '<name>')]` | Bracket-notated child or children                            |
| `[<number> (, <number>)]` | Array index or indexes                                       |
| `[start:end]`             | Array slice operator                                         |
| `[?(<expression>)]`       | Filter expression. Expression must evaluate to a boolean value. |

### 方法和过滤器

jsonpath支持多个方法：

- min()
-  max()
-  avg()
-  stddev()
-  length()

使用方法时，只能讲方法添加到json路径的末尾。

除此之外，jsonpath还支持过滤器，过滤器必须返回一个布尔类型，比如[?(@.color == 'blue')]，完整过滤器列表如下所示：

| Operator | Description                                                  |
| -------- | ------------------------------------------------------------ |
| ==       | left is equal to right (note that 1 is not equal to '1')     |
| !=       | left is not equal to right                                   |
| <        | left is less than right                                      |
| <=       | left is less or equal to right                               |
| >        | left is greater than right                                   |
| >=       | left is greater than or equal to right                       |
| =~       | left matches regular expression [?(@.name =~ /foo.*?/i)]     |
| in       | left exists in right [?(@.size in ['S', 'M'])]               |
| nin      | left does not exists in right                                |
| subsetof | left is a subset of right [?(@.sizes subsetof ['S', 'M', 'L'])] |
| size     | size of left (array or string) should match right            |
| empty    | left (array or string) should be empty                       |

混合使用运算符，方法和过滤器的具体示例如下:

| JsonPath                                                     | Result                                                       |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [$.store.book[*\].author](http://jsonpath.herokuapp.com/?path=$.store.book[*].author) | The authors of all books                                     |
| [$..author](http://jsonpath.herokuapp.com/?path=$..author)   | All authors                                                  |
| [$.store.*](http://jsonpath.herokuapp.com/?path=$.store.*)   | All things, both books and bicycles                          |
| [$.store..price](http://jsonpath.herokuapp.com/?path=$.store..price) | The price of everything                                      |
| [$..book[2\]](http://jsonpath.herokuapp.com/?path=$..book[2]) | The third book                                               |
| [$..book[-2\]](http://jsonpath.herokuapp.com/?path=$..book[2]) | The second to last book                                      |
| [$..book[0,1\]](http://jsonpath.herokuapp.com/?path=$..book[0,1]) | The first two books                                          |
| [$..book[:2\]](http://jsonpath.herokuapp.com/?path=$..book[:2]) | All books from index 0 (inclusive) until index 2 (exclusive) |
| [$..book[1:2\]](http://jsonpath.herokuapp.com/?path=$..book[1:2]) | All books from index 1 (inclusive) until index 2 (exclusive) |
| [$..book[-2:\]](http://jsonpath.herokuapp.com/?path=$..book[-2:]) | Last two books                                               |
| [$..book[2:\]](http://jsonpath.herokuapp.com/?path=$..book[2:]) | Book number two from tail                                    |
| [$..book[?(@.isbn)\]](http://jsonpath.herokuapp.com/?path=$..book[?(@.isbn)]) | All books with an ISBN number                                |
| [$.store.book[?(@.price < 10)\]](http://jsonpath.herokuapp.com/?path=$.store.book[?(@.price%20%3C%2010)]) | All books in store cheaper than 10                           |
| [$..book[?(@.price <= $['expensive'\])]](http://jsonpath.herokuapp.com/?path=$..book[?(@.price%20%3C=%20$[%27expensive%27])]) | All books in store that are not "expensive"                  |
| [$..book[?(@.author =~ /.*REES/i)\]](http://jsonpath.herokuapp.com/?path=$..book[?(@.author%20=~%20/.*REES/i)]) | All books matching regex (ignore case)                       |
| [$..*](http://jsonpath.herokuapp.com/?path=$..*)             | Give me every thing                                          |
| [$..book.length()](http://jsonpath.herokuapp.com/?path=$..book.length()) | The number of books                                          |

## 操作方式

读取一个json字符串的最简单方式如下:

```java
String json = "...";

List<String> authors = JsonPath.read(json, "$.store.book[*].author");
```

如果想要提取json字符串中的部分数据：

```java
String json = "...";

DocumentContext ctx = JsonPath.parse(json);

List<String> authorsOfBooksWithISBN = ctx.read("$.store.book[?(@.isbn)].author");


List<Map<String, Object>> expensiveBooks = JsonPath
                            .parse(json)
                            .read("$.store.book[?(@.price > 10)]", List.class);
```