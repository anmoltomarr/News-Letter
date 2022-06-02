# DEMO LINK

https://anmoltomar.000webhostapp.com/

*Please check spam, social and promotions folders*

## Project description

This is a simple PHP application that accepts a visitor’s email address and emails them random XKCD comics every five minutes.

## About

1. Created a web page that will take users name and eamil for verification with a subscribe button. There is a simple HTMl validation for email which wont allow the user to pass random garbage data as eamil.

2. Created a database where we will store name, email, verification code, subscribed/not subscribed and verified / not verified data.

3. As soon as the user click subscribe button the name and email information will be recived with post method and then a SQL query will be fired and the user name and email will be stored in the database. At the same time a verification code will be generated (for verification code i have used a function (random_bytes) which generates random bytes and then converted those random bytes into hexadecimal format using another function (bin2hex) ).
We will send verification mail with verification code along with email as a HTML tag to the subscribers email.

4. After clicking the subscribe button on the web page the user will recieve an alert regarding email verification link sent on his email.

5. After that if users clicks the verification link we will check if the verification code and email matches our database data.

### index.php

This will create a signup page that will ask user for name and email and will send it to **subscribe.php** page  via method **post**.

It also includes internal CSS to make the web page look a little good to visiters.


### connection.php 

This is the **connection.php** file in this i have set up the connection with the database where subscriber's name and email will be stored and later on email verification code and verified and subscribed information will be stored.

### subscribe.php

**SAVING EMAIL AND NAME TO DATABASE AFTER SOME CHECKS**

Here,  we will recieve name and email which user submitted (by subscribe button) by post method. Then we will check if the email is valid by **FILTER_VALIDATE_EMAIL**. If the email is not valid then the visiter will be redirected to the subscribe page again otherwise we will check if user already exist in our database. For this we will create a SQL SELECT query and then fire the query which will check if the email already exists in database or not.
If the user exists it will show an alert message at the window  'email already registered'. Otherwise we will save the email and name and verification code (explained below)in our database by firing a SQL INSERT query. And mean while we will show another alert on the window of the user 'Verify your email. Email verification link sent!'.

**FOR SENDING VERIFICATION EMAIL**

Along with INSERT query we will call a **sendmail** function to send email with verification link.

VERIFICATION CODE *We will generate a verification code using **random_bytes** function and then we will convert those random bytes into binary hexadecimal format using **bin2hex** function.*

For email sending, I used **SendGrid** srvice.

I have sent the verification code and email using HTML tag and using get method . By clicking the link the user will be taken to **verify.php**.

### verify.php

If you implement this , it will perform mainly 2 tasks 
1. Verification 
2. Sending the first XKCD comic


**VERIFICATION**

When the user will click on the verification mail sent to his registered email. We will use "$ _ GET" variable to recive the verification code and email which we have sent in the link inside HTML tag. After that we will fire a SQL SELECT query and we will search for the same email and verification code (which we recived in GET variable ) in our database. If the query retur false (that means if the eamil or verifiction code dosent match our database information) we will pass an alert 'Invalid Email or Verification code'.
Else If we find the same email and verification code , we will fire  SQL UPDATE query and will update the verified and subscribed as 1(which was predefined as 0) and will give a alert 'Email verification complete. Check subscription email'. Now the email verification is complete so we will send the user his 1st XKCD comic with a **unsubscribe link**.

**SENDING FIRST XKCD COMIC**

The first step is to scrap the image from XKCD comic website. To scrap the image I am using cURL library that allow us to send and recieve data with URL syntax.

At first I am telling which URL to hit (in our case "https://c.xkcd.com/random/comic/") then i am initialising with "curl_init()" then I am passing curl options like CURLOPT_HEADER ( to pass headers to the data stream), CURLOPT_FOLLOWLOCATION (to follow the last forwarded location after hiting the url) and CURLOPT_RETURNTRANSFER(to fetch the data) and then I executed the curl "curl_exec". Then I saved the json file of XKCD website and after that i used json decode to decode the file then I saved the image from decoded json file.

Now we are sending this comic image as mail using SENDGRID as explained above this time the the content is the random image with unsubscribe link. On clicking unsubscribe the user will be taken to **unsubscribe.php**.

### unsubscribe.php


If you implement this , it will update the subscriber data as unsubscribed.

The unsubscribe link which we are sending below the XKCD comic image is a html tag which has verification code and email of the user. When the user clicks on this link user is directed to unsubscribe.php.

Now we use "$ _ GET" variable to recive the verification code and email after that we fire a SQL SELECT query to select the user using email and verification code.If we receive a row then we fire SQL UPDATE query to update the subscribed data with 0 (which here means unsubscribed) and the user receives a alert 'You have been unsubscribed'. 


### cronjob.php

To send eamil every 5 minutes we will have to set up cron jobs which will perform the directed task after every 5 minutes.

If you implement this, it will perform 2 tasks 

1. It will scrap a random image from XKCD comic website
2. It will send the image as email along with unsubscribe link.


To scrap image and send it with unsubscribe link we are doing the same thing as done in verify.php.

Here the unsubscribe link is a HTML tag which is sending email and verification code by get method. On clicking this the the user will we directed to unsubscribe.php (explained above).

