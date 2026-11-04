# Basic Android RE 1




{{< admonition tldr "TL;DR" >}}

**Basic Android RE 1** is a reverse challenge from **CTFlearn**. A simple APK, reverse engineer the logic, recreate the flag, and submit!   [BasicAndroidRE1.apk](https://ctflearn.com/challenge/download/962)

{{< /admonition >}}

## Challenge Overview

A simple APK, reverse engineer the logic, recreate the flag, and submit!  
[BasicAndroidRE1.apk](https://ctflearn.com/challenge/download/962)

---

## Technical Analysis

This challenge is **basic Android Reverse Engineering**. The goal is to analyze the input validation logic within an Android application to reconstruct the resulting flags.

The APK is opened using **jadx-gui** to decompile the Java source code.
In the package structure, the following path is found:

```
com.example.secondapp
└── MainActivity.java
```

---

## Technical Analysis Source Code

The contents of the `MainActivity.java` file are as follows:

```java
package com.example.secondapp;

import android.os.Bundle;
import android.view.View;
import android.widget.EditText;
import android.widget.TextView;
import androidx.appcompat.app.AppCompatActivity;
import org.apache.commons.codec.digest.DigestUtils;

/* loaded from: classes.dex */
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle bundle) {
        super.onCreate(bundle);
        setContentView(R.layout.activity_main);
    }

    public void submitPassword(View view) {
        EditText editText = (EditText) findViewById(R.id.editText2);
        if (DigestUtils.md5Hex(editText.getText().toString())
                .equalsIgnoreCase("b74dec4f39d35b6a2e6c48e637c8aedb")) {

            ((TextView) findViewById(R.id.textView))
                .setText("Success! CTFlearn{" 
                + editText.getText().toString() 
                + "_is_not_secure!}");
        }
    }
}
```

---

## Understanding Application Logic

From the code above it can be concluded:

1. User input is taken from `EditText` 2. The input is hashed using **MD5**
3. The input result hash is compared with the hardcoded value:

```
b74dec4f39d35b6a2e6c48e637c8aedb
```

4. If it matches, the application will display a flag in the format:

```
CTFlearn{<password>_is_not_secure!}
```

This means, we only need to find the **plaintext MD5 hash**.

---

## Crack MD5 Hashes

MD5 hashes are bruteforced using **hashcat** with a common wordlist.

```bash
hashcat -m 0 hash.txt /usr/share/seclists/Passwords/corporate_passwords.txt
```

## Results

```
b74dec4f39d35b6a2e6c48e637c8aedb:Spring2019
```

Password successfully found:

```
Spring2019
```

---

## Flag Reconstruction

By following the format in the application code:

```
CTFlearn{Spring2019_is_not_secure!}
```

---

##Flags

```text
CTFlearn{Spring2019_is_not_secure!}
```


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://ctf.spawn2pwn.my.id/posts/basic-android-re-1/  

