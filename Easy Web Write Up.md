# **Easy Web** Write Up

This is a challenge from my school website about web penetration testing

## Web First Look:

![image-20240823233654124](images/1.png)

When I press the button, it transfer me to 1.php

![image-20240823233721805](images/2.png)

inspecting the html code, I found an odd comment line

![image-20240823233721805](images/3.png)
talkingg about .bak file, .bak file is a back up file format, so my next step would be to use the button on the previous page and use the button to acess the 1.php.bak file

![image-20240823233721805](images/4.png)

simply replace 1.php to 1.php.bak 

![image-20240823233721805](images/5.png)

and a back up file is found, and directly downloaded

![image-20240823233721805](images/6.png)

after seing **the2nd.php** lets access it via the button on the main page again

![image-20240823233721805](images/7.png)

Now we are at the level 2, when we submit anything from the form the response is always the same

![image-20240823233721805](images/8.png)

So finally I checked the network tab to see any **suspicious** payload activity

![image-20240823233721805](images/9.png)

In the referer I found the 3rd.php file appeared, let's access that via the home page button again

![image-20240823233721805](images/10.png)

We are now at the 3rd level of the challenge, let's press the button, 

![image-20240823233721805](images/11.png)

Nothing suspicious going around, Let's check the network tab

![image-20240823233721805](images/12.png)

Yeah, there is one more php page going around (wozaizheli.php)

![image-20240823233721805](images/13.png)

now it ask us to press the button that dissapeared, well one way to brute force this is by changing the html code

![image-20240823233721805](images/14.png)

here by changing the id to something else, the script won't effect the div block so the button won't disappear

![image-20240823233721805](images/15.png)

changing the idea worked and now we can just press the button to obtain the flag

![image-20240823233721805](images/16.png)

flag : AAA{y0u_2a_g0od_front-end_Web_developer}
