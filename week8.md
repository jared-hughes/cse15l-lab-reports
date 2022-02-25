Go back [home](index.md)

# Week 8

My repository:
[jared-hughes/markdown-parse](https://github.com/jared-hughes/markdown-parse)

The repository I reviewed:
[c1peng/markdown-parse](https://github.com/c1peng/markdown-parse)

## Snippet 1

Expected: `` [`google.com, google.com, ucsd.edu] ``

### My implementation

Test:

```java
@Test
public void testSnippet1() {
  assertResult("test-snippet-1.md",
      List.of("`google.com", "google.com", "ucsd.edu"));
}
```

It did not pass. Corresponding output: `` [url.com, `google.com, google.com, ucsd.edu] ``.

JUnit output:

```
1) testSnippet1(MarkdownParseTest)
java.lang.AssertionError: test-snippet-1.md expected:<[`google.com, google.com, ucsd.edu]> but was:<[url.com, `google.com, google.com, ucsd.edu]>
        at org.junit.Assert.fail(Assert.java:89)
        at org.junit.Assert.failNotEquals(Assert.java:835)
        at org.junit.Assert.assertEquals(Assert.java:120)
        at MarkdownParseTest.assertResult(MarkdownParseTest.java:13)
        at MarkdownParseTest.testSnippet1(MarkdownParseTest.java:114)
```

### Reviewed implementation

Test:

```java
@Test
public void testSnippet1() throws IOException {
    String contents = Files.readString(Path.of("test-snippet-1.md"));
    assertEquals(List.of("`google.com", "google.com", "ucsd.edu"),
            MarkdownParse.getLinks(contents));
}
```

It did not pass. Corresponding output: `` [url.com, `google.com, google.com, ucsd.edu] ``

JUnit output:

```
1) testSnippet1(MarkdownParseTest)
java.lang.AssertionError: expected:<[`google.com, google.com, ucsd.edu]> but was:<[url.com, `google.com, google.com, ucsd.edu]>
        at org.junit.Assert.fail(Assert.java:89)
        at org.junit.Assert.failNotEquals(Assert.java:835)
        at org.junit.Assert.assertEquals(Assert.java:120)
        at org.junit.Assert.assertEquals(Assert.java:146)
        at MarkdownParseTest.testSnippet1(MarkdownParseTest.java:59)
```

## Snippet 2

Expected: `[a.com, a.com(()), example.com]`

### My implementation

Test:

```java
@Test
public void testSnippet2() {
  assertResult("test-snippet-2.md",
      List.of("a.com", "a.com(())", "example.com"));
}
```

It did not pass. Corresponding output: `[a.com, example.com]`.

JUnit output:

```
2) testSnippet2(MarkdownParseTest)
java.lang.AssertionError: test-snippet-2.md expected:<[a.com, a.com(()), example.com]> but was:<[a.com, example.com]>
        at org.junit.Assert.fail(Assert.java:89)
        at org.junit.Assert.failNotEquals(Assert.java:835)
        at org.junit.Assert.assertEquals(Assert.java:120)
        at MarkdownParseTest.assertResult(MarkdownParseTest.java:13)
        at MarkdownParseTest.testSnippet2(MarkdownParseTest.java:120)
```

### Reviewed implementation

Test:

```java
@Test
public void testSnippet2() throws IOException {
    String contents = Files.readString(Path.of("test-snippet-2.md"));
    assertEquals(List.of("a.com", "a.com(())", "example.com"),
            MarkdownParse.getLinks(contents));
}
```

It did not pass. Corresponding output: `[a.com, a.com((), example.com]`

JUnit output:

```
2) testSnippet2(MarkdownParseTest)
java.lang.AssertionError: expected:<[a.com, a.com(()), example.com]> but was:<[a.com, a.com((), example.com]>
        at org.junit.Assert.fail(Assert.java:89)
        at org.junit.Assert.failNotEquals(Assert.java:835)
        at org.junit.Assert.assertEquals(Assert.java:120)
        at org.junit.Assert.assertEquals(Assert.java:146)
        at MarkdownParseTest.testSnippet2(MarkdownParseTest.java:66)
```

## Snippet 3

Expected: `[https://ucsd-cse15l-w22.github.io/, example.com]`

### My implementation

Test:

```java
@Test
public void testSnippet3() {
  assertResult("test-snippet-3.md",
      List.of("https://ucsd-cse15l-w22.github.io/", "example.com"));
}
```

It did not pass. Corresponding output: `[ ]`.

JUnit output:

```
3) testSnippet3(MarkdownParseTest)
java.lang.AssertionError: test-snippet-3.md expected:<[https://ucsd-cse15l-w22.github.io/, example.com]> but was:<[]>
        at org.junit.Assert.fail(Assert.java:89)
        at org.junit.Assert.failNotEquals(Assert.java:835)
        at org.junit.Assert.assertEquals(Assert.java:120)
        at MarkdownParseTest.assertResult(MarkdownParseTest.java:13)
        at MarkdownParseTest.testSnippet3(MarkdownParseTest.java:126)
```

### Reviewed implementation

Test:

```java
@Test
public void testSnippet3() throws IOException {
    String contents = Files.readString(Path.of("test-snippet-3.md"));
    assertEquals(
            List.of("https://ucsd-cse15l-w22.github.io/", "example.com"),
            MarkdownParse.getLinks(contents));
}
```

It did not pass. Corresponding output:

```
[
    https://www.twitter.com
,
    https://ucsd-cse15l-w22.github.io/
, github.com

And there's still some more text after that.

[this link doesn't have a closing parenthesis for a while](https://cse.ucsd.edu/



]
```

JUnit output:

```
3) testSnippet3(MarkdownParseTest)
java.lang.AssertionError: expected:<[https://ucsd-cse15l-w22.github.io/, example.com]> but was:<[
    https://www.twitter.com
,
    https://ucsd-cse15l-w22.github.io/
, github.com

And there's still some more text after that.

[this link doesn't have a closing parenthesis for a while](https://cse.ucsd.edu/



]>
        at org.junit.Assert.fail(Assert.java:89)
        at org.junit.Assert.failNotEquals(Assert.java:835)
        at org.junit.Assert.assertEquals(Assert.java:120)
        at org.junit.Assert.assertEquals(Assert.java:146)
        at MarkdownParseTest.testSnippet3(MarkdownParseTest.java:73)
```

# Changes

To fix snippet 1, there is a short change that will make my program work for inline code with backticks. All searches for syntax characters (`[]()`) use the `indexOfUnescaped` method, so that one method could be modified to skip past backticks.

To fix snippet 2, there is a short change that will make my program work for nested brackets. This could be done in a similar way as fixing backticks, using modification of the `indexOfUnescaped` method. However, this seems like it would require more work than fixing backticks.

Fixing snippet 3 by properly dealing with newlines would be more complicated. While newlines around parentheses can be dealt with using `replaceAll`, the bigger difficulty is newlines inside the link text. Dealing with that might take more than 10 lines.
