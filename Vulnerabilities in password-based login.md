# Lab 1: Vulnerabilities in password-based login

In this lab we are initially given with this much information.
- A website which is vulnerable to username enumeration and password brute-force attacks.
- Candidate usernames list
- Candidate passwords list

  ![img alt](https://github.com/r1tv1kx/portswigger_labs/blob/e26abe68bb50b7e871c6081d5e9d8ec455c85e08/images/Screenshot%202025-05-14%20113905.png)

Here we will try to login to the given website using the lists provided by brute force method.
### Step 1:
Click on "My account"

![img alt](https://github.com/r1tv1kx/portswigger_labs/blob/9bdfa56452e1374194260f0ad1a8d9945750933e/images/2025-05-14_12-15.png)

### Step 2:
Open burp suite and click on Proxy>Intercept and turn the Intercept on.
Now the browser traffic will go through burp suite.

### Step 3:
Now back to the browser. In the login field give random username and password and hit enter.

### Step 4:
Now come in burp suite and notice the request of post method and observe the request.

![img alt](https://github.com/r1tv1kx/portswigger_labs/blob/f70ca2531c61a38ca5779edec6ab065c6944d4f7/images/2025-05-14_12-30.png)

We can see that there is a username and password field in the payload. 

### Step 5:
Right click on the request and send it to the intruder.(Intruder in Burp Suite automates customized attacks by injecting payloads into HTTP requests to test for vulnerabilities like XSS, SQLi, and brute-force weaknesses.)

![img alt](https://github.com/r1tv1kx/portswigger_labs/blob/d00be2f97523b31e9829b50f878b000dc364956b/images/Screenshot%202025-05-14%20124002.png)

### Step 6:
![img alt](https://github.com/r1tv1kx/portswigger_labs/blob/5119d304017c247ed23816845a82c8abeb532d7b/images/dsdfsd0.png)

1. Make sure the attack is set to Sniper Attack(you can read the description of every attack after clicking the drop down menu).
2. Clear the position for double check.
3. Select the $incorrect_username$ and click on add in the position section.
4. From the Candidate usernames list, copy every name from there and paste it in there.

### Step 7:
Click on start attack.

### Step 8:
![img alt](https://github.com/r1tv1kx/portswigger_labs/blob/e1e525582e085181f2b86c73e6f5b775770b4f2a/images/username.png)
Something like this will appear. Observe the results(Length, response)
You will find the correct username.

### Step 9:
Repeat the entire process for password.

There you go!
You successfully cleared this lab.

