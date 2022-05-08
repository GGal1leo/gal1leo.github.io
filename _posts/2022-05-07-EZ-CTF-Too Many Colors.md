---
title: Too Many Colors Writeup
published: true
categories: writeups
tags: EZ-CTF
challenge: https://ez.ctf.cafe/
---
**Challenge link** => [{{ page.challenge }}]({{ page.challenge }})

Before I go through the solving process, I'd liike to thank the organizers for making this fun event possible

**CTF Cafe's [twitter](https://twitter.com/CTFCafe)**

---
Alright so let's get started by looking at this nonsensse of an image.

![](/assets/images/Too%20Many%20Colors.jpeg)

At first I went through a rabbit hole and tried getting each pixel's hex value and converting it to ascii. 
**SPOILER ALERT** - It didn't work.

I stumbled upon *[this article: https://omniglot.com/conscripts/hexahue.php](https://omniglot.com/conscripts/hexahue.php)*, and tried decoding it by hand. Soon I noticed the original image was upside down, so I rotated it using *gimp* and saved it as a new image that looked somewhat like this
![](/assets/images/imgonline-com-ua-Rotate360-bBQQHO83AK5jd.jpg)

I also found *[this tool: https://github.com/kusuwada/hexahue](https://github.com/kusuwada/hexahue)*, but couldn't get it to work as the pixels in the image would be of a slightly different color, and python was freaking out.

After succesfully decoding the image to a string, we get "KC4LBT1TN14PU0YN4C7VB3RUS". Looks like it's inversed. Let's fix that!

```bash
echo "KC4LBT1TN14PU0YN4C7VB3RUS" | rev
>> "SUR3BV7C4NY0UP41NT1TBL4CK"
```

Putting that into the required format, we get the flag: "EZ-CTF{SUR3_BV7_C4N_Y0U_P41NT_1T_BL4CK}"

