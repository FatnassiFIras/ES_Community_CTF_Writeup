# ES_Community_CTF_Writeups

 
## I- Challenge name: Web 1
- This challenge contains a link that takes you to http://pentestbox.ml:8500/can_y#u_get_the_flag?! but nothing appears there.
 ---> It turns out that everything after "#" is used as a fragment. So, Here we need to URL-Encode "#" (%23) to make it a part of the path. Still there is a "?" which indicates  the start of parameters as well to make it part of the path, same thing is done we only need to URL-Encode it. 

- Final request: curl "http://pentestbox.ml:8500/can_y%23u_get_the_flag%3f!
- Flag: Sp4rk{UwU-UwU-noob}

## II- Challenge name: Backup files
- Here if you either bruteforce or read thoroughly the description you will find that there is a directory named "/admin" where directory listing is open but there is nothing there. However, there is something called hidden files.

- A hidden file is a file which has the hidden attribute turned on so that it is not visible to users when exploring or listing files.

- Now, we know that the file is hidden, named "flag" (From the description of the task) and it is a backup file so the extension would be .bak. 

So, our final request would look like this: curl "http://pentestbox.ml:8500/admin/.flag.bak"

- The period (.) at the beginning of the new filename indicates that it’s hidden.

Flag: Sp4rk{Directory_listing_Spark_99}

## III- Challenge name: Just War Theory (JWT)

- Opening the link you will find a login form if you enter random credentials and hit the login button the app will return valid login credentials in the response. Since they are valid if you use them the app will return a valid JWT token. 

- So, What can we do here !? There was a wordlist attached with the challenge description. Clearly, we're gonna use that wordlist to crack the JWT and find the secret used to sign the token.

- Here I, used JohnTheRipper to crack the token with the following command: 

`john jwt.txt --wordlist=Most-Popular-Letter-Passes.txt --format=HMAC-SHA256`

- Where the john is the command name, jwt.txt is a text file where we put the token returned after loggin in with the valid credentials, --wordlist=Most-Popular-Letter-Passes.txt is the wordlist given to us by the author and --format=HMAC-SHA256 is the algorithm used to generate the signature.

- The tool will return that the secret is SparkLoveYou. Now, we do have the secret we can modify the information in the payload header and sign the token with the secret. This can be done either with a python script or using the jwt.io website.

- Python script:
```
import jwt
encoded_jwt = jwt.encode({"auth": 1639697090031, "agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/96.0.4664.110 Safari/537.36", "role": "admin", "iat": 1639697090}, "SparkLoveYou", algorithm="HS256")
print(encoded_jwt)
```

- Obtained JWT:
`eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJhdXRoIjoxNjM5Njk3MDkwMDMxLCJhZ2VudCI6Ik1vemlsbGEvNS4wIChXaW5kb3dzIE5UIDEwLjA7IFdpbjY0OyB4NjQpIEFwcGxlV2ViS2l0LzUzNy4zNiAoS0hUTUwsIGxpa2UgR2Vja28pIENocm9tZS85Ni4wLjQ2NjQuMTEwIFNhZmFyaS81MzcuMzYiLCJyb2xlIjoiYWRtaW4iLCJpYXQiOjE2Mzk2OTcwOTB9.nmECW7V1TWIF3Fx8_9MCUzpLkWNWWthzlBOZ4asq650`

- To do the exact same thing done with the script: Open https://jwt.io/ put the token there, replace `user` with `admin` and put the secret in the verify signature section.

- Finally, if you send the forged token in the cookie named `token` the app will return the flag.

Flag: sp4rk{JwT_ARe_Not_Bad}c7f
