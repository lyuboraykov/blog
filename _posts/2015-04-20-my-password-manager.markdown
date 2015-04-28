---
layout: post
title:  "My Password Manager"
date:   2015-04-14:22/l05
categories: shell
---

Password managers seem to be an area where too little effort has been put in.
Maybe people don't want to trust all of their data to a single piece of software or they use the same password for all places.
But the case is the same - I haven't found an application which suits my needs when it comes to private data storage.

> **Note:**

>I forgot to mention that I might be strange and everybody else could be happy with their managers.

Nevertheless here's how I solved my problem.

###42

I had some requirements to the manager so the first thing to do was to list them:

* I like sitting in the shell so it should be accessible from there
* I'm very bad with UI so no sense in going there
* It has to accept all kinds of data - passwords, credit card numbers etc.

It has to be quick to type and knows everything so I named it *42*

Here it is:

{% highlight bash %}
# Check if I haven't forgotten to encrypt 42
if [[ -f ~/42.plain ]]; then
  echo "WARNING: 42 is unencrypted!"
fi

42() {
  if [[ -f ~/42.enc ]]; then
    decrypt_42
  else
    encrypt_42
  fi
}

encrypt_42() {
  local encryptOutput=$(openssl aes-256-cbc -in ~/42.plain -out ~/42.enc 2>&1)
  if [[ $encryptOutput == *"bad password read"* ]]; then
    echo "The passwords don't match."
    rm -f ~/42.enc
  else
    rm -f ~/42.plain
  fi
}

decrypt_42() {
  local decryptOutput=$(openssl aes-256-cbc -d -in ~/42.enc -out ~/42.plain 2>&1)
  # Don't delete the encoded file if the password is wrong
  if [[ $decryptOutput == *"bad decrypt"* ]]; then
    echo "The decryption password is invalid."
    rm -f ~/42.plain
  else
    rm -f ~/42.enc
  fi
}
{% endhighlight %}

I just created a text file called 42.plain and encode/decode it with AES whenever I type 42 in the shell.
That's it, there are some validations for the validity of the password because I wouldn't want everything deleted on a typo.

###What can it do

Well, nothing really. It just encrypts a text file.
But in the end, do you need anything more?
