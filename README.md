

# diffiejs
Diffie Hellman implementation in JavaScript

# My Explanation
## Pre-Story
Imagine two people are in a public place and they want to talk. But if they talk other people will eavesdrop.  
So they decide to write it down and share.  
But let's assume they are far apart so they have to pass the paper between a person in between. (That is our server)  
So during the passing process, he can easily read it.   

## The method - but using addition.  
So they mutually agree on a base number. During the agreeing process, they had to pass to the intermediate person so it's public  
Let's say it is 20.  
Now first person creates his own secret number and so does second person, (they wont share this)  
Let secrete number of person A be 8 and that of B be 15   
Now first person will add his secret number to base nubmer so he get sA = 28 (sum of A) and so does second person so he gets sB = 35
Now they share it, so 28 and 35 are public. (I know there is a major flaw here, but stick along)
Now person A has 35 and person B has 28. Now they add their corresponding secret nubmers to this.
So person A has 35+8 = 43 and person B has 28 + 15 = 43.
Both has a common secret key which they can use for securing their conversations.

But there's a big flaw, when they exchange 28 and 35 the eaves droppers can guess their secret numbers since base number was already public.

## Diffie Hellman
Who said we have to use addition? 
Instead of doing base + secretA + secret B = final shared secret key.
They agree mutually on a base b and modulus value m. (these are public)
Let secret number of A and B be x and y
Then they do this >> (b^x)%m and (b^y)%m
Now they share this. The eaves droppers cannot find the value of x and y , this is not straightforward like our addition.
When m is large and some other criterions are set on a,b,m,x and y, finding x and y in this process will be nearly impossible.

To finish it off , A will use the now received value (b^y)%m = say X
now A will perform (X^a)%m=SA.
B will perform (Y^b)%m = SB.

Mathematically one can show that SA = SB.
Hence they both have a common secret key without ever revealing it to the public.
That is Diffie Hellman

You may see this with an animation in this video here https://youtu.be/85oMrKd8afY?t=110

## A bit about the Diffie–Hellman key exchange protocol (From the forked repo, not my explanation)
Suppose you're a person that wants to send a message to your friend, or a secure bank server that wants to send a message to your client. However, you're stuck with a government monitored, unsecure internet connection. How would you make sure that your message goes across without the Rothschild family reading your bank statement? Well, the answer is pretty easy: encryption. So suppose we use the standard AES, Rijndael cipher,  this is all good and all, but since this is symmetric encryption you and your partner need to share a common key. Well, that poses another problem, how do you get a key across to your friend without being sniffed? You could use RSA, but no one wants to waste their time with RSA libraries, extra prime number generation, and blah blah blah. Instead we use the Diffie–Hellman key exchange protocol.  
<br>
Now Diffie–Hellman is not a protocol that enables you to transfer a key across securely, rather, it allows you and the person you're trying to communicate with to generate a unique key that only you and they will know. This is as good if not better, than generating a key and transferring it via asymmetric encryption.  
<br>
So, is Diffie–Hellman very math heavy? Nope! Diffie–Hellman is probably easier to understand than RSA. The only thing you need to know is how to calculate modulus' and exponents.
<br>
### 4 steps of Diffie–Hellman
#### Step 1: Choosing some common parameters
Well, to begin, you and your partner need to agree upon a common base and modulus, let's call them g and p, respectively. It's very important that p is prime and that g is less than p. You can send these numbers to each other over an unsecure channel as they are public knowledge.  

Although it's easy to implement on your own I made it a bit easier to do this using the diffiejs library
```javascript
//Share this string with your friend
var commonNums = diffie.gen();
// Some code where you send the above string to your friend
// Some function where you recieve a similar string from your friend

// This callback is called when you get your friend's data
function onFriendString(data){
  diffie.update(data);
}
```
All the above code does, is generate a key file and then once the partner's keyfile is recieved, it sees which is bigger and adjusts accordingly

#### Step 2: Keeping your secret
Now you and your friend have to generate secret keys. Note: THIS IS YOUR PRIVATE KEY, YOU NEVER EVER SHARE THIS.
Any information of the key should not leave your computer. The diffiejs library does this automatically on construction. Also, for the sake of documentation we'll refer to this as S.

#### Step 3: Making a public shared key
So, here's where a bit of the math begins. You take your base, g, and raise it to the power of secret number, s. Then, you take the modulus of this and modulus number we talked about earlier, g. This is an operation known as modular exponentiation. Since these numbers are so large diffiejs uses the bigjs Big Integer library to do this operation. In common math notation it can be written in JS as the following:

```javascript
  var yourPublicKey = Math.pow(g,s) % p;
```

You can now send this to your friend, over the unsecure channel as this is public knowledge as well. Your friend will send this to you as well.
#### Step 4: Recieving a public key, and generating the final secure key.
So, let's suppose your friend sent you their public key call it P. Now you do a modular exponentation, only this time using P as your base instead of g. This new number is your secret key. Your friend will have the exact same key This can be written in JS math as:

```javascript
  var secretKey = Math.pow(P,s) % p;
  // When you expand it out on your side it looks like this
  Math.pow(((Math.pow(g,friendSecret)) % p),s) % p;
  // Your friend will calculate:
  Math.pow(((Math.pow(g,s)) % p),friendSecret) % p;
  // Thanks to the nature of modular arithmetic, these 2 numbers are the exact same
  Math.pow(((Math.pow(g,friendSecret)) % p),s) % p == Math.pow(((Math.pow(g,s)) % p),friendSecret) % p
  // ^this is true!!
```

The diffiejs library was onestep ahead of you and already computed it, when you did the .update() in Step 3. To access it, just check out the key attribute
```javascript
var secretKey = diffie.key //This will give you your secret shared key 
```
For more details check out the Wikipedia article on it: https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange


## Elliptic Curve Diffie-Hellman

Although the previous, vanilla, version of Diffie Hellman

##How to use diffie.js
