---
title: Doppelganger
published: true
categories: writeups
tags: ZeroDays
---

**This is part of the ZeroDays challenge 2024 in ireland**

---

The file we're given appears to be a normal text file

```sh
❯ cat chall.txt 
Beware the ghostly figure of the doppelganger; if you meet yours? Something might just disappear...:󠁚󠁥󠁽
```

Upon deeper inspection with `xxd` we find some strange characters appended to the text.

```sh
❯ xxd chall.txt
00000000: 4265 7761 7265 2074 6865 2067 686f 7374  Beware the ghost
00000010: 6c79 2066 6967 7572 6520 6f66 2074 6865  ly figure of the
00000020: 2064 6f70 7065 6c67 616e 6765 723b 2069   doppelganger; i
00000030: 6620 796f 7520 6d65 6574 2079 6f75 7273  f you meet yours
00000040: 3f20 536f 6d65 7468 696e 6720 6d69 6768  ? Something migh
00000050: 7420 6a75 7374 2064 6973 6170 7065 6172  t just disappear
00000060: 2e2e 2e3a f3a0 819a f3a0 81a5 f3a0 81b2  ...:............
00000070: f3a0 81af f3a0 8184 f3a0 81a1 f3a0 81b9  ................
00000080: f3a0 81b3 f3a0 81bb f3a0 81a2 f3a0 80b3  ................
00000090: f3a0 81b7 f3a0 80b4 f3a0 81b2 f3a0 81a5  ................
000000a0: f3a0 819f f3a0 81a9 f3a0 81ae f3a0 81b6  ................
000000b0: f3a0 80b1 f3a0 81b3 f3a0 80b1 f3a0 81a2  ................
000000c0: f3a0 81ac f3a0 80b3 f3a0 819f f3a0 81a3  ................
000000d0: f3a0 81a8 f3a0 80b4 f3a0 81b2 f3a0 81b3  ................
000000e0: f3a0 81bd                                ....
```

The values such as `f3a0 819a` and `f3a0 81a5` are not ASCII characters. They look like UTF-8. Let's convert to check the value pf each character.

```python
>>> with open('chall.txt', 'rb') as f:
...     s = f.read().decode('utf-8')
...
>>> [hex(ord(x)) for x in s if ord(x) >= 0x7f]
['0xe005a', '0xe0065', '0xe0072', '0xe006f', '0xe0044', '0xe0061', '0xe0079', '0xe0073', '0xe007b', '0xe0062', '0xe0033', '0xe0077', '0xe0034', '0xe0072', '0xe0065', '0xe005f', '0xe0069', '0xe006e', '0xe0076', '0xe0031', '0xe0073', '0xe0031', '0xe0062', '0xe006c', '0xe0033', '0xe005f', '0xe0063', '0xe0068', '0xe0034', '0xe0072', '0xe0073', '0xe007d']
```

Cool. Now let's extract them:

```python
>>> ''.join(chr(ord(x) & 0xff) for x in s if ord(x) >= 0x7f)
'ZeroDays{b3w4re_inv1s1bl3_ch4rs}'
>>> 
```

And we get our flag. Nice :) 
