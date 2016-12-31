# secret-base32

Randomly generate a secret token using base32 charset i.e. alphanumeric omitting letters `i` and `o` and digits zero and one.

We omit those characters to avoid potential confusion if transcribed by humans e.g. for hand-written backup.

This is suitable for secret URLs, whereas base64 includes slash in its charset.

Also see related project for base56 which uses upper and lowercase: https://github.com/evanx/secret-base56

It is implemented as follows:
```javascript
const assert = require('assert');
const crypto = require('crypto');
const letters24 = 'ABCDEFGHJKLMNPQRSTUVWXYZ'; // exclude I and O since too similar to 0 and 1
const digits = '23456789'; // omit 0 and 1 to avoid potential confusion with O and I (and perhaps 'l')
const charset = [digits, letters24].join('');
assert.equal(charset.length, 32);
const length = parseInt(process.env.length || '16');
const string = crypto.randomBytes(length)
.map(value => charset.charCodeAt(Math.floor(value*charset.length/256)))
.toString();
console.log(string);
```
where we generate an array of random bytes (values 0 to 255 inclusive) of the desired `length` and then map each into our charset:
```
23456789ABCDEFGHJKLMNPQRSTUVWXYZ
```

We can build using its `Dockerfile` as follows:
```
docker build -t secret-base32:test https://github.com/evanx/secret-base32.git
```
where we tag the image so we can run by tag name:
```
docker run -t secret-base32:test 
```
which gives random output e.g. ``

Use `length` envar to change from default `16`
```
docker run -e length=40 secret-base32:test
```
which outputs length `40` token e.g. ``

You can then use this for a secret URL e.g. for a Telegram Bot webhook, or some other purpose.

