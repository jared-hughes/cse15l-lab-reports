Go back [home](index.md)

# Week 10

I found differences between the two implementations by running the following script with each implementation:

```bash
for file in test-files/*.md;
do
  echo $file
  java MarkdownParse $file
  echo ""
done
```

After piping the results to a file using `bash script.sh > results.txt`, a git diff sufficed to find differences between the implementations: `git diff --no-index md4/results.txt markdown-parse/results.txt`:

```diff
@@ -1301,28 +1301,28 @@ test-files/491.md
 [<b]

 test-files/492.md
-[<foo\>]
+[<foo>]

 test-files/493.md
 [<b, <b, <b>c]

 test-files/494.md
-[\(foo\)]
+[(foo)]

 test-files/495.md
-[foo(and(bar))]
+[]

...
```

## Difference 1

For test file 492, neither implementation is correct. The provided implementation outputs `[<foo\>]`, and my implementation outputs `[<foo>]`. However, the angle brackets should be removed, and the output should be `[foo]`.

The offending code in my implementation is

```java
String substr = markdown.substring(openParen + 1, closeParen);
substr = substr.replaceAll("\\\\(.)", "$1");
if (!substr.contains(" ")) {
    // URLs containing a space should be ignored
    toReturn.add(substr);
}
```

This reveals one bug causing two symptoms. The bug is angle brackets (`<>`) are not treated specially. On symptom is in test file 492, where the angle brackets are included even though they should not be. Another symptom is in test file 488 (`[link](</my uri>)`), where spaces should not invalidate the URL if the URL is between angle brackets.

Code that would fix the bug would handle the angle brackets specially: if the link destination starts and ends with unescaped angle brackets, then exclude the angle brackets from the URL string, and allow spaces if present.

## Difference 2

For test file 577, the provided implementation is not correct. The provided i mplementation outputs `[train.jpg]`, while the correct output is `[]`. No links should be detected because `![foo](train.jpg)` is an image.

The offending code in the provided implementation is

```java
toReturn.add(potentialLink);
```

This always adds the link if reaching that line. The bug is that there's no check for `!` before the link markdown. A fix would ignore images (start with a `!` before the `[`), something like

```java
// Ignore images
if (!(nextOpenBracket > 0 && markdown.charAt(nextOpenBracket - 1) == '!')) {
  toReturn.add(potentialLink);
}
```
