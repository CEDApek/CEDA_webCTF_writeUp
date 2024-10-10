# **Dangerous Flask** Write Up

**Flask** is a lightweight and flexible web framework for building web applications in **Python**. It is designed to be simple and easy to get started with, while also being powerful enough to scale up to more complex applications.

**Development Mode (Debug Mode)**, Flask has a `Debug=True` setting that provides detailed error messages when something goes wrong. It is useful in development but dangerous in production because it exposes internal application information to the public.

One of the `main focus` in flask is when the Debug mode is set to True :

## Web First Look:

![image-20241010231450283](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20241010231450283.png)

We can do an exploit from the button, after interrupting the post request method:

![63](C:\Users\ASUS\Desktop\Write Ups\images\63.png)

I tried to trigger an error by fuzzing the "setsuna" value (via intruder on BurpSuite).

![64](C:\Users\ASUS\Desktop\Write Ups\images\64.png)

after further fuzzing some payload triggers an error, here we can see the error response from the server.

And from the last line it also confirms that the `?__debugger__=yes` meaning debug is set to True. After scrolling down to the bottom of the error response, we receive the flag at the bottom of the error response:

![65](C:\Users\ASUS\Desktop\Write Ups\images\65.png)

**Flag : AAA{haruki_SH4b1_qq_qun_~~}**  ~~ for privacy purpose
