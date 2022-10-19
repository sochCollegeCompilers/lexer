```
definitions
%%
rules
%%
user code
```
### Format of definitions section
```name definition```

For example:
```
DIGIT    [0-9]
ID       [a-z][a-z0-9]*
```

#### Conventions
```{DIGIT}+"."{DIGIT}*``` is equivalent to ```([0-9])+"."([0-9])*```

An unindented comment (i.e., a line beginning with ‘/\*’) is copied verbatim to the output up to the next ‘\*/’

Any indented text or text enclosed in ‘%{’ and ‘%}’ is also copied verbatim to the output (with the %{ and %} symbols removed). The %{ and %} symbols must appear unindented on lines by themselves

A %top block is similar to a ‘%{’ ... ‘%}’ block, except that the code in a %top block is relocated to the top of the generated file, before any flex definitions.

The %top block is useful when you want certain preprocessor macros to be defined or certain files to be included before the generated code


    %top{
        /* This code goes at the "top" of the generated file. */
        #include <stdint.h>
        #include <inttypes.h>
    }

### Format of the Rules Section

    pattern action

The pattern must be unindented and the action must begin on the same line.


### Writing Comments:

    %{
     /* code block */
    %}
     /* Definitions Section */
     %x STATE_X
    %%
        /* Rules Section */
    ruleA   /* after regex */ { /* code block */ } /* after code block */
            /* Rules Section (indented) */
    <STATE_X>{
    ruleC   ECHO;
    ruleD   ECHO;
    %{
    /* code block */
    %}
    }
    %%
    /* User Code Section */

### Patterns
|Pattern|Purpose|
|----|-----|
|‘x’ | match the character ’x’|
|‘.’ |any character (byte) except newline|
|‘[xyz]’| a character class; in this case, the pattern matches either an ’x’, a ’y’, or a ’z’ ‘[abj-oZ]’|
|[abj-oZ] |"character class" with a range in it; matches an ’a’, a ’b’, any letter from ’j’ through ’o’, or a ’Z’|
|‘[^A-Z]’ |a "negated character class", i.e., any character but those in the class. In this case, any character EXCEPT an uppercase letter.|
|‘[^A-Z\n]’| any character EXCEPT an uppercase letter or a newline|
|‘[a-z]{-}[aeiou]’|the lowercase consonants|
|‘r*’| zero or more r’s, where r is any regular expression|
|‘r+’| one or more r’s|
|‘r?’ |zero or one r’s (that is, “an optional r”)|
|‘r{2,5}’| anywhere from two to five r’s|
|‘r{2,}’ |two or more r’s|
|‘r{4}’ |exactly 4 r’s|
|‘{name}’| the expansion of the ‘name’ definition|
|‘"[xyz]\"foo"’| the literal string: ‘[xyz]"foo’|
|‘\X’| if X is ‘a’, ‘b’, ‘f’, ‘n’, ‘r’, ‘t’, or ‘v’, then the ANSI-C interpretation of |‘\x’. Otherwise, a literal ‘X’ (used to escape operators such as ‘*’)|
|‘\0’ |a NUL character (ASCII code 0)|
|‘\123’| the character with octal value 123|
|‘\x2a’ |the character with hexadecimal value 2a|
|‘(r)'| match an ‘r’; parentheses are used to override precedence (see below)|
|‘(?r-s:pattern)’|apply option ‘r’ and omit option ‘s’ while interpreting pattern. Options may be zero or more of the characters ‘i’, ‘s’, or ‘x’.|
||‘i’ means case-insensitive. ‘-i’ means case-sensitive.|
||‘s’ alters the meaning of the ‘.’ syntax to match any single byte whatsoever. ‘-s’ alters the meaning of ‘.’ to match any byte except ‘\n’.|
||‘x’ ignores comments and whitespace in patterns. Whitespace is ignored unless it is backslash-escaped, contained within ‘""’s, or appears inside a character class.|
||The following are all valid:
||(?:foo)         same as  (foo)|
||(?i:ab7)        same as  ([aA][bB]7)|
||(?-i:ab)        same as  (ab)|
||(?s:.)          same as  [\x00-\xFF]|
||(?-s:.)         same as  [^\n]|
||(?ix-s: a . b)  same as  ([Aa][^\n][bB])|
||(?x:a  b) same as same as  ("ab")|
||(?x:a\ b) same as  ("ab")|
||(?x:a" "b) same as  ("a b")|
||(?x:a[ ]b) same as  ("a b")|
||(?x:a/* comment */bc)          same as  (abc)|
|‘rs’ |the regular expression ‘r’ followed by the regular expression ‘s’; called concate- nation|
|‘r|s’ |either an ‘r’ or an ‘s’|
|‘r/s’| an ‘r’ but only if it is followed by an ‘s’. The text matched by ‘s’ is included when determining whether this rule is the longest match, but is then returned to the input before the action is executed. So the action only sees the text matched by ‘r’. This type of pattern is called trailing context. (There are some combinations of ‘r/s’ that flex cannot match correctly.)|
|‘^r’ |an ‘r’, but only at the beginning of a line (i.e., when just starting to scan, or right after a newline has been scanned).|
|‘r$’| an ‘r’, but only at the end of a line (i.e., just before a newline). Equivalent to ‘r/\n’.|
|‘\<s\>r’| an ‘r’, but only in start condition(s)|
|‘<s1,s2,s3>r’| same, but in any of start conditions s1, s2, or s3.|
|‘<*>r’| an ‘r’ in any start condition, even an exclusive one.|
|‘<\<EOF>>’| an end-of-file.|
|‘<s1,s2><\<EOF>>’|an end-of-file when in start condition s1 or s2|

We have character class expressions also.

| Expressions |Description|
|--|--|
|[:alnum:]| same as [a-zA-Z0-9]|
| [:alpha:] | same as [a-zA-Z]|
|[:blank:]| blank or a tab |
|[:cntrl:] | control characters|
|[:digit:]| same as [0-9]|
|[:graph:]|
|[:lower:]| same as [a-z]|
|[:print:]| printable chaacters |
|[:punct:]| punctuation characters|
|[:space:]|
|[:upper:]| same as [A-Z]|
|[:xdigit:]| hex digits |
|[:^alnum:]| negated character class|

Especial characters

|Characters| Usage| Description|
|--|--|--|
|{+}|[a-z]{+}[0-9]| Union of a-Z and 0-9|
|{-}|[a-x]{-}{c-z}| Equivalent to [a-b]|
