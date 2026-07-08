# Neighbour


{{< image src="./images/room_image.webp" >}}

> **Check out our new cloud service, Authentication Anywhere. Can you find other users' secrets?**

{{< admonition tldr "TL;DR" >}}
Neighbour is an easy TryHackMe room that demonstrates an **Insecure Direct Object Reference (IDOR)** vulnerability. The application fails to enforce authorization checks on the profile endpoint, allowing an authenticated user to access another user's profile by modifying the `user` query parameter.
{{< /admonition >}}

## Initial Access

We are provided with a website containing a login form.

{{< image src="./images/first-visit.webp" >}}

The page instructs us to inspect its source code using `Ctrl+U`. Reviewing the HTML source reveals a comment containing credentials for a guest account.

{{< image src="./images/source-code.webp" >}}

Using these credentials, we can authenticate as the `guest` user.

{{< image src="./images/login-guest.webp" >}}

## Identifying the IDOR Vulnerability

After logging in, the browser is redirected to the following URL:

```text
http://MACHINE_IP/profile.php?user=guest
```

The application uses the `user` query parameter to determine which profile should be displayed. However, the server does not verify whether the authenticated user is authorized to access the requested profile.

Because the current account is `guest`, the parameter is set to:

```text
user=guest
```

We can test for an IDOR vulnerability by changing the parameter value from `guest` to `admin`:

{{< style "text-align:center" >}}
{{< image src="./images/giphy.gif " width="60%" align="center" >}}
{{< /style >}}

```text
http://MACHINE_IP/profile.php?user=admin
```

The application then loads the administrator's profile and exposes the flag.

{{< image src="./images/login-admin.webp" >}}

## Conclusion

This room demonstrates how broken access control can lead to unauthorized access to sensitive user data. Although the application requires authentication, it relies entirely on a user-controlled URL parameter without validating ownership or permissions.

To prevent this issue, the server should derive the current user's identity from the authenticated session rather than trusting a client-controlled parameter. If access to other profiles is required, the application must perform an authorization check before returning the requested data.


---

> Author: [spawn2pwn](https://github.com/nfl-alvis)  
> URL: https://nfl-alvis.github.io/ctf-writeups/posts/neighbour/  

