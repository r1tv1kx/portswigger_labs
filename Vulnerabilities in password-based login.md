# Vulnerabilities in password-based login

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
Right click on the request and send it to the intruder

