# The Adventures of Boris Ivanov – Part 1




{{< admonition tldr "TL;DR" >}}

**The Adventures of Boris Ivanov – Part 1** is a forensics challenge from **CTFlearn**. The KGB agent Boris Ivanov got information about an attempt to sell classified data. He quickly reacted and intercepted the correspondence. Help Boris understand what exactly they were trying to sell.   Here is the interception data: [Boris_Ivanov_1.jpg](https://mega.nz/#!HfAHmKQb!zg6EPqfwes1bBDCjx7-ZFR_0O0-GtGg2Mrn56l5LCkE)

{{< /admonition >}}

##Challenge

The KGB agent Boris Ivanov got information about an attempt to sell classified data. He quickly reacted and intercepted the correspondence. Help Boris understand what exactly they were trying to sell.  
Here is the interception data: [Boris_Ivanov_1.jpg](https://mega.nz/#!HfAHmKQb!zg6EPqfwes1bBDCjx7-ZFR_0O0-GtGg2Mrn56l5LCkE)

---

## Technical Analysis

We are provided with a JPEG image file named `Boris_Ivanov_1.jpg` .
When opened normally, the image displays a document marked **“TOP SECRET”** containing a conversation between *John Brown* and *Michael Smith* regarding confidential information transactions.

However, there is an oddity at the bottom of the image in the form of a **horizontal area with a random RGB noise pattern**.

{{< image src="./images/Boris_Ivanov_1.jpg" >}}

This kind of noise pattern is often an indication of **hidden data using steganography techniques**, especially the **stereogram** type, where the message is hidden through pixel shifts, not plain text.

---

## Technical Analysis Steganography

Based on these indications, the image is further analyzed using **StegSolve** by checking various pixel offsets.

After performing the offset adjustment, it was found that at **offset 898** the noise was transformed into readable text.

{{< image src="./images/solved.bmp" >}}

This confirms that the image indeed contains **hidden messages** that cannot be seen with the naked eye without proper analysis techniques.

---

##Flags

```text
flag{d0nt_m3s5_w1th_th3_KGB}
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/the-adventures-of-boris-ivanov-part-1/  

