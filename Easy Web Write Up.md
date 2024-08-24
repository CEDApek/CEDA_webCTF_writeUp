# **Easy Web** Write Up

This is a challenge from my school website about web penetration testing

## Web First Look:

![image-20240823233654124](images/1.png)

When I press the button, it transfer me to 1.php

![image-20240823233721805](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20240823233721805.png)

inspecting the html code, I found an odd comment line

<img src="C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20240823233753969.png" alt="image-20240823233753969" style="zoom: 50%;" />

talkingg about .bak file, .bak file is a back up file format, so my next step would be to use the button on the previous page and use the button to acess the 1.php.bak file

<img src="C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20240823233937326.png" alt="image-20240823233937326" style="zoom:50%;" />

simply replace 1.php to 1.php.bak 

![image-20240823234014815](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20240823234014815.png)

and a back up file is found, and directly downloaded

<img src="C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20240823234637479.png" alt="image-20240823234637479" style="zoom:50%;" />

after seing **the2nd.php** lets access it via the button on the main page again

<img src="C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20240823234757540.png" alt="image-20240823234757540" style="zoom:45%;" />

Now we are at the level 2, when we submit anything from the form the response is always the same

<img src="C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20240823234907821.png" alt="image-20240823234907821" style="zoom:50%;" />

So finally I checked the network tab to see any **suspicious** payload activity

![image-20240823235004976](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20240823235004976.png)

In the referer I found the 3rd.php file appeared, let's access that via the home page button again

<img src="C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20240823235154926.png" alt="image-20240823235154926" style="zoom:50%;" />

We are now at the 3rd level of the challenge, let's press the button, 

<img src="C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20240823235230327.png" alt="image-20240823235230327" style="zoom:50%;" />

Nothing suspicious going around, Let's check the network tab

![image-20240823235324429](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20240823235324429.png)

Yeah, there is one more php page going around (wozaizheli.php)

![](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20240824000037806.png)

now it ask us to press the button that dissapeared, well one way to brute force this is by changing the html code

<img src="C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20240823235516178.png" alt="image-20240823235516178" style="zoom:50%;" />

here by changing the id to something else, the script won't effect the div block so the button won't disappear

![image-20240824000141205](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20240824000141205.png)

changing the idea worked and now we can just press the button to obtain the flag

![image-20240824000208896](C:\Users\ASUS\AppData\Roaming\Typora\typora-user-images\image-20240824000208896.png)

flag : AAA{y0u_2a_g0od_front-end_Web_developer}
